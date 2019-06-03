---
title: Java并发编程入门与高并发面试（三）：线程安全性-原子性-CAS（CAS的ABA问题）
categories:
  - 高并发
abbrlink: 922ce624
date: 2018-09-14 22:30:08
tags:
---

摘要：本文介绍线程的安全性，原子性，java.lang.Number包下的类与CAS操作，synchronized锁，和原子性操作各方法间的对比。

<!--more-->

------

线程安全性

- - 线程安全？
  - 线程安全性？
  - 原子性
    - Atomic包中的类与CAS：
      - AtomicInteger
      - AtomicLong 与 LongAdder
      - AtomicBoolean
      - AtomicIntegerFieldUpdater
      - AtomicStampReference与CAS的ABA问题
      - AtomicLongArray
    - synchronized
      - synchronized 修饰一个代码块
      - synchronized 修饰一个方法
      - synchronized 修饰一个静态方法
      - synchronized 修饰一个类
    - 原子性操作各方法间的对比

### 线程安全？

当多个线程访问某个类时，不管运行时环境采用**何种调度方式**或者这些进程将如何交替执行，并且在主调代码中**不需要任何额外的同步或协同**，这个类都能表现出**正确的行为**，那么就称这个类是线程安全的。

### 线程安全性？

线程安全性主要体现在三个方面：原子性、可见性、有序性

- 原子性:提供了**互斥访问**，同一时刻只能有一个线程来对它进行操作
- 可见性:一个线程对主内存的修改可以及时的被其他线程观察到
- 有序性:一个线程观察其他线程中的指令执行顺序，由于指令重排序的存在，该观察结果一般杂乱无序。

> 基础代码：以下代码用于描述下方的知识点，所有代码均在此代码基础上进行修改。

```
public class CountExample {

    //请求总数
    public static int clientTotal  = 5000;
    //同时并发执行的线程数
    public static int threadTotal = 200;
    //变量声明：计数
    public static AtomicInteger count = new AtomicInteger(0);

    public static void main(String[] args) throws InterruptedException {
        ExecutorService executorService = Executors.newCachedThreadPool();//创建线程池
        final Semaphore semaphore = new Semaphore(threadTotal);//定义信号量，给出允许并发的数目
        final CountDownLatch countDownLatch = new CountDownLatch(clientTotal);//定义计数器闭锁
        for (int i = 0;i<clientTotal;i++){
            executorService.execute(()->{
                try {
                    semaphore.acquire();//判断进程是否允许被执行
                    add();
                    semaphore.release();//释放进程
                } catch (InterruptedException e) {
                    log.error("excption",e);
                }
                countDownLatch.countDown();
            });
        }
        countDownLatch.await();//保证信号量减为0
        executorService.shutdown();//关闭线程池
        log.info("count:{}",count.get());//变量取值
    }

    private static void add(){
        count.incrementAndGet();//变量操作
    }
}12345678910111213141516171819202122232425262728293031323334
```

### 原子性

说到原子性，一共有两个方面需要学习一下，一个是JDK中已经提供好的Atomic包，他们均使用了CAS完成线程的原子性操作，另一个是使用锁的机制来处理线程之间的原子性。锁包括：synchronized、Lock

#### Atomic包中的类与CAS：

![这里写图片描述](https://img-blog.csdn.net/20180407184050321?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
我们从最简单的AtomicInteger类来了解什么是CAS

##### AtomicInteger

上边的示例代码就是通过AtomicInteger类保证了线程的原子性。 
那么它是如何保证原子性的呢？我们接下来分析一下它的源码。示例中，对count变量的+1操作，采用的是incrementAndGet方法，此方法的源码中调用了一个名为unsafe.getAndAddInt的方法

```
public final int incrementAndGet() {
     return unsafe.getAndAddInt(this, valueOffset, 1) + 1;
}123
```

而getAndAddInt方法的具体实现为：

```
public final int getAndAddInt(Object var1, long var2, int var4) {
    int var5;
    do {
        var5 = this.getIntVolatile(var1, var2);
    } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));
    return var5;
}1234567
```

在此方法中，方法参数为要操作的对象Object var1、期望底层当前的数值为var2、要修改的数值var4。定义的var5为真正从底层取出来的值。采用do..while循环的方式去获取底层数值并与期望值进行比较，比较成功才将值进行修改。而这个比较再进行修改的方法就是compareAndSwapInt就是我们所说的**CAS**，它是一系列的接口，比如下面罗列的几个接口。使用**native修饰**，是底层的方法。CAS取的是compareAndSwap三个单词的首字母.

另外，示例代码中的count可以理解为JMM中的**工作内存**，而这里的底层数值即为**主内存**，如果看过我上一篇文章的盆友就能把这一块的知识点串联起来了。

```
public final native boolean compareAndSwapObject(Object var1, long var2, Object var4, Object var5);
public final native boolean compareAndSwapInt(Object var1, long var2, int var4, int var5);
public final native boolean compareAndSwapLong(Object var1, long var2, long var4, long var6);123
```

##### AtomicLong 与 LongAdder

LongAdder是java8为我们提供的新的类，跟AtomicLong有相同的效果。首先看一下代码实现：

```
AtomicLong：
//变量声明
public static AtomicLong count = new AtomicLong(0);
//变量操作
count.incrementAndGet();
//变量取值
count.get();1234567
LongAdder：
//变量声明
public static LongAdder count = new LongAdder();
//变量操作
count.increment();
//变量取值
count1234567
```

那么问题来了，为什么有了AtomicLong还要新增一个LongAdder呢？ 
原因是：CAS底层实现是在一个死循环中不断地尝试修改目标值，直到修改成功。如果竞争不激烈的时候，修改成功率很高，否则失败率很高。在失败的时候，这些重复的原子性操作会耗费性能。

> 知识点： 对于普通类型的long、double变量，JVM允许将64位的读操作或写操作拆成两个32位的操作。

LongAdder类的实现核心是将热点数据分离，比如说它可以将AtomicLong内部的内部核心数据value分离成一个数组，每个线程访问时，通过hash等算法映射到其中一个数字进行计数，而最终的计数结果则为这个数组的求和累加，其中热点数据value会被分离成多个单元的cell，每个cell独自维护内部的值。当前对象的实际值由所有的cell累计合成，这样热点就进行了有效地分离，并提高了并行度。这相当于将AtomicLong的单点的更新压力分担到各个节点上。在低并发的时候通过对base的直接更新，可以保障和AtomicLong的性能基本一致。而在高并发的时候通过分散提高了性能。

```
源码：
public void increment() {
    add(1L);
}
public void add(long x) {
    Cell[] as; long b, v; int m; Cell a;
    if ((as = cells) != null || !casBase(b = base, b + x)) {
        boolean uncontended = true;
        if (as == null || (m = as.length - 1) < 0 ||
            (a = as[getProbe() & m]) == null ||
            !(uncontended = a.cas(v = a.value, v + x)))
            longAccumulate(x, null, uncontended);
    }
}1234567891011121314
```

缺点：如果在统计的时候，如果有并发更新，可能会有统计数据有误差。实际使用中在处理高并发计数的时候优先使用LongAdder，而不是AtomicLong在线程竞争很低的时候，使用AtomicLong会简单效率更高一些。比如序列号生成（准确性）

##### AtomicBoolean

这个类中值得一提的是它包含了一个名为compareAndSet的方法，这个方法可以做到的是控制一个boolean变量在一件事情执行之前为false，事情执行之后变为true。或者也可以理解为可以控制某一件事只让一个线程执行，并仅能执行一次。 
他的源码如下：

```
public final boolean compareAndSet(boolean expect, boolean update) {
    int e = expect ? 1 : 0;
    int u = update ? 1 : 0;
    return unsafe.compareAndSwapInt(this, valueOffset, e, u);
}12345
```

举例说明：

```
    //是否发生过
    private static AtomicBoolean isHappened = new AtomicBoolean(false);
    // 请求总数
    public static int clientTotal = 5000;
    // 同时并发执行的线程数
    public static int threadTotal = 200;

    public static void main(String[] args) throws Exception {
        ExecutorService executorService = Executors.newCachedThreadPool();
        final Semaphore semaphore = new Semaphore(threadTotal);
        final CountDownLatch countDownLatch = new CountDownLatch(clientTotal);
        for (int i = 0; i < clientTotal ; i++) {
            executorService.execute(() -> {
                try {
                    semaphore.acquire();
                    test();
                    semaphore.release();
                } catch (Exception e) {
                    log.error("exception", e);
                }
                countDownLatch.countDown();
            });
        }
        countDownLatch.await();
        executorService.shutdown();
        log.info("isHappened:{}", isHappened.get());
    }

    private static void test() {
        if (isHappened.compareAndSet(false, true)) {//控制某有一段代码只执行一次
            log.info("execute");
        }
    }

结果：(log只打印一次)
[pool-1-thread-2] INFO com.superboys.concurrency.example.Atomic.AtomicExample6 - execute
[main] INFO com.superboys.concurrency.example.Atomic.AtomicExample6 - isHappened:true12345678910111213141516171819202122232425262728293031323334353637
```

##### AtomicIntegerFieldUpdater

这个类的核心作用是要更新一个指定的类的某一个字段的值。并且这个字段一定要用volatile修饰同时还不能是static的。 
举例说明：

```
@Slf4j
public class AtomicExample5 {

    //原子性更新某一个类的一个实例
    private static AtomicIntegerFieldUpdater<AtomicExample5> updater
            = AtomicIntegerFieldUpdater.newUpdater(AtomicExample5.class,"count");

    @Getter
    public volatile int count = 100;//必须要volatile标记，且不能是static

    public static void main(String[] args) {
        AtomicExample5 example5 = new AtomicExample5();

        if(updater.compareAndSet(example5,100,120)){
            log.info("update success 1,{}",example5.getCount());
        }

        if(updater.compareAndSet(example5,100,120)){
            log.info("update success 2,{}",example5.getCount());
        }else{
            log.info("update failed,{}",example5.getCount());
        }
    }
}

此方法输出的结果为：
[main] INFO com.superboys.concurrency.example.Atomic.AtomicExample5 - update success 1,120
[main] INFO com.superboys.concurrency.example.Atomic.AtomicExample5 - update failed,120

由此可见，count的值只修改了一次。123456789101112131415161718192021222324252627282930
```

##### AtomicStampReference与CAS的ABA问题

什么是ABA问题？ 
CAS操作的时候，其他线程将变量的值A改成了B，但是随后又改成了A，本线程在CAS方法中使用期望值A与当前变量进行比较的时候，发现变量的值未发生改变，于是CAS就将变量的值进行了交换操作。但是实际上变量的值已经被其他的变量改变过，这与设计思想是不符合的。所以就有了AtomicStampReference。

```
源码：
private static class Pair<T> {
        final T reference;
        final int stamp;
        private Pair(T reference, int stamp) {
            this.reference = reference;
            this.stamp = stamp;
        }
        static <T> Pair<T> of(T reference, int stamp) {
            return new Pair<T>(reference, stamp);
        }
    }

private volatile Pair<V> pair;

private boolean casPair(Pair<V> cmp, Pair<V> val) {
        return UNSAFE.compareAndSwapObject(this, pairOffset, cmp, val);
    }

public boolean compareAndSet(V   expectedReference,
                                 V   newReference,
                                 int expectedStamp,
                                 int newStamp) {
        Pair<V> current = pair;
        return
            expectedReference == current.reference &&
            expectedStamp == current.stamp &&
            ((newReference == current.reference &&   //排除新的引用和新的版本号与底层的值相同的情况
              newStamp == current.stamp) ||
             casPair(current, Pair.of(newReference, newStamp)));
}12345678910111213141516171819202122232425262728293031
```

AtomicStampReference的处理思想是，每次变量更新的时候，将变量的版本号+1，之前的ABA问题中，变量经过两次操作以后，变量的版本号就会由1变成3，也就是说只要线程对变量进行过操作，变量的版本号就会发生更改。从而解决了ABA问题。

解释一下上边的源码： 
类中维护了一个volatile修饰的Pair类型变量current，Pair是一个私有的静态类，current可以理解为底层数值。 
compareAndSet方法的参数部分分别为期望的引用、新的引用、期望的版本号、新的版本号。 
return的逻辑为判断了期望的引用和版本号是否与底层的引用和版本号相符，并且排除了新的引用和新的版本号与底层的值相同的情况（即不需要修改）的情况（return代码部分3、4行）。条件成立，执行casPair方法，调用CAS操作。

##### AtomicLongArray

这个类实际上维护了一个Array数组，我们在对数值进行更新的时候，会多一个索引值让我们更新。

> 原子性，提供了互斥访问，同一时刻只能有一个线程来对它进行操作。那么在java里，保证同一时刻只有一个线程对它进行操作的，除了Atomic包之外，还有锁的机制。JDK提供锁主要分为两种：synchronized和Lock。接下来我们了解一下synchronized。

#### synchronized

依赖于JVM去实现锁，因此在这个关键字作用对象的作用范围内，都是同一时刻只能有一个线程对其进行操作的。 
synchronized是java中的一个关键字，是一种同步锁。它可以修饰的对象主要有四种：

- 修饰代码块:大括号括起来的代码，作用于**调用的对象**
- 修饰方法：整个方法，作用于**调用的对象**
- ———————————————————————–
- 修饰静态方法：整个静态方法，作用于**所有对象**
- 修饰类：括号括起来的部分，作用于**所有对象**

##### synchronized 修饰一个代码块

被修饰的代码称为同步语句块，作用的范围是大括号括起来的部分。作用的对象是**调用这段代码的对象**。 
验证：

```
public class SynchronizedExample {
    public void test(int j){
        synchronized (this){
            for (int i = 0; i < 10; i++) {
                log.info("test - {} - {}",j,i);
            }
        }
    }
    //使用线程池方法进行测试：
    public static void main(String[] args) {
        SynchronizedExample example1 = new SynchronizedExample();
        SynchronizedExample example2 = new SynchronizedExample();
        ExecutorService executorService = Executors.newCachedThreadPool();
        executorService.execute(()-> example1.test(1));
        executorService.execute(()-> example2.test(2));
    }
}1234567891011121314151617
```

结果：不同对象之间的操作互不影响 
![这里写图片描述](https://img-blog.csdn.net/20180408093154905?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

##### synchronized 修饰一个方法

被修饰的方法称为同步方法，作用的范围是大括号括起来的部分，作用的对象是**调用这段代码的对象**。 
验证：

```
public class SynchronizedExample 
    public synchronized void test(int j){
        for (int i = 0; i < 10; i++) {
            log.info("test - {} - {}",j,i);
        }
    }
    //验证方法与上面相同
    ...
}123456789
```

结果：不同对象之间的操作互不影响 
![这里写图片描述](https://img-blog.csdn.net/20180408093507839?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

> TIPS： 
> 如果当前类是一个父类，子类调用父类的被synchronized修饰的方法，不会携带synchronized属性，因为synchronized不属于方法声明的一部分

##### synchronized 修饰一个静态方法

作用的范围是synchronized 大括号括起来的部分，作用的对象是**这个类的所有对象**。 
验证：

```
public class SynchronizedExample{
    public static synchronized void test(int j){
        for (int i = 0; i < 10; i++) {
            log.info("test - {} - {}",j,i);
        }
    }
    //验证方法与上面相同
    ...
}123456789
```

结果：同一时间只有一个线程可以执行 
![这里写图片描述](https://img-blog.csdn.net/20180408094528467?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

##### synchronized 修饰一个类

验证：

```
public class SynchronizedExample{
    public static void test(int j){
        synchronized (SynchronizedExample.class){
            for (int i = 0; i < 10; i++) {
                log.info("test - {}-{}",j,i);
            }
        }
    }
    //验证方法与上面相同
    ...
}1234567891011
```

结果：同一时间只有一个线程可以执行 
![这里写图片描述](https://img-blog.csdn.net/20180408100038341?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### 原子性操作各方法间的对比

- synchronized:不可中断锁，适合竞争不激烈，可读性好
- Lock：可中断锁，多样化同步，竞争激烈时能维持常态
- Atomic:竞争激烈时能维持常态，比Lock性能好，每次只能同步一个值

经作者授权转载，原文地址：https://blog.csdn.net/jesonjoke/article/details/79837508
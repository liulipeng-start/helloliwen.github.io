---
title: Java并发编程入门与高并发面试（四）：线程安全性-可见性-有序性
categories:
  - 高并发
abbrlink: 4d4cd374
date: 2018-09-14 22:30:10
tags:
---

摘要：本文介绍可见性及保证可见性的synchronized和volatile，有序性及如何保证有序性。

<!--more-->

------

### 可见性

#### 什么是可见性？

> 一个线程对主内存的修改可以及时的被其他线程观察到

#### 导致共享变量在线程间不可见的原因

- 线程交叉执行

- 重排序结合线程交叉执行

- 共享变量更新后的值没有在工作内存与主存间及时更新


#### JVM处理可见性

JVM对于可见性，提供了synchronized和volatile

JMM关于synchronized的两条规定：

- 线程解锁前，必须把共享变量的最新值刷新到主内存
- 线程加锁时，将清空工作内存中共享变量的值，从而使用共享变量时需要从主内存中重新读取最新的值（注意：**加锁与解锁是同一把锁**）

Volatile:通过加入**内存屏障**和**禁止重排序**优化来实现

- 对volatile变量写操作时，会在写操作后加入一条store屏障指令，将本地内存中的共享变量值刷新到主内存。

![这里写图片描述](https://img-blog.csdn.net/20180408202445295?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

- 对volatile变量读操作时，会在读操作前加入一条load屏障指令，从主内存中读取共享变量。

![这里写图片描述](https://img-blog.csdn.net/20180408202543387?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

- volatile的屏障操作都是cpu级别的。
- 适合状态验证，不适合累加值，volatile关键字不具有原子性 
  举个例子：我们仍用[高并发学习（二）](https://blog.csdn.net/jesonjoke/article/details/79811336)中的例子来说明，对一个int型数值的多线程读写操作。我们将count变量用volatile来修饰：

```
public class CountExample {

    //请求总数
    public static int clientTotal  = 5000;
    //同时并发执行的线程数
    public static int threadTotal = 200;
    //计数 *
    public static volatile int count = 0;

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
        log.info("count:{}",count);
    }

    private static void add(){
        count++;
    }
}12345678910111213141516171819202122232425262728293031323334
```

多次运行代码我们发现：count的最终结果并不是预期的5000，而是有时为5000，但是大多数时间比5000小，这是为什么呢？原因在于对count++的操作中，jvm对count做了三步操作：

> 1、从主存中取出count的值放入工作变量 count 
> 2、对工作变量中的count进行+1 
> 3、将工作变量中的count刷新回主存中

在单线程执行此操作绝对没有问题，但是在多线程环境中，假设有两个线程A、B同时执行count++操作，某一刻A与B同时读取主存中count的值，然后在自己线程对应的工作空间中对count+1，最后又同时将count+1的值写回主存。到此，count+1的值被写回主存两遍，所以导致最终的count值小了1。在整体程序执行过程中，该事件发生一次或多次，自然结果就不正确。 
那么volatile适合做什么呢？其实它比较适合做状态标记量（不会涉及到多线程同时读写的操作），而且要保证两点： 
（1）对变量的写操作不依赖于当前值 
（2）该变量没有包含在具有其他变量的不变的式子中 
例如：

```
volatile boolean inited = false;
//线程一：
context = loadContext();
inited = true;

//线程二：
while（!inited）{
    sleep();
}
doSomethingWithConfig(context);12345678910
```

### 有序性

#### 什么是有序性？

Java内存模型中，允许编译器和处理器对指令进行**重排序**，但是重排序过程不会影响到**单线程**程序的执行，却会影响到多线程并发执行的正确性。 
关于重排序，详情见：[高并发学习（二）– 4、乱序执行优化](https://blog.csdn.net/jesonjoke/article/details/79811336)

#### java中保证有序性

java提供了 volatile、synchronized、Lock可以用来保证有序性 
另外，java内存模型具备一些先天的有序性，即不需要任何手段就能得到保证的有序性。通常被我们成为happens-before原则（先行发生原则）。如果两个线程的执行顺序无法从happens-before原则推导出来，那么就不能保证它们的有序性，虚拟机就可以对它们进行重排序。

【以下规则来自于《深入理解java虚拟机》】

- 程序次序规则：一个线程内，按照代码顺序，书写在前面的操作先行发生于书写在后面的操作
- 锁定规则：一个unlock操作先行发生于后面对同一个锁的lock操作
- volatile变量规则：对一个变量的写操作先行发生于后面对这个变量的读操作（重要）
- 传递规则：如果操作A先行发生于操作B，而操作B又先行发生于操作C，则可以得出操作A先行发生于操作C
- ——————————————————————————————————————————————
- 线程启动规则：Thread对象的start()方法先行发生于此线程的每一个动作
- 线程中断规则：对线程interrupt()方法的调用先行发生于被中断线程的代码检测到中断事件的发生
- 线程终结规则：线程中所有的操作都先行发生于线程的终止检测，我们可以通过Thread.join()方法结束、Thread.isAlive()的返回值手段检测到线程已经终止执行
- 对象终结规则：一个对象的初始化完成先行发生于他的finalize()方法的开始

经作者授权转载，原文地址：https://blog.csdn.net/jesonjoke/article/details/79848032
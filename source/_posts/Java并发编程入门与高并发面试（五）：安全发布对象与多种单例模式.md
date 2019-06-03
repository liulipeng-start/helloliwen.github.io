---
title: Java并发编程入门与高并发面试（五）：安全发布对象与多种单例模式
categories:
  - 高并发
abbrlink: dcfa5a09
date: 2018-09-14 22:30:12
tags:
---

摘要：本文介绍安全发布对象的概念，代码演示，对象逃逸与多种单例模式

<!--more-->

------

* 概念
   + 发布对象
   + 对象逸出
* 代码演示
   + 不安全发布对象
   + 对象逸出
* 安全发布对象示例（多种单例模式演示）
   + 1、懒汉式（最简式）
   + 2、懒汉式（synchronized）
   + 3、双重同步锁模式【先入坑再出坑】
   + 4、饿汉式（最简式）
   + 5、饿汉式（静态块初始化）
   + 6、枚举式

## 概念

##### 发布对象

使一个对象能够被当前范围之外的代码所使用。 
在我们的日常开发中，我们经常要发布一些对象，比如通过类的非私有方法返回对象的引用，或者通过公有静态变量发布对象。

##### 对象逸出

一种错误的发布。当一个对象还没有构造完成时，就使它被其他线程所见。

## 代码演示

##### 不安全发布对象

```
public class UnsafePublish {

    private String[] states = {"a", "b", "c"};

    //类的非私有方法，返回私有对象的引用
    public String[] getStates() {
        return states;
    }

    public static void main(String[] args) {
        UnsafePublish unsafePublish = new UnsafePublish();
        log.info("{}", Arrays.toString(unsafePublish.getStates()));

        unsafePublish.getStates()[0] = "d";
        log.info("{}", Arrays.toString(unsafePublish.getStates()));
    }
}1234567891011121314151617
```

分析：

- 这个代码通过public访问级别发布了类的域，在类的任何外部的线程都可以访问这些域
- 我们无法保证其他线程会不会修改这个域，从而使私有域内的值错误（上述代码中就对私有域进行了修改）

##### 对象逸出

```
public class Escape {

    private Integer thisCanBeEscape = 0;

    public Escape () {
        new InnerClass();
        thisCanBeEscape = null;
    }

    //内部类构造方法调用外部类的私有域
    private class InnerClass {

        public InnerClass() {
            log.info("{}", Escape.this.thisCanBeEscape);
        }
    }

    public static void main(String[] args) {
        new Escape();
    }
}123456789101112131415161718192021
```

分析：

- 这个内部类的实例里面包含了对封装实例的私有域对象的引用，在对象没有被正确构造完成之前就会被发布，有可能有不安全的因素在里面，会导致this引用在构造期间溢出的错误。
- 上述代码在函数构造过程中启动了一个线程。无论是隐式的启动还是显式的启动，都会造成这个this引用的溢出。新线程总会在所属对象构造完毕之前就已经看到它了。
- 因此要在构造函数中创建线程，那么不要启动它，而是应该采用一个专有的start或者初始化的方法统一启动线程
- 这里其实我们可以采用工厂方法和私有构造函数来完成对象创建和监听器的注册等等，这样才可以避免错误
- ——————————————————————————————————————————————————-
- 如果不正确的发布对象会导致两种错误： 
  （1）发布线程意外的任何线程都可以看到被发布对象的过期的值 
  （2）线程看到的被发布线程的引用是最新的，然而被发布对象的状态却是过期的

## 安全发布对象示例（多种单例模式演示）

如何安全发布对象？共有四种方法

- 1、在静态初始化函数中初始化一个对象引用
- 2、将对象的引用保存到volatile类型域或者AtomicReference对象中
- 3、将对象的引用保存到某个正确构造对象的final类型域中
- 4、将对象的引用保存到一个由锁保护的域中

下面我们用各种单例模式来演示其中的几种方法

##### 1、懒汉式（最简式）

```
public class SingletonExample {
    //私有构造函数
    private SingletonExample(){
    }

    //单例对象
    private static SingletonExample instance = null;

    //静态工厂方法
    public static SingletonExample getInstance(){
        if(instance==null){
            return new SingletonExample();
        }
        return instance;
    }
}12345678910111213141516
```

分析： 
1、在多线程环境下，当两个线程同时访问这个方法，同时制定到instance==null的判断。都判断为null，接下来同时执行new操作。这样类的构造函数被执行了两次。一旦构造函数中涉及到某些资源的处理，那么就会发生错误。所以说最简式是**线程不安全的**

##### 2、懒汉式（synchronized）

```
在类的静态方法上使用synchronized修饰
 public static synchronized SingletonExample getInstance()12
```

分析： 
1、使用synchronized修饰静态方法后，保证了方法的线程安全性，同一时间只有一个线程访问该方法 
2、有缺陷：会造成性能损耗

##### 3、双重同步锁模式【先入坑再出坑】

```
public class SingletonExample {
    // 私有构造函数
    private SingletonExample() {
    }
    // 单例对象
    private static SingletonExample instance = null;
    // 静态的工厂方法
    public static SingletonExample getInstance() {
        if (instance == null) { // 双重检测机制
            synchronized (SingletonExample.class) { // 同步锁
                if (instance == null) {
                    instance = new SingletonExample();
                }
            }
        }
        return instance;
    }
}
12345678910111213141516171819
```

（入坑）分析： 
1、我们将上面的第二个例子(懒汉式（synchronized))进行了改进，由synchronized修饰方法改为先判断后，再锁定整个类，再加上双重的检测机制，保证了最大程度上的避免耗损性能。 
2、这个方法是**线程不安全**的，可能大家会想在多线程情况下，只要有一个线程对类进行了上锁，那么无论如何其他线程也不会执行到new的操作上。接下来我们分析一下线程不安全的原因：

> 这里有一个知识点：CPU指令相关 
> 在上述代码中，执行new操作的时候，CPU一共进行了三次指令 
> （1）memory = allocate() 分配对象的内存空间 
> （2）ctorInstance() 初始化对象 
> （3）instance = memory 设置instance指向刚分配的内存

在程序运行过程中，CPU为提高运算速度会做出违背代码原有顺序的优化。我们称之为乱序执行优化或者说是**指令重排**。 
那么上面知识点中的三步指令极有可能被优化为（1）（3）（2）的顺序。当我们有两个线程A与B，A线程遵从132的顺序，经过了两此instance的空值判断后，执行了new操作，并且cpu在某一瞬间刚结束指令（3），并且还没有执行指令（2）。而在此时线程B恰巧在进行第一次的instance空值判断，由于线程A执行完（3）指令，为instance分配了内存，线程B判断instance不为空，直接执行return，返回了instance，这样就出现了错误。 
![这里写图片描述](https://img-blog.csdn.net/20180409165630129?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

（出坑）解决办法：

```
在对象声明时使用volatile关键字修饰，阻止CPU的指令重排。
private volatile static SingletonExample instance = null;12
```

关于volatile如何阻止CPU指令重排，详情请见另一篇文章：[高并发探索（四)：线程安全性-可见性-有序性](https://blog.csdn.net/jesonjoke/article/details/79848032)

##### 4、饿汉式（最简式）

```
public class SingletonExample {
    // 私有构造函数
    private SingletonExample() {

    }
    // 单例对象
    private static SingletonExample instance = new SingletonExample();

    // 静态的工厂方法
    public static SingletonExample getInstance() {
        return instance;
    }
}
1234567891011121314
```

分析： 
1、饿汉模式由于单例实例是在类装载的时候进行创建，因此只会被执行一次，所以它是线程安全的。 
2、该方法存在缺陷：如果构造函数中有着大量的事情操作要做，那么类的装载时间会很长，影响性能。如果只是做的类的构造，却没有引用，那么会造成资源浪费 
3、饿汉模式适用场景为：（1）私有构造函数在实现的时候没有太多的处理（2）这个类在实例化后肯定会被使用

##### 5、饿汉式（静态块初始化）

```
public class SingletonExample {
    // 私有构造函数
    private SingletonExample() {
    }
    // 单例对象
    private static SingletonExample instance = null;
    static {
        instance = new SingletonExample();
    }
    // 静态的工厂方法
    public static SingletonExample getInstance() {
        return instance;
    }
    public static void main(String[] args) {
        System.out.println(getInstance().hashCode());
        System.out.println(getInstance().hashCode());
    }
}123456789101112131415161718
```

分析： 
1、除了使用静态域直接初始化单例对象，还可以用静态块初始化单例对象。 
2、值得注意的一点是，**静态域与静态块的顺序一定不要反**，在写静态域和静态方法的时候，一定要注意顺序，不同的静态代码块是按照顺序执行的，它跟我们正常定义的静态方法和普通方法是不一样的。

##### 6、枚举式

```
public class SingletonExample {

    private SingletonExample() {
    }

    public static SingletonExample getInstance() {
        return Singleton.INSTANCE.getInstance();
    }

    private enum Singleton {
        INSTANCE;
        private SingletonExample singleton;

        Singleton() {
            singleton = new SingletonExample();
        }

        public SingletonExample getInstance() {
            return singleton;
        }
    }
}
1234567891011121314151617181920212223
```

- 由于枚举类的特殊性，枚举类的构造函数Singleton方法只会被实例化一次，且是这个类被调用之前。这个是JVM保证的。
- 对比懒汉与饿汉模式，它的优势很明显。

经作者授权转载，原文地址：https://blog.csdn.net/jesonjoke/article/details/79866152
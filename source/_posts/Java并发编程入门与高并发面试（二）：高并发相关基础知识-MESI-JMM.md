---
title: Java并发编程入门与高并发面试（二）：高并发相关基础知识 - MESI - JMM
categories:
  - 高并发
abbrlink: 4e44ac3
date: 2018-09-14 22:30:06
tags:
---

摘要：本文介绍高并发的工具、基础概念、CPU多级缓存、缓存一致性原则、CPU的乱序执行优化、JMM、JMM的同步操作、同步规则以及并发的优势与风险。

<!--more-->

------

高并发相关基础知识
0、工具
1、基础概念
2、CPU多级缓存
3、缓存一致性(MESI Modify|Exclusive|Share|Invalid)
4、乱序执行优化
5、JAVA 内存模型(JMM)
6、Java内存模型-同步八种操作
7、Java内存模型-同步规则
8、并发的优势与风险

### 0、工具

- Apache Bench(AB) :Apache附带的工具，测试网站性能 — [ApacheBench安装及使用方法](https://blog.csdn.net/jesonjoke/article/details/79806358)

- Jmeter ： Apache组织开发的压力测试工具（比AB更强大）

- 代码测试方法 ：Semaphore、CountDownLatch类

  - Semaphore类：信号量 
    信号量，在我们测试的过程中充当监控并发数的角色。能够维持在同一时间的请求的并发量，达到并发量上线，会阻塞进程。
  - CountDownLatch类：计数器向下减的闭锁 
    ![这里写图片描述](/upload-image/high-concurrency2-1.png) 
  - 说明：假设计数器的值为3，线程A执行了await()方法之后，进入了awaiting等待状态。在其他线程的方法中执行了countDown()方法之后，计数器的值都会减一，直到计数器的值减为0，线程A的方法才继续执行。所以说，countDownLatch类可以阻塞线程执行，并且当满足指定条件后让线程继续执行。

```
/**
 * @author JeffOsmond
 * @version V1.0
 * @package com.superboys.concurrency
 * @description 【线程不安全】模拟示例
 * @email yinjiaxing_web@163.com
 * @time 2018/4/3
 */
@NotThreadSafe
@Slf4j
public class CountExample1 {

    //请求总数
    public static int clientTotal  = 5000;
    //同时并发执行的线程数
    public static int threadTotal = 200;
    //计数
    public static int count = 0;

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
}
```

### 1、基础概念

- 并发:同时拥有两个或者多个线程，如果程序在单核处理器上运行，多个线程将交替地还如或者换出内存，这些线程是同时”存在”的，每隔线程都处于执行过程中的某个状态，如果运行在多核处理器上，此时，程序中的每个线程都将分配到一个处理器核上，因此可以同时运行。
- 高并发:High Concurrency 是互联网分布式系统架构设计中必须考虑的因素之一，它通常是指，通过设计保证系统能够**同时并行处理**很多请求。

### 2、CPU多级缓存

CPU的频率太快了，快到主存跟不上，这样在处理器时钟周期内，CPU常常需要等待主存，浪费资源，所以cache的出现，是为了缓解CPU和内存之间速度的不匹配问题。 
CPU多级缓存配置（演变）： 
![这里写图片描述](/upload-image/high-concurrency2-2.png) 
局部性原理： 
(1) 时间局部性：如果某个数据被访问，那么在不久的将来它很可能被再次访问。 
(2) 空间局部性：如果某个数据被访问，那么与他相邻的数据很快也可能被访问。

### 3、缓存一致性(MESI Modify|Exclusive|Share|Invalid)

![这里写图片描述](/upload-image/high-concurrency2-3.png) 

- Modify:被修改，该缓存行只被缓存在该CPU的缓存中。并且是被修改过的，因此它与主存中的数据是不一致的，该缓存行中的内存需要在未来的某个时间点写回主存，这个时间点是允许其他CPU读取主存中相应的内存之前。当这里的值被写回主存之后，该缓存行的状态将变为Excluisive.

- Exclusive:独享，该缓存行只被缓存在该CPU的缓存中，他是未被修改过的，是与主存中的数据一致的。他可以在任何时刻，被其他CPU读取该内存时，变成Share。当该CPU修改他的内容时，变成Modify

- Share：共享，意味着该缓存行可能被多个CPU进行缓存，并且各缓存中的数据与主存数据是一致的。当有一个CPU修改该缓存行的时候，其他CPU中该缓存行变成Invalid

- Invalid：无效

   

  > 四种操作

- 本地读取 local read :读本地缓存

- 本地写入 local write : 写本地缓存

- 远端读取 remote rade : 将Memory中的数据读取过来

- 远端写入 remote write : 将数据写回Memory中 
  缓存被修改时的情况： 
  ![这里写图片描述](/upload-image/high-concurrency2-4.png) 
  某一时刻缓存被CPU A 与CPU B共享，这时CPU A 要修改本地缓存的时候，会将主存的数据与CPU B在共享的数据置为无效状态。缓存由S -> I

### 4、乱序执行优化

处理器为提高运算速度而做出违背代码原有顺序的优化。 
举例：初始计算需求如下

![这里写图片描述](/upload-image/high-concurrency2-5.png) 

预期计算流程： 
![这里写图片描述](/upload-image/high-concurrency2-6.png) 

实际计算流程（乱序执行优化后）： 
![这里写图片描述](/upload-image/high-concurrency2-7.png) 

### 5、JAVA 内存模型(JMM)

一种规范，规范了java虚拟机与计算机内存如何协同工作的。它规定了**一个线程如何和何时可以看到其他线程修改过的共享变量的值，以及在必须时如何同步地访问共享变量**。 

- 堆Heap:运行时数据区，有垃圾回收，堆的优势可以动态分配内存大小，生存期也不必事先告诉编译器，因为他是在运行时动态分配内存。缺点是由于运行时动态分配内存，所以存取速度慢一些。
- 栈Stack:优势存取速度快，速度仅次于计算机的寄存器。栈的数据是可以共享的，但是缺点是存在栈中数据的大小与生存期必须是确定的。主要存放基本类型变量，对象据点。要求调用栈和本地变量存放在**线程栈**上。
- **静态类型**变量跟随类的定义存放在堆上。存放在堆上的对象可以被所持有对这个对象引用的线程访问。
- 如果两个线程同时调用了同一个对象的同一个方法，他们都会访问这个对象的成员变量。但是这两个线程都拥有的是该对象的成员变量（局部变量）的**私有拷贝**。—[线程封闭中的堆栈封闭]

![这里写图片描述](/upload-image/high-concurrency2-8.png) 

- CPU Registers(寄存器):是CPU内存的基础，CPU在寄存器上执行操作的速度远大于在主存上执行的速度。这是因为CPU访问寄存器速度远大于主存。
- CPU Cache Memory(高速缓存):由于计算机的存储设备与处理器的运算速度之间有着几个数量级的差距，所以现代计算机系统都不得不加入一层读写速度尽可能接近处理器运算速度的高级缓存，来作为内存与处理器之间的缓冲。将运算时所使用到的数据复制到缓存中,让运算能快速的进行。当运算结束后，再从缓存同步回内存之中，这样处理器就无需等待缓慢的内存读写了。
- RAM-Main Memory(主存/内存):
- 当一个CPU需要读取主存的时候，他会将主存中的部分读取到CPU缓存中，甚至他可能将缓存中的部分内容读到他的内部寄存器里面，然后在寄存器中执行操作。当CPU需要将结果回写到主存的时候，他会将内部寄存器中的值刷新到缓存中，然后在某个时间点从缓存中刷回主存。

![这里写图片描述](/upload-image/high-concurrency2-9.png) 

- Java内存模型抽象结构：每个线程都有一个私有的本地内存，本地内存他是java内存模型的一个抽象的概念。它并不是真实存在的，它涵盖了缓存、写缓冲区、寄存器以及其他的硬件和编译器的优化。本地内存中它存储了该线程以读或写共享变量拷贝的一个副本。
- 从更低的层次来说，主内存就是硬件的内存，是为了获取更高的运行速度，虚拟机及硬件系统可能会让工作内存优先存储于寄存器和高速缓存中，java内存模型中的线程的工作内存是CPU的寄存器和高速缓存的一个抽象的描述。而JVM的静态内存存储模型它只是对内存的一种物理划分而已。它只局限在内存，而且只局限在JVM的内存。

### 6、Java内存模型-同步八种操作

![这里写图片描述](/upload-image/high-concurrency2-10.png)

- lock(锁定) ：作用于主内存变量，把一个变量标识为一条线程独占状态
- unlock(解锁) ： 作用于主内存的变量，把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定
- read(读取) ： 作用于主内存的变量，把一个变量值从主内存传输到线程的工作内存中，以便随后的load动作使用
- load(载入) ：作用于工作内存的变量，它把read操作从主内存中得到的变量值放入工作内存的变量副本中
- use(使用) ：作用于工作内存的变量，把工作内存中的一个变量值传递给执行引擎
- assign(赋值) ： 作用于工作内存的变量，它把一个从执行引擎接收到的值赋值给工作内存的变量
- store(存储) ： 作用于工作内存的变量，把工作内存中的一个变量的值传送到主内存中，以便随后的write操作
- write(写入) ：作用于主内存的变量中，它把store操作从工作内存中一个变量的值传送到主内存的变量中

### 7、Java内存模型-同步规则

- 如果要把一个变量从主内存中复制到工作内存，就需要按顺序的执行read和load操作，如果把变量从工作内存中同步回主内存中，就要按顺序的执行store和write操作。但java内存模型只要求上述操作必须按顺序执行，而没有保证必须是连续执行
- 不允许read和load、store和write操作之一单独出现
- 不允许一个线程丢弃它的最近assign的操作，即变量在工作内存中改变了之后必须同步到主内存中
- 不允许一个线程无原因的（没有发生过任何assign操作）把数据从工作内存同步回主内存中
- 一个新的变量只能在主内存中诞生，不允许在工作内存中直接使用一个未被初始化（load或assign）的变量。即就是对一个变量实施use和store操作之前，必须先执行过了assign和load操作。
- 一个变量早同一时刻只允许一条线程对其进行lock操作，但lock操作可以被同一条线程重复执行多次，多次执行lock后，只有执行相同次数的unlock操作，变量才会被解锁。lock和unlock必须是成对出现。
- 如果对一个变量执行lock操作，将会清空工作内存中此变量的值，在执行引擎使用这个变量前需要重新执行load或assign操作初始化变量的值。
- 如果一个变量事先没有被lock锁定，则不允许对它执行unlock操作，也不允许unlock一个被其他线程锁定的变量
- 对一个变量执行unlock操作之前，必须先把此变量同步到主内存中（执行store和write操作）

### 8、并发的优势与风险

> 风险：

- 安全性：多个线程共享数据时可能会产生于期望不相符的结果
- 活跃性：某个操作无法继续进行下去时，就会发生活跃性问题。比如死锁、饥饿问题
- 性能：线程过多时会使得CPU频繁切换，调度时间增多；同步机制；消耗过多内存。

> 优势：

- 速度：同时处理多个请求，响应更快；复杂的操作可以分成多个进程同时进行。
- 设计：程序设计在某些情况下更简单，也可以有更多选择
- 资源利用：CPU能够在等待IO的时候做一些其他的事情

经作者授权转载，原文地址：https://blog.csdn.net/jesonjoke/article/details/79811336
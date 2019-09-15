---
title: Java虚拟机（JVM）
categories: 
  - Java
  - JVM
tags: 面试
description: JVM集锦
abbrlink: cff4bf17
date: 2019-08-01 11:18:31
keywords:
---

JVM结构图：

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g5ka5bf08bj20hc0b8tdf.jpg)

JVM = 类加载器 classloader + 执行引擎 execution engine + 运行时数据区域 runtime data area
classloader

[JVM 的 工作原理，层次结构 以及 GC工作原理](https://segmentfault.com/a/1190000002579346)

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g5kajstouwj20rl0hvgou.jpg)

方法区、堆：所有线程共享的内存区域

Java虚拟机栈、本地方法栈、程序员计数器：运行时线程私有的内存区域

## 1.JVM的主要组成部分及其作用

- 类加载器（ClassLoader）
- 运行时数据区（Runtime Data Area）
- 执行引擎（Execution Engine）
- 本地库接口（Native Interface）

　　组件的作用： 首先通过类加载器（ClassLoader）会把 Java 代码转换成字节码，运行时数据区（Runtime Data Area）再把字节码加载到内存中，而字节码文件只是 JVM 的一套指令集规范，并不能直接交个底层操作系统去执行，因此需要特定的命令解析器执行引擎（Execution Engine），将字节码翻译成底层系统指令，再交由 CPU 去执行，而这个过程中需要调用其他语言的本地库接口（Native Interface）来实现整个程序的功能。

## 2.堆栈的区别

1. 栈内存存储的是局部变量，堆内存存储的是实体；

2. 栈内存的更新速度要快于堆内存，因为局部变量的生命周期很短；

3. 栈内存存放的变量生命周期一旦结束就会被释放，而堆内存存放的实体会被垃圾回收机制不定时的回收。

## 3.双亲委派

　　在介绍双亲委派模型之前先说下类加载器。对于任意一个类，都需要由加载它的类加载器和这个类本身一同确立在 JVM 中的唯一性，每一个类加载器，都有一个独立的类名称空间。类加载器就是根据指定全限定名称将 class 文件加载到 JVM 内存，然后再转化为 class 对象。

类加载器分类：

- 启动类加载器（Bootstrap ClassLoader）：是虚拟机自身的一部分，用来加载Java_HOME/lib/目录中的，或者被 -Xbootclasspath 参数所指定的路径中并且被虚拟机识别的类库；

- 扩展类加载器（Extension ClassLoader）：负责加载/lib/ext目录或Java. ext. dirs系统变量指定的路径中的所有类库；

- 应用程序类加载器（Application ClassLoader）：负责加载用户类路径（classpath）上的指定类库，我们可以直接使用这个类加载器。一般情况，如果我们没有自定义类加载器默认就是用这个加载器。

　　双亲委派模型：如果一个类加载器收到了类加载的请求，它首先不会自己去加载这个类，而是把这个请求委派给父类加载器去完成，每一层的类加载器都是如此，这样所有的加载请求都会被传送到顶层的启动类加载器中，只有当父加载无法完成加载请求（它的搜索范围中没找到所需的类）时，子加载器才会尝试去加载类。

[【深入理解JVM】：类加载器与双亲委派模型](https://blog.csdn.net/u011080472/article/details/51332866)

## 4.类加载的执行过程

类加载分为以下 5 个步骤：

1. **加载**：根据查找路径找到相应的 class 文件然后导入；
2. **检查**：检查加载的 class 文件的正确性；
3. **准备**：给类中的静态变量分配内存空间；
4. **解析**：虚拟机将常量池中的符号引用替换成直接引用的过程。符号引用就理解为一个标示，而在直接引用直接指向内存中的地址；
5. **初始化**：对静态变量和静态代码块执行初始化工作。

## 5.怎么判断对象是否可以被回收，如何确定垃圾

- 引用计数器：为每个对象创建一个引用计数，有对象引用时计数器 +1，引用被释放时计数 -1，当计数器为 0 时就可以被回收。它有一个缺点不能解决循环引用的问题
- 可达性分析：从 GC Roots 开始向下搜索，搜索所走过的路径称为引用链。当一个对象到 GC Roots 没有任何引用链相连时，则证明此对象是可以被回收的。

## 6.垃圾回收算法

1. **标记-清除算法**（Mark-Sweep）：最基础的垃圾回收算法，分为两个阶段，**标注**和**清除**。标记阶段标记出所有需要回收的对象，清除阶段回收被标记的对象所占用的空间。该算法最大的问题是内存碎片化严重，后续可能发生大对象不能找到可利用空间的问题。
2. **复制算法**（Copying）：为了解决标记-清除算法内存碎片化的缺陷而被提出的算法。按内存容量将内存划分为等大小的两块。每次只使用其中一块，当这一块内存满后将尚存活的对象复制到另一块上去，把已使用的内存清掉。这种算法虽然实现简单，内存效率高，不易产生碎片，但是最大的问题是可用内存被压缩到了原本的一半。且存活对象增多的话，Copying 算法的效率会大大降低。
3. **标记-整理算法**（Mark-Compact）：结合了Mark-Sweep和Copying两个算法，为了避免缺陷而提出。标记阶段和Mark-Sweep算法相同。标记后不是清理对象，而是将存活对象移向内存的一端，然后清除端边界外的对象。
4. **分代算法**：分代收集法是目前大部分 JVM 所采用的方法，其核心思想是根据对象存活的不同生命周期将内存
   划分为不同的域。一般情况下将 GC 堆划分为老生代(Tenured/Old Generation)和新生代(Young Generation)。老生代的特点是每次垃圾回收时只有少量对象需要被回收，新生代的特点是每次垃圾回收时都有大量垃圾需要被回收，因此可以根据不同区域选择不同的算法。

## 7.JVM垃圾回收器

1. **Serial**：最早的单线程串行垃圾回收器。
2. **Serial Old**：Serial 垃圾回收器的老年代版本，同样也是单线程的，可以作为 CMS 垃圾回收器的备选预案。
3. **ParNew**：是 Serial 的多线程版本。
4. **Parallel **：和 ParNew 收集器类似是多线程的，但 Parallel 是吞吐量优先的收集器，可以牺牲等待时间换取系统的吞吐量。
5. **Parallel Old**：是 Parallel 老年代版本，Parallel 使用的是复制的内存回收算法，Parallel Old 使用的是标记-整理的内存回收算法。
6. **CMS**：一种以获得最短停顿时间为目标的收集器，非常适用 B/S 系统。
7. **G1**：一种兼顾吞吐量和停顿时间的 GC 实现，是 JDK 9 以后的默认 GC 选项。

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g5w3nqkn0jj21400u0dre.jpg)

- 新生代回收器：Serial、ParNew、Parallel Scavenge
- 老年代回收器：Serial Old、Parallel Old、CMS
- 整堆回收器：G1

　　新生代垃圾回收器一般采用的是**复制算法**，复制算法的优点是效率高，缺点是内存利用率低；老年代回收器一般采用的是**标记-整理算法**进行垃圾回收。

默认垃圾收集器：

- JDK1.7：Parallel Scavenge + Serial Old
- JDK1.8：Parallel Scavenge + Serial Old
- JDK1.9：G1
- JDK10：G1

## 8.分代垃圾回收器工作原理

　　分代回收器有两个分区：老生代和新生代，新生代默认的空间占比总空间的 1/3，老生代的默认占比是 2/3。

　　新生代使用的是复制算法，新生代里有 3 个分区：Eden、To Survivor、From Survivor，它们的默认占比是 8:1:1，它的执行流程如下：

- 把 Eden + From Survivor 存活的对象放入 To Survivor 区；
- 清空 Eden 和 From Survivor 分区；
- From Survivor 和 To Survivor 分区交换，From Survivor 变 To Survivor，To Survivor 变 From Survivor。

　　每次在 From Survivor 到 To Survivor 移动时都存活的对象，年龄就 +1，当年龄到达 15（默认配置是 15）时，升级为老生代。大对象也会直接进入老生代。

　　老生代当空间占用到达某个值之后就会触发全局垃圾收回，一般使用标记-整理的执行算法。以上这些循环往复就构成了整个分代垃圾回收的整体执行流程。

## 9.JVM调优工具

　　JDK 自带了很多监控工具，都位于 JDK 的 bin 目录下，其中最常用的是 jconsole 和 jvisualvm 这两款视图监控工具。

- jconsole：用于对 JVM 中的内存、线程和类等进行监控；
- jvisualvm：JDK 自带的全能分析工具，可以分析：内存快照、线程快照、程序死锁、监控内存的变化、gc 变化等。

## 10.JVM调优常用的参数

1. -Xms2g：堆最小内存为 2G
2. -Xmx2g：堆最大内存为 2G
3. -Xss128k：栈（不区分虚拟机栈和本地方法栈）内存容量
4. -XX:PermSize=10M -XX:MaxPermSize=10M 限制方法区从而间接限制常量池的容量
5. -XX:MaxDirectMemorySize=10M 本地直接内存容量
6. -XX:NewRatio=4：设置新生代(Eden+2个Survivor)和老年代的内存比例为 1:4
7. -XX:SurvivorRatio=8：设置新生代 Eden 和 Survivor 比例为 8:2
8. -XX:+UseSerialGC：虚拟机运行在Client模式下的默认值，Serial+Serial Old
9. -XX:+UseParNewGC：指定使用 ParNew + Serial Old 垃圾回收器组合
10. -XX:+UseParallelGC：虚拟机运行在Server模式下的默认值，Parallel Scavenge+Serial Old（PS MarkSweep）
11. -XX:+UseParallelOldGC：指定使用 Parallel Scavenge+ Parallel Old 垃圾回收器组合
12. -XX:+UseConcMarkSweepGC：指定使用 ParNew+CMS+Serial Old 垃圾回收器组合，Serial Old收集器将作为CMS收集器出现Concurrent Mode Failure失败后的后备收集器使用
13. -XX:+PrintGC：开启打印 gc 信息
14. -XX:+PrintGCDetails：打印 gc 详细信息

## 11.内存泄漏与内存溢出

**内存泄露是指程序在申请内存后，无法释放已申请的内存空间，从而造成内存不可用的情况。**重启计算机可以解决，但也有可能再次发生内存泄露，内存泄露和硬件没有关系，它是由软件设计缺陷引起的。

内存溢出是指程序在申请内存时，没有足够的内存空间供其使用，出现out of memory。

[内存溢出和内存泄漏的区别、产生原因以及解决方案](https://blog.csdn.net/jingzi123456789/article/details/84196357)

[Java 内存泄漏与内存溢出详解](https://juejin.im/entry/58abdf288ac24732480d0aef)

参考：

[1.JVM 的 工作原理，层次结构 以及 GC工作原理](https://segmentfault.com/a/1190000002579346)

[2.JVM结构、GC工作机制详解](https://blog.csdn.net/tonytfjing/article/details/44278233)

[3.JVM结构、GC工作机制详解](https://blog.csdn.net/tonytfjing/article/details/44278233)

[4.Jvm 系列(二):Jvm 内存结构](http://www.ityouknow.com/jvm/2017/08/25/jvm-memory-structure.html)



---
title: Java基础
categories:
  - Java
tags: 面试
description: Java基础
abbrlink: f7ede91d
date: 2019-07-10 20:40:04
keywords:
---

## 1.JDK 和 JRE 有什么区别？

JDK：Java Development Kit 的简称，提供了 java 的开发环境和运行环境。JRE：Java Runtime Environment 的简称，提供了java的运行环境。简单来说：如果你需要运行 java 程序，只需安装 JRE 就可以了，如果你需要编写 java 程序，需要安装 JDK。

## 2.== 和 equals 的区别是什么？

== 对于基本类型来说是<font color="red">值</font>比较，对于引用类型来说比较的是<font color="red">引用</font>；而 equals本质上就是 == ，默认情况下是引用比较，只是很多类重写了 equals 方法。比如 String、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等。

## 3.两个对象的 hashCode()相同，则 equals()也一定为 true，对吗？

　　不一定！两个对象的 hashCode()相同，equals()不一定 true。

　　在Java里，equals()和hashCode()都是用来对比两个对象是否相等一致。它们的区别主要体现在性能和可靠性上：重写的equals（）比较全面比较复杂，但是效率低；而利用hashCode()进行对比，只要生成一个hash值进行比较就可以了，效率很高。

　　equals()相等的两个对象他们的hashCode()肯定相等，也就是用equals()对比是绝对可靠的。hashCode()相等的两个对象他们的equals()不一定相等，也就是hashCode()不是绝对可靠的。一般的地方不需要重写hashCode，只有当类需要放在HashTable、HashMap、HashSet等等hash结构的集合时才会重写hashCode。

## 4.final 在 java 中有什么作用？

- final 修饰的<font color="red">类</font>叫最终类，该类不能被继承。
- final 修饰的<font color="red">方法</font>不能被重写。
- final 修饰的<font color="red">变量</font>叫常量，常量必须初始化，初始化之后值就不能被修改。

## 5.java 中操作字符串都有哪些类？它们之间有什么区别？

　　操作字符串的类有：String、StringBuffer、StringBuilder。

　　String 和 StringBuffer、StringBuilder 的区别在于 String 声明的是不可变的对象，每次操作都会生成新的 String 对象，然后将指针指向新的 String 对象。而 StringBuffer、StringBuilder 可以在原有对象的基础上进行操作，所以在经常改变字符串内容的情况下最好不要使用 String。

　　StringBuffer 和 StringBuilder 最大的区别在于，StringBuffer 是线程安全的，而 StringBuilder 是非线程安全的，但 StringBuilder 的性能却高于 StringBuffer。所以在单线程环境下推荐使用 StringBuilder，多线程环境下推荐使用 StringBuffer。

## 6.String str="i"与 String str=new String("i")一样吗？

　　不一样，因为内存的分配方式不一样。String str="i"的方式，java 虚拟机会将其分配到常量池中；而 String str=new String("i") 则会被分到堆内存中。

## 7.普通类和抽象类有哪些区别？

- <font color="red">抽象方法</font>：普通类不能包含抽象方法，抽象类可以包含抽象方法。
- <font color="red">实例化</font>：抽象类不能直接实例化，普通类可以直接实例化。

## 8.抽象类和接口有什么区别

①<font color="red">实现</font>：抽象类可以被继承，接口需要被实现。一个类只能继承一个抽象类，但是可以实现多个接口；

②<font color="red">抽象方法</font>：抽象类可以有成员变量和非抽象的方法，而接口只能存在公共抽象方法；

③<font color="red">静态代码块</font>：抽象类可以有静态代码块和静态方法，而接口中不能含有静态代码块以及静态方法；

④<font color="red">抽象</font>：抽象类是对对象的抽象，然而接口是一种行为规范；

⑤<font color="red">main方法</font>：抽象类可以有main方法，而接口不能有。

## 9.BIO、NIO、AIO 有什么区别？

- BIO：Block IO 同步阻塞式 IO，就是我们平常使用的传统 IO，它的特点是模式简单使用方便，并发处理能力低。
- NIO：New IO 同步非阻塞 IO，是传统 IO 的升级，客户端和服务器端通过 Channel（通道）通讯，实现了多路复用。
- AIO：Asynchronous IO 是 NIO 的升级，也叫 NIO2.0。实现了异步非堵塞 IO ，异步 IO 的操作基于事件和回调机制。

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4vs7qhlihj21400u0gp9.jpg)


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

## 1.JDK 和 JRE 的区别

JDK：Java Development Kit 的简称，提供了 java 的开发环境和运行环境。JRE：Java Runtime Environment 的简称，提供了java的运行环境。简单来说：如果你需要运行 java 程序，只需安装 JRE 就可以了，如果你需要编写 java 程序，需要安装 JDK。

## 2.== 和 equals 的区别

== 对于基本类型来说是<font color="red">值</font>比较，对于引用类型来说比较的是<font color="red">引用</font>；而 equals本质上就是 == ，默认情况下是引用比较，只是很多类重写了 equals 方法。比如 String、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等。

## 3.两个对象的 hashCode()相同，则 equals()也一定为 true，对吗？

　　不一定！两个对象的 hashCode()相同，equals()不一定 true。

　　在Java里，equals()和hashCode()都是用来对比两个对象是否相等一致。它们的区别主要体现在性能和可靠性上：重写的equals（）比较全面比较复杂，但是效率低；而利用hashCode()进行对比，只要生成一个hash值进行比较就可以了，效率很高。

　　equals()相等的两个对象他们的hashCode()肯定相等，也就是用equals()对比是绝对可靠的。hashCode()相等的两个对象他们的equals()不一定相等，也就是hashCode()不是绝对可靠的。一般的地方不需要重写hashCode，只有当类需要放在HashTable、HashMap、HashSet等等hash结构的集合时才会重写hashCode。

## 4.final 在 java 中的作用

- final 修饰的<font color="red">类</font>叫最终类，该类不能被继承。
- final 修饰的<font color="red">方法</font>不能被重写。
- final 修饰的<font color="red">变量</font>叫常量，常量必须初始化，初始化之后值就不能被修改。

## 5.java 中操作字符串类及其区别

　　操作字符串的类有：String、StringBuffer、StringBuilder。

　　String 和 StringBuffer、StringBuilder 的区别在于 String 声明的是不可变的对象，每次操作都会生成新的 String 对象，然后将指针指向新的 String 对象。而 StringBuffer、StringBuilder 可以在原有对象的基础上进行操作，所以在经常改变字符串内容的情况下最好不要使用 String。

　　StringBuffer 和 StringBuilder 最大的区别在于，StringBuffer 是线程安全的，而 StringBuilder 是非线程安全的，但 StringBuilder 的性能却高于 StringBuffer。所以在单线程环境下推荐使用 StringBuilder，多线程环境下推荐使用 StringBuffer。

## 6.String str="i"与 String str=new String("i")是否一样

　　不一样，因为内存的分配方式不一样。String str="i"的方式，java 虚拟机会将其分配到常量池中；而 String str=new String("i") 则会被分到堆内存中。

## 7.普通类和抽象类的区别

- <font color="red">抽象方法</font>：普通类不能包含抽象方法，抽象类可以包含抽象方法。
- <font color="red">实例化</font>：抽象类不能直接实例化，普通类可以直接实例化。

## 8.抽象类和接口的区别

①<font color="red">实现</font>：抽象类可以被继承，接口需要被实现。一个类只能继承一个抽象类，但是可以实现多个接口；

②<font color="red">抽象方法</font>：抽象类可以有成员变量和非抽象的方法，而接口只能存在公共抽象方法；

③<font color="red">静态代码块</font>：抽象类可以有静态代码块和静态方法，而接口中不能含有静态代码块以及静态方法；

④<font color="red">抽象</font>：抽象类是对对象的抽象，然而接口是一种行为规范；

⑤<font color="red">main方法</font>：抽象类可以有main方法，而接口不能有。

## 9.BIO、NIO、AIO 的区别

- BIO：Block IO 同步阻塞式 IO，就是我们平常使用的传统 IO，它的特点是模式简单使用方便，并发处理能力低。
- NIO：New IO 同步非阻塞 IO，是传统 IO 的升级，客户端和服务器端通过 Channel（通道）通讯，实现了多路复用。
- AIO：Asynchronous IO 是 NIO 的升级，也叫 NIO2.0。实现了异步非堵塞 IO ，异步 IO 的操作基于事件和回调机制。

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4vs7qhlihj21400u0gp9.jpg)

## 10.final、finally、finalize 的区别

- final可以修饰<font color="red">类、变量、方法</font>，修饰类表示该类不能被继承、修饰方法表示该方法不能被重写、修饰变量表示该变量是一个常量不能被重新赋值。
- finally一般作用在try-catch代码块中，在处理异常的时候，通常我们将一定要执行的代码方法finally代码块中，表示不管是否出现异常，该代码块都会执行，一般用来存放一些关闭资源的代码。
- finalize是一个方法，属于Object类的一个方法。而Object类是所有类的父类，该方法一般由垃圾回收器来调用，当我们调用System的gc()方法的时候，由垃圾回收器调用finalize()，回收垃圾。 

## 11.try-catch-finally 中哪个部分可以省略

答：catch 可以省略


**原因：**


　　更为严格的说法其实是：try只适合处理运行时异常，try+catch适合处理运行时异常+普通异常。也就是说，如果你只用try去处理普通异常却不加以catch处理，编译是通不过的，因为编译器硬性规定，普通异常如果选择捕获，则必须用catch显示声明以便进一步处理。而<font color="red">运行时异常</font>在编译时没有如此规定，所以catch可以省略，你加上catch编译器也觉得无可厚非。


　　理论上，编译器看任何代码都不顺眼，都觉得可能有潜在的问题，所以你即使对所有代码加上try，代码在运行期时也只不过是在正常运行的基础上加一层皮。但是你一旦对一段代码加上try，就等于显示地承诺编译器，对这段代码可能抛出的异常进行捕获而非向上抛出处理。如果是普通异常，编译器要求必须用catch捕获以便进一步处理；如果运行时异常，捕获然后丢弃并且+finally扫尾处理，或者加上catch捕获以便进一步处理。

　　至于加上finally，则是在不管有没捕获异常，都要进行的“扫尾”处理。

## 12.常见的异常类

1. NullPointerException：当应用程序试图访问空对象时，则抛出该异常。

2. SQLException：提供关于数据库访问错误或其他错误信息的异常。

3. IndexOutOfBoundsException：指示某排序索引（例如对数组、字符串或向量的排序）超出范围时抛出。 

4. NumberFormatException：当应用程序试图将字符串转换成一种数值类型，但该字符串不能转换为适当格式时，抛出该异常。

5. FileNotFoundException：当试图打开指定路径名表示的文件失败时，抛出此异常。

6. IOException：当发生某种I/O异常时，抛出此异常。此类是失败或中断的I/O操作生成的异常的通用类。

7. ClassCastException：当试图将对象强制转换为不是实例的子类时，抛出该异常。

8. ArrayStoreException：试图将错误类型的对象存储到一个对象数组时抛出的异常。

9. IllegalArgumentException：抛出的异常表明向方法传递了一个不合法或不正确的参数。

10. ArithmeticException：当出现异常的运算条件时，抛出此异常。例如，一个整数“除以零”时，抛出此类的一个实例。 

11. NegativeArraySizeException：如果应用程序试图创建大小为负的数组，则抛出该异常。

12. NoSuchMethodException：无法找到某一特定方法时，抛出该异常。

13. SecurityException：由安全管理器抛出的异常，指示存在安全侵犯。

14. UnsupportedOperationException：当不支持请求的操作时，抛出该异常。

15. RuntimeException：是那些可能在Java虚拟机正常运行期间抛出的异常的超类。


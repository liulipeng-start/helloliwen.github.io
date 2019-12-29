---
title: Spark源码阅读环境搭建
categories:
  - 大数据
  - Spark
description: Spark源码阅读环境搭建
abbrlink: a05e24d7
date: 2019-12-23 16:28:24
tags:
keywords:
---

## 一、写在前面

　　为什么要阅读源码？这个问题对于热爱、学习、使用技术的程序er的重要性不言而喻：不懂原理只会使用就是重复劳动的码农，技术长进不大，以后也会碰到职业天花板。所以我们就开始吧！

　　环境如下：

- JVM：JDK 1.8.0_144
- Scala：2.12.10
- Spark：spark-3.0.0-preview
- IDE：IntelliJ IDEA 2017.2.3 

## 二、搭建步骤

### 1.下载源码并解压

下载地址： http://spark.apache.org/downloads.html 

目前（2019年12月23日）尚未发布3.0.0正式版，先下载preview版本阅读学习。

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1ga6r11fltzj215s0okael.jpg" alt="微信截图_20191223164416.png" style="zoom: 67%;" />

### 2.修改Spark源码下的pom.xml文件

打开Spark源码下的pom.xml文件，修改对应的JDK版本和maven版本

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1ga6rag1gl8j20ty0gnjro.jpg" alt="2019-12-23_165111.png" style="zoom:67%;" />

Spark源文件中有多个pom.xml，只需要改根目录下的pom.xml文件就可以了。完成修改后，就能开始编译了。 

### 3.导入源码到IDEA

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1ga6x32udwfj20x90k4409.jpg" alt="1.png" style="zoom:50%;" />

勾选对应的选项框

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1ga6xbp9kgvj20hy0gun1m.jpg" alt="5.png" style="zoom:67%;" />

添加`yarn`和`hadoop2.7`选项，其他默认即可。

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1ga6xdqlnizj20j00hw76d.jpg" alt="6.png" style="zoom:67%;" />

下一步默认：

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1ga6xew5j1xj20hw0gwjvn.jpg" style="zoom: 67%;" />

点击finished的之后，需要等待比较长时间（我使用了几个小时）的编译过程。

### 4.下载Maven依赖

　　工程导入之后，依次点击右侧`Maven Project——Generate Sources and Update Folders For All Projects`，点击之后Maven会下载编译需要的源码，需要等待一段时间，长短依赖网络好坏。

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1ga7tjejy99j20nz0cjjvg.jpg" alt="8.png" style="zoom:80%;" />

### 5.build工程

#### 问题1：scala-xml问题

~~~
Error:(48, 9) To compile XML syntax, the scala.xml package must be on the classpath.
Please see https://github.com/scala/scala-xml for details.
        <br/> ++
~~~

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1gadkjox1v4j20v70hrq4w.jpg" alt="build错误.png" style="zoom: 67%;" />

确保依赖：

~~~xml
<dependency>
    <groupId>org.scala-lang</groupId>
    <artifactId>scala-library</artifactId>
    <version>${scala.version}</version>
</dependency>

<dependency>
    <groupId>org.scala-lang.modules</groupId>
    <artifactId>scala-xml_2.12</artifactId>
    <version>1.2.0</version>
</dependency>
~~~

点击Maven Projects——Reimport All Maven Projects

具体可查看： https://github.com/scala/scala-xml 的文档说明及issues

#### 问题2：NoClassDefFoundError

~~~shell
"H:\Program Files\jdk8\bin\java" "-javaagent:..."
org.apache.spark.examples.SparkPi
Exception in thread "main" java.lang.NoClassDefFoundError: scala/collection/Seq
	at org.apache.spark.examples.SparkPi.main(SparkPi.scala)
Caused by: java.lang.ClassNotFoundException: scala.collection.Seq
	at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:335)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	... 1 more
~~~

原因： SDK没有找到SCALA的库(lib)，导致找不到类

解决方法：  在项目中的SDK中添加SCALA的库（scala-library）

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1gadlbs4crbj20ti0l4myo.jpg" alt="找不到类错误.png" style="zoom: 67%;" />

#### 问题3：找不到SparkSession

~~~shell
Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/spark/sql/SparkSession$
	at org.apache.spark.examples.SparkPi$.main(SparkPi.scala:28)
	at org.apache.spark.examples.SparkPi.main(SparkPi.scala)
Caused by: java.lang.ClassNotFoundException: org.apache.spark.sql.SparkSession$
	at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:335)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	... 2 more
~~~

解决：在Modules中的spark-examples_2.12中的Dependencies中，把所有依赖范围Scope是Provided和Runtime的全部改成Compile，Test的不用修改。

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1gadmuh0d68j21200l276g.jpg" alt="问题3.png" style="zoom:50%;" />

参考：[IntelliJ Runtime error](http://apache-spark-developers-list.1001551.n3.nabble.com/IntelliJ-Runtime-error-td11383.html)

#### 问题4：A master URL must be set in your configuration

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1gadn2jz6t1j21e00ne425.jpg" alt="问题4.png" style="zoom: 50%;" />

　　点击edit configuration，在左侧点击该项目。在右侧VM options中输入`-Dspark.master=local`，作用是指定本程序本地单线程运行，再次运行即可。

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1gadmynua1vj20tj0jtdgm.jpg" alt="问题4-2.png" style="zoom: 67%;" />

之后就可以正常运行Spark的example例子了。

参考：[Maven编译打包spark（2.1.0）源码及出现问题的解决方案（win7+Intellij IDEA）]( https://blog.csdn.net/u011464774/article/details/76704785 )
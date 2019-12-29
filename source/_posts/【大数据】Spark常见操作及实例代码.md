---
title: Spark常见操作及实例代码
categories:
  - 大数据
  - Spark
description: Spark常见操作及实例代码
abbrlink: b4d2d389
date: 2019-12-01 21:57:06
tags:
keywords:
---

## 一、RDD常见操作

### 1.转换操作（Transformation）

对一个数据为{1,2,3,3}的RDD进行基本的RDD转换操作

| 函数名                                  | 目的                                                         | 示例                      | 结果              |
| --------------------------------------- | ------------------------------------------------------------ | ------------------------- | ----------------- |
| map()                                   | 将函数应用于RDD中的每个元素，将返回值构成新的RDD             | rdd.map(x => x + 1)       | {2, 3, 4, 4}      |
| flatMap()                               | 将函数应用于RDD中的每个元素，将返回的迭代器的所有内容构成新的RDD。通常用来切分单词。map+拍扁 | rdd.flatMap(x => x.to(3)) | {1, 2, 3,2,3,3,3} |
| filter()                                | 返回一个由通过传给filter()的函数的元素组成RDD                | rdd.filter(x => x != 1)   | {2,3,3}           |
| distinct()                              | 去重                                                         | rdd.distinct()            | {1,2,3}           |
| sample(withReplacement,fraction,[seed]) | 对RDD采样，以及是否替换                                      | rdd.sample(false,0.5)     | 非确定的          |

对数据分别为{1,2,3}和{3,4,5}的RDD进行针对两个RDD的转化操作

| 函数名         | 目的                                       | 示例                    | 结果                    |
| -------------- | ------------------------------------------ | ----------------------- | ----------------------- |
| union()        | 生成一个包含两个RDD中所有元素的RDD（并集） | rdd.union(other)        | {1,2,3,3,4,5}           |
| intersection() | 求两个RDD共同元素的RDD（交集）             | rdd.intersection(other) | {3}                     |
| subtract()     | 移除一个RDD中的内容（例如移除训练数据）    | rdd.subtract(other)     | {1,2}                   |
| cartesian()    | 与另一个RDD的笛卡尔积                      | rdd.cartesian(other)    | {(1,3),(1,4),...,(3,5)} |

### 2.行动操作（Action）

对一个数据为{1,2,3,3}的RDD进行基本的RDD行动操作

| 函数名                                 | 目的                                       | 示例                                                         | 结果                |
| -------------------------------------- | ------------------------------------------ | ------------------------------------------------------------ | ------------------- |
| collect()                              | 返回RDD中的所有元素                        | rdd.collect()                                                | {1,2,3,3}           |
| count()                                | RDD中的元素个数                            | rdd.count()                                                  | 4                   |
| countByValue()                         | 各元素在RDD中出现的次数                    | rdd.countByValue()                                           | {(1,1),(2,1),(3,2)} |
| take(num)                              | 从RDD中返回num个元素                       | rdd.take(2)                                                  | {1,2}               |
| top(num)                               | 从RDD中返回最前面的num个元素               | rdd.top(2)                                                   | {3,3}               |
| takeOrdered(num)(ordering)             | 从RDD中按照提供的顺序返回最前面的num个元素 | rdd.takeOrdered(2)(myOrdering)                               | {3,3}               |
| takeSample(withReplacement,num,[seed]) | 从RDD中返回任意一些元素                    | rdd.takeSample(false,1)                                      | 非确定性            |
| reduce(func)                           | 并行整合RDD中所有数据（例如sum）           | rdd.reduce((x, y) => x + y)                                  | 9                   |
| fold(zero)(func)                       | 和reduce()一样，但是需要提供初始值         | rdd.fold(0)((x, y) => x + y)                                 | 9                   |
| aggregate(zeroValue)(seqOp,combOp)     | 和reduce相似，但是通常返回不同类型的函数   | rdd.aggregate((0, 0))((x, y) => (x.\_1 + y, x.\_2 + 1),(x, y) => (x.\_1 + y.\_1, x.\_2 + y.\_2)) | (9,4)               |
| foreach(func)                          | 对RDD中的每个元素使用给定的函数            | rdd.foreach(func)                                            | 无                  |

## 二、Pair RDD常见操作

### 1.转换操作（Transformation）

Pair RDD的转换操作(以键值对集合{(1, 2), (3, 4), (3, 6)}为例)

| 函数名                                                       | 目的                                                         | 示例                              | 结果                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------- |
| reduceByKey(func)                                            | 合并具有相同的键的值                                         | rdd.reduceByKey((x, y) => x + y)  | {(1,2),(3,10)}                        |
| groupByKey()                                                 | 对具有相同键的值进行分组                                     | rdd.groupByKey()                  | {(1,[2]),(3,[4,6])}                   |
| combineByKey(createCombiner,mergeValue,mergeCombiners,partitioner) | 使用不同的返回类型合并具有相同键的值                         |                                   | 无                                    |
| mapValues(func)                                              | 对pair RDD中的每个值应用一个函数而不改变键                   | rdd.mapValues(x => x + 1)         | {(1, 3), (3, 5), (3, 7)}              |
| flatMapValues(func)                                          | 对pairRDD中的每个值应用一个返回迭代器的函数，然后对返回的每个元素都生成一个对应原键的键值对记录。通常用户符号化 | rdd.flatMapValues( x => (x to 5)) | {(1,2),(1,3),(1,4),(1,5),(3,4),(3,5)} |
| keys()                                                       | 返回一个仅包含键的RDD                                        | rdd.keys                          | {1,3,3}                               |
| values()                                                     | 返回一个仅包含值的RDD                                        | rdd.values                        | {2,4,6}                               |
| sortByKey()                                                  | 返回一个根据键排序的RDD                                      | rdd.sortByKey()                   | {(1, 2), (3, 4), (3, 6)}              |

针对两个pair RDD的转换操作(rdd = {(1, 2), (3, 4), (3, 6)} other={(3,9)})

| 函数名         | 目的                                                         | 示例                      | 结果                                            |
| -------------- | ------------------------------------------------------------ | ------------------------- | ----------------------------------------------- |
| subtractByKey  | 删掉RDD中键与other RDD中的键相同的元素                       | rdd.subtractByKey(other)  | {(1,2)}                                         |
| join           | 对两个RDD进行内连接                                          | rdd.join(other)           | {(3, (4, 9)),(3,(6, 9))}                        |
| rightOuterJoin | 对两个RDD进行连接操作，确保第一个RDD的键必须存在(右外连接)   | rdd.rightOuterJoin(other) | {(3,(Some(4),9)),(3,(Some(6),9))}               |
| leftOuterJoin  | 对两个RDD进行连接操作，确保第二个RDD的键必须存在（左外连接） | rdd.leftOuterJoin(other)  | {(1,(2,None)),(3,(Some(4),9)),(3,(Some(6),9)))} |
| cogroup        | 将两个RDD中拥有相同键的数据分组到一起                        | rdd.cogroup(other)        | {(1,([2],[])),(3,([4,6],[9]))}                  |

### 2.行动操作(Action)

和转换操作一样，所有基础RDD支持的传统行动操作也都在pair RDD上可用。

Pair RDD的行动操作（以键值对集合{(1, 2), (3, 4),(3, 6)}为例）

| 函数           | 描述                               | 示例               | 结果             |
| -------------- | ---------------------------------- | ------------------ | ---------------- |
| countByKey()   | 对每个键对应的元素分别计数         | rdd.countByKey()   | {(1, 1),(3, 2)}  |
| collectAsMap() | 将结果以映射表的形式返回，以便查询 | rdd.collectAsMap() | Map{(1,2),(3,6)} |
| lookup(key)    | 返回给定键对应的所有值             | rdd.lookup(3)      | [4, 6]           |

## 三、一些Spark实例

### 1.WordCount

文件包含很多行文本，每行文本由多个单词构成，单词之间用空格分隔。

~~~scala
scala> val lines = sc.textFile("/word.txt")
scala> val wordCount = lines.flatMap(line => line.split(" ")).map(word => (word,1))
					.reduceByKey((a,b) => a + b)
scala> wordCount.collect()
scala> wordCount.foreach(println)
~~~

或

~~~scala
scala> lines.map(word => (word,1)).groupByKey().map(t => (t._1,t._2.sum))
~~~

纯Scala版

~~~scala
import java.io.{File, PrintWriter}

import scala.io.Source
import collection.mutable.Map

object WordCount {
	def main(args:Array[String]){
		val dirFile = new File("F:/logs")
		val files = dirFile.listFiles()
		val results = Map.empty[String,Int] //可变的空的映射(Map)对象，保存统计结果。key是单词，value是次数
		for (file <- files){    //处理文件
      val data = Source.fromFile(file)("GBK")
      //getLines方法返回文件各行构成的迭代器对象，类型为Iterator[String]
      //flatMap将每一行字符串拆分成单词，再返回所有单词构成的新迭代器
      val strs = data.getLines().flatMap(line => line.split(" "))
      strs foreach { word =>
        if(results.contains(word))
          results(word)+=1
        else
          results(word) = 1
      }
    }
    //输出文件
    val writer = new PrintWriter("F:/results.txt");
    writer.write(results.toString())
    writer.close()
    //打印控制台
    results foreach {case (k,v) => {println(s"$k:$v")}}
	}
}
~~~

### 2.统计平均销量

给定一组键值对("spark", 2)、("hadoop", 6）、("hadoop", 4）、("spark", 6），key表示图书名称，value表示某天图书销量，计算每种图书的每天的平均销量。

~~~scala
scala> val rdd = sc.parallelize(Array(("spark",2),("hadoop",6),("hadoop",4),("spark"),6))
scala> rdd.mapValues(x => (x,1)).reduceByKey((x,y) => (x._1+y._1,x._2+y._2)).mapValues(x => (x._1 / x._2)).collect()
~~~

### 3.求TopN值

文本包含很多行数据，每行数据由4个字段的值构成，不同值之间用逗号隔开，4个字段分别为orderid、userid、payment和productid，要求求出TopN个payment值。

~~~
1,1768,50,155
2,1218,600,211
3,2239,788,242
4,3101,28,599
5,4899,290,129
6,3110,54,1201
7,4436,259,877
8,2369,7890,27
~~~

代码如下：

~~~scala
import org.apache.spark.{SparkConf, SparkContext}

object TopN {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("TopN")
    val sc = new SparkContext(conf)
    sc.setLogLevel("ERROR")
    val lines = sc.textFile("hdfs://master:9000/liwen/TopN.txt")
    var num = 0 //编号
    val result = lines
      .filter(line => (line.trim.length>0) && (line.split(",").length == 4))
      .map(_.split(",")(2))
      .map(x => (x.toInt,""))
      .sortByKey(false)
      .map(x => x._1).take(5)  //top5
      .foreach(x =>{
        num += 1
        println(num + "\t" + x)
    })
  }
}
~~~

### 4.多文本排序

![微信图片_20191205152259.jpg](http://ww1.sinaimg.cn/large/75a4a8eegy1g9lwmnemupj208b0cvgmf.jpg)

代码如下：

~~~scala
import org.apache.spark.{HashPartitioner, SparkConf, SparkContext}

object FileSort {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("FileSort")
    val sc = new SparkContext(conf)
    val lines = sc.textFile("/liwen/filesort")
    var index = 0
    val result = lines.filter(_.trim.length>0)
      .map(x => (x.trim.toInt,""))
      .partitionBy(new HashPartitioner(1))
      .sortByKey().map(x => {
        index += 1
        (index,x._1)
    })
    result.saveAsTextFile("/liwen/result")
  }
}
~~~

### 5.二次排序

首先根据第1列数据降序排序，如果第1列数据相等，则根据第2列数据降序排序。

~~~
输入文件file1.txt
5 3
1 6
4 9
8 3
4 7
5 6
3 2
输出结果
8 3
5 6
5 3
4 9
4 7
3 2
1 6
~~~

SecondarySortKey.scala

~~~scala
package sort

class SecondarySortKey(val first:Int,val second:Int) extends Ordered[SecondarySortKey] with Serializable{
  override def compare(other: SecondarySortKey) = {
    if(this.first - other.first != 0){
      this.first - other.first
    }else{
      this.second - other.second
    }
  }
}
~~~

实现二次排序功能的代码文件SecondarySortApp.scala

~~~scala
package sort

import org.apache.spark.{SparkConf, SparkContext}

object SecondarySortApp {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("SecondarySortApp")
    val sc = new SparkContext(conf)
    val lines = sc.textFile("/liwen/secondarysort.txt")
    val pairWithSortKey = lines.map(line =>
      (new SecondarySortKey(line.split(" ")(0).toInt,line.split(" ")(1).toInt),line))
    // -> (SecondarySortKey(5,3),"5 3")
    val result = pairWithSortKey.sortByKey(false).map(x => x._2)
    result.collect().foreach(println)
  }
}
~~~

Spark运行命令：

~~~shell
/home/hadoop/install/spark-2.2.0-bin-hadoop2.7/bin/spark-submit --master spark://master:7077 --class sort.SecondarySortApp /home/hadoop/personaldoc/liwen/sparkapp-1.0-SNAPSHOT.jar
~~~

### 6.Spark-Shell交互式编程

计算机系的成绩如下：

~~~
Tom,DataBase,26
Tom,Algorithm,12
Tom,OperatingSystem,16
Tom,Python,40
Tom,Software,60
Tony,DataBase,30
Tony,Algorithm,12
Tony,Python,96
......
~~~

- 1.该系总共有多少学生

~~~scala
var n = 0
val stuNum = lines.map(line => (line.split(",")(0),1)).groupByKey().map(x => {n += 1;n}).collect
stuNum.length
~~~

或

~~~scala
lines.map(line => (line.split(",")(0),1)).groupByKey().count
~~~

或

~~~scala
lines.map(line => line.split(",")(0)).distinct().count
~~~

- 2.该系共开设来多少门课程

~~~scala
lines.map(line => line.trim.split(",")(1)).distinct().count
~~~

- 3.Tom 同学的总成绩平均分是多少

~~~scala
val tom = lines.filter(line => line.split(",")(0) == "Tom")
tom.map(line => (line.split(",")(0),line.split(",")(2).toInt))
.mapValues(x => (x,1)) //Array[(String, (Int, Int))] = Array((Tom,(26,1)), (Tom,(12,1)), (Tom,(16,1)), (Tom,(40,1)), (Tom,(60,1)))
.reduceByKey((x,y) =>(x._1+y._1,x._2+y._2)) //Array[(String, (Int, Int))] = Array((Tom,(154,5)))
.mapValues(x => x._1/x._2) 
.collect
~~~

- 4.求每名同学的选修的课程门数

~~~scala
lines.map(line => (line.split(",")(0),line.split(",")(1)))
.mapValues(x => (x,1))
.reduceByKey((x,y) => (" ",x._2+y._2)) //这个地方,key不能省略，否则会报错：error: type mismatch; found   : Int;  required: (String, Int)
.mapValues(x => x._2)
.collect
.foreach(println)
~~~

- 5.该系DataBase课程共有多少人选修

~~~scala
lines.filter(line => line.split(",")(1) == "DataBase")
.map(line => (line.split(",")(1),1))
.reduceByKey(_+_)
~~~

或

~~~scala
lines.filter(line => line.split(",")(1) == "DataBase").count
~~~

- 6.各门课程的平均分是多少

~~~scala
lines.map(line => (line.split(",")(1),line.split(",")(2).toInt))
.mapValues(x => (x,1))
.reduceByKey((x,y) =>(x._1+y._1,x._2+y._2))
.mapValues(x => x._1/x._2)
.collect
.foreach(println)
~~~

- 7.使用累加器计算共有多少人选了DataBase这门课

~~~scala
val pair = lines.filter(line => line.split(",")(1) == "DataBase").map(line => (line.split(",")(1),1))
val accum = sc.longAccumulator
pair.values.foreach(x => accum.add(x))
accum.value
~~~

### 7.数据去重

对两个文件进行合并，并剔除其中重复的内容，得到一个新文件C。

A文件样例如下：

~~~
20170101 x
20170102 y
20170103 x
20170104 y
20170105 z
20170106 z
~~~

B文件样例如下：

~~~
20170101 y
20170102 y
20170103 x
20170104 z
20170105 y
~~~

合并得到的输出文件C的样例如下：

~~~scala
20170101 x
20170101 y
20170102 y
20170103 x
20170104 y
20170104 z
20170105 y
20170105 z
20170106 z
~~~

代码如下：

~~~scala
import org.apache.spark.{HashPartitioner, SparkConf, SparkContext}

/**
  * 对于两个输入文件 A 和 B，编写 Spark 独立应用程序，对两个文件进行合并，并剔除其中重复的内容，得到一个新文件 C。 
  */
object RemoveDup {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("RemoveDup")
    val sc = new SparkContext(conf)
    val files = "/liwen/removedup"
    val rdd = sc.textFile(files)
    val res = rdd.filter(_.trim.length>0)
      .map(line => (line.trim,""))
      .partitionBy(new HashPartitioner(1))
      .groupByKey()
      .sortByKey()
      .keys
    res.saveAsTextFile("/liwen/removedupRes")
  }
}
~~~

### 8.多文本求平均值

Algorithm成绩：

~~~
小明 92
小红 87
小新 82
小丽 90
~~~

Database成绩：

~~~
小明 95
小红 81
小新 89
小丽 85
~~~

Python成绩：

~~~
小明 82
小红 83
小新 94
小丽 91
~~~

平均成绩如下：

~~~
(小红,83.67)
(小新,88.33)
(小明,89.67)
(小丽,88.67)
~~~

代码如下：

~~~scala
import org.apache.spark.{HashPartitioner, SparkConf, SparkContext}

/**
  * 每个输入文件表示班级学生某个学科的成绩，每行内容由两个字段组成，第一个是学生名字，第二个是学生的成绩；
  * 求出所有学生的平均成绩，并输出到一个新文件中。
  */
object AverageScore {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("AverageScore")
    val sc = new SparkContext(conf)
    val rdd = sc.textFile("/liwen/averagescore")
    val res = rdd.filter(_.trim.length>0)
      .map(line => (line.split(" ")(0).trim,line.split(" ")(1).trim.toInt))
      .partitionBy(new HashPartitioner(1))
      .groupByKey()
      .map(x =>{
        var n = 0
        var sum = 0.0
        for(i <- x._2){
          sum += i
          n += 1
        }
        val average = sum/n
        val format = f"$average%1.2f".toDouble
        (x._1,format)
      })
    res.saveAsTextFile("/liwen/averagescoreres")
  }
}
~~~


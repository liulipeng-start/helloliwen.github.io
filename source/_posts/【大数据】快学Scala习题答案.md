---
title: 快学Scala习题答案
categories:
  - 大数据
  - Scala
description: 快学Scala习题答案及容器相关操作
abbrlink: 1eb3eaf9
date: 2019-10-31 20:57:33
tags:
keywords:
---

## 第一章 基础

**1.在Scala REPL中键入3.，然后按Tab键。有哪些方法可以被应用？**

~~~shell
!=    <     >>>          doubleValue     isNaN          
%     <<    ^            floatValue      isNegInfinity  
&     <=    abs          floor           isPosInfinity  
*     ==    byteValue    getClass        isValidByte    
+     >     ceil         intValue        isValidChar    
-     >=    compare      isInfinite      isValidInt     
/     >>    compareTo    isInfinity      isValidLong    
isValidShort  shortValue      toDouble       toShort
isWhole       signum          toFloat        unary_+
longValue     to              toHexString    unary_-
max           toBinaryString  toInt          unary_~
min           toByte          toLong         underlying
round         toChar          toOctalString  until
self          toDegrees       toRadians
~~~

**2.在Scala REPL中，计算3的平方根然后再对该值求平方，结果相差多少？**

~~~scala
scala> math.sqrt(3)
res2: Double = 1.7320508075688772

scala> math.pow(res2,2)
res3: Double = 2.9999999999999996

scala> 3 - res3
res4: Double = 4.440892098500626E-16
~~~

**3.res变量是var还是val？**

~~~shell
是val。只需要对res赋值看能否成功即可。
scala> 1
res4: Int = 1

scala> res4 = 0
<console>:12: error: reassignment to val
       res4 = 0
            ^
~~~

**4.数字乘字符串，这个操作做什么？在Scaladoc中如何找到这个操作？**

~~~scala
scala> "crazy" * 3
res4: String = crazycrazycrazy
~~~

a. 从结果可见字符串的 * 运算相当于复制运算。 * 3 = 复制三次

b. 从代码可以推断, "\*"是 ”crazy"这个字符串所具有的方法，但是Java中的String没这个方法。因此，该方法在StringOps中，在Scaladoc中搜索”StringOps“，然后搜索"*"即可找到

**5.10 max 2 的意义是什么？ max 方法定义在哪个类中？**

a. 根据scala中一切事物皆对象的原则，10 max 2 相当于 (10).max(2) 

b.由于10是Int类型，所以max方法定义在Scala.Int类中。（如果Int类中没有，则可能是在RichInt类中）

**6.用BigInt计算2的1024次方**

~~~scala
object exercise6{
	def main(args:Array[String]) {
		println(scala.BigInt(2).pow(1024))
	}
}
~~~

**7. 为了在使用probablePrime(100,Random)获取随机数时不在probablePrime和Random之前使用任何限定符，你需要引入什么？**

Random在scala.util中，而probablePrime是BigInt中的方法。所以，
import scala.math.BigInt.probablePrime
import scala.util.Random

**8. 创建随机文件的方式之一是生成一个随机的BigInt，然后将它转换成三十六进制，输出类似”qsnvbevtomcj38o06kul”这样字样的字符串。查阅Scaladoc，找到在Scala中实现该逻辑的方法。**

~~~scala
scala> scala.math.BigInt(scala.util.Random.nextInt).toString(36)
res6: String = -wvvbjj
~~~

BigInt中toString方法定义：

~~~scala
def toString(radix: Int): String
// Returns the String representation in the specified radix of this BigInt.
~~~

**9. 在Scala中如何获取字符串的首字符和尾字符？**

~~~scala
// 首字符
s.head
s(0)
s.take(1)
s.charAt(0)

// 尾字符
s.last
s.takeRight(1)
s.reverse(0)
s(s.length-1)
s.charAt(s.length-1)
~~~

**10. take, drop, takeRight, dropRight 这些字符串函数是做什么用的？和substring相比，他们的优缺点有哪些？**

~~~scala
def take(n: Int): String  		// Selects first n elements.
def takeRight(n: Int): String 	// Selects last n elements.
def drop(n: Int): String 		// Selects all elements except first n ones.
def dropRight(n: Int): String 	// Selects all elements except last n ones.
~~~

优点：简洁

缺点：单方向。如果需要取中间的字串，则需要同时调用多个函数；或者用substring

~~~scala
scala> s
res19: String = Hello,Scala!

scala> s.takeRight(10).dropRight(2)
res20: String = llo,Scal

scala> s
res21: String = Hello,Scala!
~~~

## 第二章 控制结构和函数

**1.signum函数：正数返回1，负数返回-1，0返回0。**

~~~scala
object exercise1{
	def signum(num:Int) = { //有等于号，函数
		if (num > 0) 1
		else if (num < 0) -1 
		else 0
	}

	def main(args:Array[String]) { //没有等于号，过程
		println(signum(100))
		println(signum(-2))
		println(signum(0))
	}
}
~~~

**2. 一个空的表达式{}的值是什么？类型是什么？**

~~~scala
scala> val t = {}
t: Unit = ()
~~~

a.空表达式的值是 no value

b.对应的类型是 Unit (或者说Nothing)

**3. 指出在Scala中何种情况下赋值语句 x = y = 1是合法的。**

由于y=1，所以y可以定义为var类型。(y=1)本身是Unit，所以可以定义x为var Unit类型

~~~scala
scala> var x:Unit = {} //()和{}都是Unit类型
x: Unit = ()

scala> var y = 0
y: Int = 0

scala> x = y = 1
x: Unit = ()

scala> x

scala> y
res23: Int = 1
~~~

**4. 针对下列Java循环编写一个Scala版本：for(int i = 10; i >= 0; i--) System.out.println(i);**

~~~scala
for(i <- 0 to 10 reverse) println(i)
~~~

**5. 编写一个过程countdown(n : Int), 打印从n到0的数字**

过程 - 没有返回值的函数，或者返回值为Unit的函数（没有等于号）

函数 - （有等于号）

方法 - 对象的行为

~~~scala
def countdown(n : Int){
    for(i <- 0 to n reverse) println(i)
}
~~~

更scala的写法

~~~scala
(0 to n).reverse foreach println
或者
0.to(n).reverse.foreach(println)
~~~

**6. 编写一个for循环，计算字符串中所有字母的Unicode代码的乘积。举例来说，“Hello”中所有字符的乘积为9415087488L**

~~~scala
def unicodeMuti(str : String) = {
    var result = 1L
    for(s <- str) result*=s //或者s.toLong
    result
}
~~~

**7. 同样是解决前一个练习的问题，但这次不使用循环。（提示：在Scaladoc中查看StringOps）**

~~~scala
def unicodeMuti2(str : String) = {
    var result = 1L
    str.foreach(result *= _) //_是通配符，表示传入的元素
    result
}
~~~

**9. 把前一个练习中的函数改成递归函数。**

~~~scala
def product(s:String):Long = {
    if(s.length == 1) s.head
    else s.head * product(s.drop(1)) //取首字符方法看第一章第9题
}
~~~

10.编写函数计算x<sup>n</sup>，其中n是整数，使用如下递归定义：

- x<sup>n</sup>=y<sup>2</sup>，如果n是正偶数的话，这里的y=x<sup>n/2</sup>。

- x<sup>n</sup>=x*x<sup>n-1</sup>，如果n是正奇数的话。
- x<sup>0</sup>=1
- x<sup>n</sup>=1/x<sup>-n</sup>，如果n是负数的话。

~~~scala
//模式匹配
def xn(x:Int,n:Int) :Double= {
    n match {
        case _ if(n>0 && n%2==0) => xn(x,n>>1)*xn(x,n>>1)			
        case _ if(n>0 && n%2==1) => x * xn(x,n-1)
        case 0 => 1
        case _ if(n<0) => 1/xn(x,-n)
    }
}
~~~

~~~scala
def mypow(x:Int,n:Int):Double={
    if(n>0 && n%2==0) mypow(x,n>>1)*mypow(x,n>>1)
    else if(n>0 && n%2==1 ) x*mypow(x,n-1)
    else if(n == 0) 1
    else 1/mypow(x,-n)
}
~~~

## 第三章 数组相关操作

**1. 编写一段代码，将a设置为一个n个随机整数的数组，要求随机数结余0（包含）和n（不包含）之间。**

~~~scala
def makeArr(n:Int) = {
    val a = new Array[Int](n)
    for(i <- a) yield scala.util.Random.nextInt(n)
}
~~~

**2. 编写一个循环，将整数数组中相邻的元素互换。例如，Array(1,2,3,4,5)经过互换后得到Array(2,1,4,3,5)**

~~~scala
def main(args:Array[String]){
    val arr = Array(1,2,3,4,5)
    swap(arr)
    arr.foreach(println)
}
def swap(arr:Array[Int]){
    //也可写成i <- 0 until (arr.length-1,2)
    for(i <- 0 until arr.length-1 by 2){
        val temp = arr(i)
        arr(i) = arr(i+1)
        arr(i+1) = temp
    }
}
~~~

**3. 重复前一个练习，不过这一次生成一个新的值交换过的数组。用for/yield。(note: 习题3是返回一个新数组；习题2是直接修改原数组）**

~~~scala
def main(args:Array[String]){
    val a = Array(1,2,3,4,5)
    val b = swapYield(a)
    b.foreach(print)
}

def swapYield(arr:Array[Int]) = {
    for(i <- 0 until arr.length) yield{
        if(i<arr.length-1 && i%2 == 0) arr(i+1)
        else if(i<arr.length-1 && i%2 == 1) arr(i-1)
        else arr(i)
    }
}
~~~

**4. 给定一个整数数组，产出一个新的数组，包含原数组中的所有正值，以原有顺序排列；之后的元素是所有零或负值，按原有顺序排列**

~~~scala
import scala.collection.mutable.ArrayBuffer
object exercise{
	def main(args:Array[String]){
		val a = Array(-3,1,0,0,2,-2,-1,3)
	    val b = sigArr(a)
	    b.foreach(print)
	}

	def sigArr(arr:Array[Int]) = {
		val buf = new ArrayBuffer[Int]()
		buf ++= (for(i <- arr if i>0) yield i)
		buf ++= (for(i <- arr if i==0) yield i)
		buf ++= (for(i <- arr if i<0) yield i)
		buf //或者buf.toArray
	}
}
~~~

**5. 如何计算Array[Double]的平均值**

~~~scala
def avg(arr:Array[Double]) = {
    arr.sum / arr.length
}
~~~

**6. 如何重新组织Array[Int]的元素将它们以反序排列？对于ArrayBuffer[Int]你又会怎么做呢？**

~~~scala
// Array的反转
def revertArr(arr:Array[Int]) {
    for(i <- 0 until arr.length>>1){
        val temp = arr(i)
        arr(i) = arr(arr.length-1-i)
        arr(arr.length-1-i)=temp
    }
}
~~~

~~~scala
// ArrayBuffer的反转，直接调用reverse函数
import scala.collection.mutable._
def main(args:Array[String]){
    val a = Array(1,2,3,4,5,6)
    val b = new ArrayBuffer[Int]()
    b ++= a		
    val c = revertArrayBuf(b)
    c.foreach(print)
}
def revertArrayBuf(arr:ArrayBuffer[Int]) = {
    arr.reverse
}
~~~

**7. 编写一段代码，产出数组中的所有值，去掉重复项。**

~~~scala
def main(args:Array[String]){
    val a = Array(1,1,2,2,2,3,4,5,5)
    val b = removeDulicate(a)
    b.foreach(print)
}

import scala.collection.mutable.ArrayBuffer
def removeDulicate(arr:Array[Int]) = {
    val a = arr.distinct
    //a
    //或者
    val b = new ArrayBuffer[Int]()
    b ++= a.distinct
    b.toArray
}
~~~

**8. 重新编写3.4节结尾的示例：移除整数数组缓存中除第一个负数外的所有负数。思路：收集负值元素的下标，反序，去掉最后一个下标，然后对每一个下标调用a.remove(i)。比较这样的效率和3.4节另外两种方法的效率。**

~~~scala
def main(args:Array[String]){
    val a = Array(1,-1,0,2,3,-4,-5)
    val b = delAll(a)
    b.foreach(print)
}

def del(arr:Array[Int]) = {		
    val indexs = for(i <- arr.indices if arr(i)<0) yield i
    val dropIndexes = indexs.reverse.dropRight(1) //从后往前删除，所以要反转
    val t = arr.toBuffer
    for(i <- dropIndexes) t.remove(i) 
    t
}
//3.4节是移除所有负数元素
def delAll(arr:Array[Int]) = {
    //直接做法是保持所有非负数的元素
    //val result = for(e <- arr if e >= 0) yield e

    //记住要删除的元素的下标

    //记住要保留的位置，将对应的元素（往前）复制，最后缩短缓冲
    val postitionToKeep = for(i <- arr.indices if arr(i) > 0) yield i
    val a = arr.toBuffer
    for(j <- postitionToKeep.indices) a(j)=a(postitionToKeep(j))
    a.trimEnd(a.length - postitionToKeep.length)
    a.toArray
}
~~~

**10. 创建一个由java.util.TimeZone.getAvailableIDs返回的时区集合，判断条件是它们在美洲。去掉”America/“前缀并排序**

~~~scala
def main(args:Array[String]){
    val arr = java.util.TimeZone.getAvailableIDs()
    val t = (for(e <- arr if e.startsWith("America")) yield e.drop("America/".length))
    scala.util.Sorting.quickSort(t)
    t.foreach(i => print(i+' ')) //容器的遍历操作
}
~~~

更多方法查看API文档：[Array]( https://scala-lang.org/files/archive/api/2.11.8/#scala.Array )　[ArrayBuffer]( https://scala-lang.org/files/archive/api/2.11.8/#scala.collection.mutable.ArrayBuffer )

## 第四章 映射和元组

**1. 设置一个映射，其中包含你想要的一些装备，以及它们的价格。然后构建另外一个映射，采用同一组键，但在价格上打九折。**

~~~scala
def main(args:Array[String]){
    val map = Map("cup"->10,"book"->30,"notebook"->20)
    val discount = for((k,v) <- map) yield (k,v*0.9)
    discount.foreach(println)
}
~~~

**2.编写一段程序，从文件中读取单词。用一个可变映射来清点每个单词出现的频率。读取这些单词的操作可以使用java.util.Scanner:**

~~~scala
val in = new java.util.Scanner(new java.io.File("myfile.txt")) 
while(in.hasNext()) //处理 in.next()或者翻到第9章看看更Scala的做法。 
~~~

**最后，打印出所有单词和它们出现的次数。**

~~~java
def readByJava(){
    val in = new java.util.Scanner(new java.io.File("myfile.txt"))
        val map = new scala.collection.mutable.HashMap[String,Int]
        var key:String = null
            while(in.hasNext()){
                key = in.next()
                    map(key) = map.getOrElse(key,0)+1
            }
    map.foreach(println)
}
~~~

~~~scala
//更scala的写法
def readByScala(){
    val source = scala.io.Source.fromFile("myfile.txt").mkString //将文件读取为字符串
    val tokes = source.split("\\s+") //使用正则表达式分割字符（匹配任何空白字符，包括空格、制表符、换页符等等）
    val map = scala.collection.mutable.Map.empty[String,Int]
    for(key <- tokes)
    map(key) = map.getOrElse(key,0)+1 // 用 getOrElse 来加入新的元素或加1

    println(map.mkString(","))// 用","来作为打印map中元素的分隔符
}
~~~

**3. 重复前一个练习，这次用不可变的映射**

~~~scala
def main(args:Array[String]) {
    val source = scala.io.Source.fromFile("myfile.txt").mkString
    val tokens = source.split("\\s+")
    var map = Map[String,Int]()// 利用var变量来更新不可变映射
    for(key <- tokens){			
        map = map + (key -> (map.getOrElse(key,0) + 1))//+运算方法来添加元素，返回新的映射
    }

    map.foreach{case (k,v) => println(k+"->"+v)}//最外面()是不行的
}
~~~

**4. 重复前一个练习，这次使用已排序的映射，以便单词可以按顺序打印出来**

~~~scala
def exercise4(){
    val source = scala.io.Source.fromFile("myfile.txt").mkString
    val tokens = source.split("\\s+")
    var map = collection.SortedMap[String,Int]()

    for(key <- tokens)
    map += (key -> (map.getOrElse(key,0)+1))

    println(map.mkString(","))
}
~~~

**5. 重复前一个练习，这次使用java.util.TreeMap并使之适用于Scala API**

~~~scala
import scala.collection.JavaConversions.mapAsScalaMap //必须引入
def exercise5(){
    val source = scala.io.Source.fromFile("myfile.txt").mkString
    val tokens = source.split("\\s+")
    val map:scala.collection.mutable.Map[String,Int] = new java.util.TreeMap[String,Int]
    scala.io.Source.fromFile("myfile.txt").getLines().foreach(
        lines => {
            for(key <- lines.split("\\s+")){
                map(key) = map.getOrElse(key,0)+1
            }
        }
    )
    map.foreach{case (k,v) => println(k+":"+v)}
}
~~~

**6. 定义一个链式哈希映射,将"Monday"映射到 java.util.Calendar.MONDAY,依次类推加入其他日期。展示元素是以插入的顺序被访问的**

~~~java
import scala.collection.mutable.LinkedHashMap // 链式哈希映射
import java.util.Calendar
def exercise6(){
	val map = new LinkedHashMap[String,Int]
	map += ("Monday" -> Calendar.MONDAY)  // 2
	map += ("Tuesday" -> Calendar.TUESDAY)  // 3
	map += ("Wednesday" -> Calendar.WEDNESDAY) 
	map += ("Thursday" -> Calendar.THURSDAY) 
	map += ("Friday" -> Calendar.FRIDAY) 
	map += ("Saturday" -> Calendar.SATURDAY) // 7
	map += ("Sunday" -> Calendar.SUNDAY) // 1

	println(map.mkString(","))
}
~~~

**7. 打印出所有Java系统属性的表格**

~~~scala
import scala.collection.JavaConversions.propertiesAsScalaMap//必须引入
def exercise7() {
    val props:collection.Map[String,String] = System.getProperties()
    val keys = props.keySet
    val keyLens = for(key <- keys) yield key.length
    val maxKeyLens = keyLens.max

    for(key <- keys){
        print(key)
        print(" " * (maxKeyLens - key.length))
        print(" | ")
        println(props(key))
    }
}
~~~

~~~shell
env.emacs                     | 
java.runtime.name             | Java(TM) SE Runtime Environment
sun.boot.library.path         | H:\Program Files\jdk8\jre\bin
java.vm.version               | 25.144-b01
java.vm.vendor                | Oracle Corporation
java.vendor.url               | http://java.oracle.com/
path.separator                | ;
java.vm.name                  | Java HotSpot(TM) 64-Bit Server VM
file.encoding.pkg             | sun.io
user.country                  | CN
user.script                   | 
sun.java.launcher             | SUN_STANDARD
sun.os.patch.level            | 
java.vm.specification.name    | Java Virtual Machine Specification
user.dir                      | D:\java\scala\chapter4
java.runtime.version          | 1.8.0_144-b01
java.awt.graphicsenv          | sun.awt.Win32GraphicsEnvironment
java.endorsed.dirs            | H:\Program Files\jdk8\jre\lib\endorsed
os.arch                       | amd64
java.io.tmpdir                | 
line.separator                | 

java.vm.specification.vendor  | Oracle Corporation
user.variant                  | 
os.name                       | Windows 10
sun.jnu.encoding              | GBK
java.library.path             | 。。。
java.specification.name       | Java Platform API Specification
java.class.version            | 52.0
sun.management.compiler       | HotSpot 64-Bit Tiered Compilers
os.version                    | 10.0
user.home                     | 。。。
user.timezone                 | 
scala.home                    | H:\install\scala\bin\..
java.awt.printerjob           | sun.awt.windows.WPrinterJob
file.encoding                 | GBK
java.specification.version    | 1.8
scala.usejavacp               | true
java.class.path               | 。。。
user.name                     | liwen
java.vm.specification.version | 1.8
sun.java.command              | scala.tools.nsc.MainGenericRunner D:\java\scala\chapter4\3.scala
java.home                     | H:\Program Files\jdk8\jre
sun.arch.data.model           | 64
user.language                 | zh
java.specification.vendor     | Oracle Corporation
awt.toolkit                   | sun.awt.windows.WToolkit
java.vm.info                  | mixed mode
java.version                  | 1.8.0_144
java.ext.dirs                 | 。。。
sun.boot.class.path           | 。。。
java.vendor                   | Oracle Corporation
file.separator                | \
java.vendor.url.bug           | http://bugreport.sun.com/bugreport/
sun.io.unicode.encoding       | UnicodeLittle
sun.cpu.endian                | little
sun.desktop                   | windows
sun.cpu.isalist               | amd64
~~~

**8. 编写一个函数minmax(values:Array[Int]),返回数组中最小值和最大值的对偶**

~~~scala
def main(args:Array[String]) {
    val array = Array(1,2,3,4,5)
    var tuple:(Int,Int) = minmax(array)
    println(tuple._1+" "+tuple._2)
}
def minmax(arr:Array[Int]) = {
    (arr.min,arr.max)
}
~~~

**9. 编写一个函数Iteqgt(values:Array[Int],v:Int),返回数组中小于v,等于v和大于v的数量，要求三个值一起返回**

~~~scala
def main(args:Array[String]) {
    val values = Array(-5, -3, -1, 0, 0, 1, 2, 3, 4, 5);
    val v = 0
    val tuple = Iteqgt(values,v)
    printf("小于%d的数量：%d\n",v,tuple._1)
    printf("等于%d的数量：%d\n",v,tuple._2)
    printf("大于%d的数量：%d\n",v,tuple._3)
}
def Iteqgt(values:Array[Int],v:Int) = {
    (values.count(_ < v),values.count(_ == v),values.count(_ > v))
}
~~~

**10. 当你将两个字符串拉链在一起，比如"Hello".zip("World")，会是什么结果？想出一个讲得通的用例**

~~~scala
def exercise10() {
    val tuple = "Hello".zip("world")
    tuple.toMap
    tuple.foreach(println)
}
~~~

结果：

~~~scala
(H,w)
(e,o)
(l,r)
(l,l)
(o,d)
~~~

或者：

~~~shell
scala> "hello".zip("world")
res0: scala.collection.immutable.IndexedSeq[(Char, Char)] = Vector((h,w), (e,o), (l,r), (l,l), (o,d))
~~~

更多方法查看API文档：[Map]( https://scala-lang.org/files/archive/api/2.11.8/#scala.collection.Map )

## 第五章 类

**1. 改进5.1节的Counter类,让它不要在Int.MaxValue时变成负数**

~~~scala
class counter{
    private var value = 0
    def increment(){value = if(value < Int.MaxValue) value+1 else value}
    def current = value
}

object counter{
    def main(args:Array[String]){
        val max = Int.MaxValue
        println("Int最大值:"+max)
        val myCounter = new counter
        for(i <- 1 to max)
        myCounter.increment
        println("经过"+max+"增加后value值为："+myCounter.current)
    }
}
~~~

**2. 编写一个BankAccount类，加入deposit和withdraw方法，和一个只读的balance属性**

~~~scala
class BankAccount{
    private var balance = 0.0
    def deposit(depo:Double){balance+=depo}
    def withdraw(draw:Double){balance-=draw}
    def current = balance
}

object BankAccount{
    def main(args: Array[String]){
        val depo = 1000
        val draw = 800
        val acc = new BankAccount
        println("存入金额："+depo)
        acc.deposit(depo)
        println("余额："+acc.current)

        println("取出金额："+draw)
        acc.withdraw(draw)
        println("余额："+acc.current)
    }
}
~~~

**3. 编写一个Time类，加入只读属性hours和minutes，和一个检查某一时刻是否早于另一时刻的方法 before(other:Time):Boolean。Time对象应该以new Time(hrs,min)方式构建。其中hrs以军用时间格式呈现(介于0和23之间)**

~~~scala
class Time(val hours:Int,val minutes:Int) {
    def before(other:Time):Boolean={
        hours < other.hours || (hours == other.hours && minutes<other.minutes)
    }

    override def toString: String = hours+":"+minutes
}
object Time{
    def main(args: Array[String]){
        val t1 = new Time(10,30)
        val t2 = new Time(22,30)
        val t3 = new Time(23,0)
        println("t1时刻是："+t1.toString())
        println("t2时刻是："+t2.toString())
        println("t3时刻是："+t3.toString())
        println("t1早于t2时刻吗？"+t1.before(t2))
        println("t3早于t2时刻吗？"+t3.before(t2))
    }
}
~~~

**4. 重新实现前一个类中的Time类，将内部呈现改成午夜起的分钟数(介于0到24*60-1之间)。不要改变公有接口。也就是说，客户端代码不应因你的修改而受影响 **

~~~scala
class Time(val hours:Int,val minutes:Int) {
    def before(other:Time):Boolean={
        hours < other.hours || (hours == other.hours && minutes<other.minutes)
    }

    override def toString: String = hours*60+minutes
}
~~~

**5. 创建一个Student类，加入可读写的JavaBeans属性name(类型为String)和id(类型为Long)。有哪些方法被生产？(用javap查看。)你可以在Scala中调用JavaBeans的getter和setter方法吗？应该这样做吗？**

~~~scala
import scala.beans.BeanProperty
class Student {
  @BeanProperty
  val name:String=null
  @BeanProperty
  val id:Long=0
}
~~~

~~~java
Compiled from "Student.scala"
public class chapter5.second.Student {
  public java.lang.String name();
    Code:
       0: aload_0
       1: getfield      #15                 // Field name:Ljava/lang/String;
       4: areturn
    LineNumberTable:
      line 12: 0
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0       5     0  this   Lchapter5/second/Student;

  public long id();
    Code:
       0: aload_0
       1: getfield      #20                 // Field id:J
       4: lreturn
    LineNumberTable:
      line 14: 0
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0       5     0  this   Lchapter5/second/Student;
  //val字段只生成get方法
  public java.lang.String getName();
    Code:
       0: aload_0
       1: invokevirtual #23                 // Method name:()Ljava/lang/String;
       4: areturn
    LineNumberTable:
      line 12: 0
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0       5     0  this   Lchapter5/second/Student;

  public long getId();
    Code:
       0: aload_0
       1: invokevirtual #26                 // Method id:()J
       4: lreturn
    LineNumberTable:
      line 14: 0
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0       5     0  this   Lchapter5/second/Student;

  public chapter5.second.Student();
    Code:
       0: aload_0
       1: invokespecial #30                 // Method java/lang/Object."<init>":()V
       4: aload_0
       5: aconst_null
       6: putfield      #15                 // Field name:Ljava/lang/String;
       9: aload_0
      10: lconst_0
      11: putfield      #20                 // Field id:J
      14: return
    LineNumberTable:
      line 15: 0
      line 12: 4
      line 14: 9
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0      15     0  this   Lchapter5/second/Student;
~~~

**6. 在5.2节的Person类中提供一个主构造器,将负年龄转换为0 **

~~~scala
class Person(var age:Int) {
  if(age<0) age = 0
}
~~~

**7. 编写一个Person类，其主构造器接受一个字符串，该字符串包含名字，空格和姓，如new Person("Fred Smith")。提供只读属性firstName和lastName。主构造器参数应该是var,val还是普通参数？为什么？**

~~~scala
class Person2(val name:String) {
  val firstName:String = {val tokens = name.split("\\s+");tokens.head}//或者tokens(0)
  val lastName:String = {val tokens = name.split("\\s+");tokens.last}//或者tokens(1)
}
~~~

应该设为val。如果为var，则对应的此字符串有get和set方法，而Person中的firstName和lastName为只读的,
所以不能重复赋值。如果为var则会重复赋值而报错。

**8. 创建一个Car类，以只读属性对应制造商，型号名称，型号年份以及一个可读写的属性用于车牌。提供四组构造器。每个构造器fc都要求制造商和型号为必填。型号年份和车牌可选，如果未填，则型号年份为-1，车牌为空串。你会选择哪一个作为你的主构造器？为什么？**

~~~scala
class Car(val maker:String, val typeName:String, val year:Int = -1, var licencePlate:String = "") {
  def this(mk:String, tname:String) { this(mk, tname, -1, "") }
  def this(mk:String, tname:String, y:Int) { this(mk,tname,y,"") }
  def this(mk:String, tname:String, c:String){ this(mk,tname,-1,c)}
}
~~~

直接用四个参数的主构造器会非常省代码！！！其他都需要多些几行~

**10. 考虑如下的类 **

~~~scala
class Employ(val name:String, var salary:Double){      
	def this(){this("John Q. Public",0.0)}  
}
~~~

**重写该类,使用显示的字段定义，和一个缺省主构造器。你更倾向于使用哪种形式？为什么？**

~~~scala
class Employee(val name:String,var salary:Double) {
  def this(){this("John Q. Public",0.0)}
}

class Employee2(){
  val name:String = "John Q. Public"
  val salary:Double = 0.0
}
~~~

第二种更Java/C++风格，第一种更Scala风格。从Scalable的角度来说的话，第一种更容易拓展，代码量更少。所以，值得转换思路。

## 第六章 对象（面向对象编程）

**1. 编写一个Conversion对象，加入inchesToCentimeters、gallonsToLitters和milesToKilometers方法**

~~~scala
object Conversions{
	private val i2c = 30.48
  	private val g2l = 3.785411784
  	private val m2k = 1.609344
  	def inchesToCentimeters(inch:Double) = inch*i2c 
  	def gallonsToLiters(gallon:Double) = gallon*g2l
  	def milesToKilometers(miles:Double) = miles*m2k
  	
  	def main(args:Array[String]){
  		val inch = 1
	    val gallon = 10
	    val mile = 70
	    println(inch+"英尺="+inchesToCentimeters(inch)+"厘米")
	    println(gallon+"加仑="+gallonsToLiters(gallon)+"升")
	    println(mile+"英里="+milesToKilometers(mile)+"千米")
  	}
}
~~~

**2. 前一个练习不是很面向对象。提供一个通用的超类UnitConversion并定义扩展该超类的InchesToCentimeters、GallonsToLitters和MilesToKilometers对象**

~~~scala
abstract class UnitConversion{
	def inchesToCentimeters(){}
	def gallonsToLitters(){}
	def milesToKilometers(){} 
}

object InchesToCentimeters extends UnitConversion{
	override def inchesToCentimeters(){}
}

object GallonsToLitters extends UnitConversion {
	override def gallonsToLitters(){}
}

object MilesToKilometers extends UnitConversion {
	override def milesToKilometers(){}
}
~~~

**3. 定义一个扩展自java.awt.Point的Origin对象。为什么说这实际上不是个好主意？（仔细看Point类的方法）**

~~~scala
import java.awt.Point

object Origin extends Point with App { //App特质，可直接运行程序。应用程序对象
  override def getLocation: Point = super.getLocation
  Origin.move(2,3)
  println(Origin.toString)
}
~~~

**4. 定义一个Point类和伴生对象，使得我们可以不用new而直接用Point(3,4)来构造Point实例**

~~~scala
class Pointer(x:Int,y:Int) {
  override def toString: String = "x="+x+" y="+y
}

object Pointer extends App{
  def apply(x: Int, y: Int): Pointer = new Pointer(x, y)
  val p = new Pointer(3,4)
  println(p)
}
~~~

**5. 编写一个scala应用程序，使用App特质，以反序打印命令行参数，用空格隔开。举例来说，
scala Reverse Hello World应该打印出World Hello**

~~~scala
object Reverse extends App {
   println(args.reverse.mkString(" "))
    
   for (str <- args.reverse){
      print(str+" ")
   } 
}
~~~

**6. 编写一个扑克牌四种花色的枚举，让其toString方法分别返回梅花、方块、红桃和黑桃。**

~~~scala
object PokerFace extends Enumeration with App {
  type PokerFace = Value
  val SPADES = Value("黑桃")
  val HEARTS = Value("红桃")
  val DIAMONDS = Value("方块")
  val CLUBS = Value("梅花")

  println(PokerFace.SPADES)
  println(PokerFace.HEARTS)
  println(PokerFace.DIAMONDS)
  println(PokerFace.CLUBS)

  for (p <- PokerFace.values) println(p)
}
~~~

**7. 实现一个函数，检查某张牌的花色是否为红色。**

~~~scala
object Card extends Enumeration with App {
  type Card = Value //增加一个类型别名
  val SPADES = Value("黑桃")
  val HEARTS = Value("红桃")
  val DIAMONDS = Value("方块")
  val CLUBS = Value("梅花")

  def checkColor(c:Card)={
    if(c == Card.HEARTS || c == Card.DIAMONDS) "Red"
    else "Black"
  }

  println(checkColor(Card.SPADES))
  println(checkColor(Card.HEARTS))
}
~~~

**8. 编写一个枚举，描述RGB立方体的8个角。ID使用颜色值(例如，红色是0xff0000)**

~~~scala
object RGB extends Enumeration with App {   
	val RED = Value(0xff0000, "Red")   
	val BLACK = Value(0x000000, "Black")   
	val GREEN = Value(0x00ff00, "Green")   
	val CYAN = Value(0x00ffff, "Cyan")   
	val YELLOW = Value(0xffff00, "Yellow")   
	val WHITE = Value(0xffffff, "White")   
	val BLUE = Value(0x0000ff, "Blue")   
	val MAGENTA = Value(0xff00ff, "Magenta")   
}
~~~

## 第八章 继承

**1.扩展如下的BankAccount类，新类CheckingAccount对每次存款和取款都收取1美元的手续费。**

~~~scala
class BankAccount(initialBalance:Double) {
  private var balance = initialBalance
  def currentBalance = balance
  def depoit(amount:Double) = { balance += amount;balance}
  def withdraw(amount:Double) = { balance -= amount;balance}
}
~~~

~~~scala
class  CheckingAccount(initialBalance:Double) extends BankAccount(initialBalance){
  override def depoit(amount: Double): Double = super.depoit(amount-1)
  //是+1，如果减1的话，就相差2块了
  override def withdraw(amount: Double): Double = super.withdraw(amount+1)
}

object CheckingAccount{
  val account = new CheckingAccount(0)
  val in = 1000
  val out = 800

  def main(args: Array[String]) {
    account.depoit(in)
    println("存入："+in+" 余额："+account.currentBalance)
    account.withdraw(out)
    println("取出："+out+" 余额："+account.currentBalance)
  }
}
~~~

**2.扩展前一个练习的BankAccount类，新类SavingAccount每个月都有利息产生（earnMonthlyInterest方法被调用），并且有每月三次免手续费的存款或取款。在earnMonthlyInterest方法中重置交易计数**

~~~scala
class SavingAccount(initialBalance:Double) extends BankAccount(initialBalance){
  private var freeCount = 3 //免手续费次数
  private val interestRate = 0.03
  def CurrentCount = freeCount
  def earnMonthlyInterest = {
    super.depoit(super.currentBalance*interestRate)
    super.currentBalance*interestRate
  }

  override def depoit(amount: Double) = {
    if(freeCount>0){
      freeCount -= 1
      super.depoit(amount)
    }else{
      super.depoit(amount-1)
    }
  }

  override def withdraw(amount: Double) = {
    if(freeCount>0){
      freeCount-=1
      super.withdraw(amount)
    }else{
      super.withdraw(amount+1)
    }
  }
}

object SavingAccount{
  val in = 1000
  val out = 100
  var interest = 0.0
  val sa = new SavingAccount(1000)

  def main(args: Array[String]) {
    for(i <- 1 until 32){
      if(i>=1 && i<=4){
        sa.depoit(in)
        println(i+"号存入"+in+",余额："+sa.currentBalance+",剩余免费次数："+sa.freeCount)
      }
      if(i>=29 && i<=31){
        if(i == 30)
          interest = sa.earnMonthlyInterest
        sa.withdraw(out)
        println(i+"号取出"+out+",余额："+sa.currentBalance+",剩余免费次数："+sa.freeCount)
      }
    }
    println("一个月的利息为："+interest+",剩余免费次数："+sa.CurrentCount)
  }
}
~~~

**4.定义一个抽象类Item，加入方法price和description。SimpleItem是一个在构造器中给出价格和描述的物件。利用val可以重写def这个事实。Bundle是一个可以包含其他物件的物件。其价格是打包中所有物件的价格之和。同时提供一个将物件添加到打包中的机制，以及一个合适的description方法。**

~~~scala
import scala.collection.mutable.ArrayBuffer

abstract class Item {
  def price:Double //没有方法体，这是一个抽象方法
  def description:String
}

class SimpleItem(override val price:Double,override val description:String) extends Item{

}

class Bundle() extends Item{
  val itemList = ArrayBuffer[Item]()
  def addItem(item:Item){ itemList += item  }

  override def price: Double = {
    var priceTotal:Double = 0
    itemList.foreach(i => priceTotal += i.price)
    priceTotal
  }

  override def description: String = {
    var descriptionTotal:StringBuilder = new StringBuilder
    itemList.foreach(i => descriptionTotal.append(i.description+" "))
    descriptionTotal.toString()
  }
}

object Bundle{
  val bundle = new Bundle

  def main(args: Array[String]): Unit = {
    val priceArr = Array(2.5,100,3.5,40,32.5)
    val desArr = Array("铅笔","水杯","笔记本","火腿肠","鼠标")
    for(i <- priceArr.indices){
      bundle.addItem(new SimpleItem(priceArr(i),desArr(i)))
    }

    println("购物车信息如下：")
    bundle.itemList.foreach(item => println("描述："+item.description+",价格："+item.price))
    println("所购物品如下："+bundle.description)
    println("本次购物合计："+bundle.price)
  }
}
~~~

**7.提供一个Square类，其扩展自java.awt.Rectangle并且有三个构造器：一个以给定的端点和宽度构造的正方形，一个以（0,0）为端点和给定的宽度构造正方形，还有一个以（0,0）为端点、0为宽度构造正方形。**

~~~scala
import java.awt.{Point, Rectangle}

class Square extends Rectangle{
  height = 0
  width = 0
  x = 0
  y = 0
  def this(point:Point, width:Int){
    this()
    this.height = width
    this.width = width
    this.x = point.x
    this.y = point.y
  }
  def this(width:Int){
    this(new Point(0,0),width)
  }
}

object Square{
  def main(args: Array[String]): Unit = {
    val rect1 = new Square()
    val rect2=new Square(2)
    val rect3=new Square(new Point(2,3),5)
    println(rect1)
    println(rect2)
    println(rect3)
  }
}
~~~

## 第十章 特质

**1.java.awt.Rectangle类有两个很有用的方法translate和grow，但可惜的是像java.awt.geom.Ellipse2D这样的类中没有。在Scala中，你可以解决掉这个问题。定义一个RectangleLike特质，加入具体的translate和grow方法。提供任何你需要用来实现的抽象方法，以便你可以像如下代码这样混入该特质：**

~~~scala
val egg = new Ellipse2D.Double(5, 10, 15, 20) with RectangleLike
egg.translate(10,-10)
egg.grow(10,21)
~~~

~~~scala
import java.awt.geom.{Ellipse2D}

trait RectangleLike {
  this:Ellipse2D.Double =>
  def translate(dx:Int,dy:Int): Unit ={
    this.x += dx
    this.y += dy
  }

  def grow(w:Int,h:Int): Unit ={
    this.width = w
    this.height = h
  }
}

object One{
  def main(args: Array[String]): Unit = {
    val egg = new Ellipse2D.Double(5, 10, 15, 20) with RectangleLike

    println("x = "+egg.getX+" y = "+egg.getY)
    egg.translate(10,-10)
    println("x = "+egg.getX+" y = "+egg.getY)

    println("w = "+egg.getWidth+" h ="+egg.getHeight)
    egg.grow(10,21)
    println("w = "+egg.getWidth+" h ="+egg.getHeight)
  }
}
~~~

**2.通过把scala.math.Ordered[Point]混入java.awt.Point的方式，定义OrderedPoint类。按照词典顺序排序，也就是说，如果x<x<sup>'</sup>或者x=x<sup>'</sup>且y<y<sup>'</sup>则(x,y)<(x<sup>'</sup>,y<sup>'</sup>)。**

~~~scala
import java.awt.Point

class OrderedPoint(x:Int,y:Int)extends Point(x:Int,y:Int) with Ordered[Point]{
  override def compare(that: Point):Int = {
    if(this.x<= that.x && this.y < that.y) -1
    else if(this.x == that.x && this.y == that.y) 0
    else 1
  }
}

object OrderedPoint{
  def main(args: Array[String]): Unit = {
    val arr : Array[OrderedPoint] = new Array[OrderedPoint](3)
    arr(0) = new OrderedPoint(1,2)
    arr(1) = new OrderedPoint(11,12)
    arr(2) = new OrderedPoint(21,22)
    val sortedArr = arr.sortWith(_ > _)
    sortedArr.foreach((point:OrderedPoint) => println("x="+point.getX+" y="+point.getY))
  }
}
~~~

**4.提供一个CryptoLogger类，将日志消息以凯撒密码加密。缺省情况下密钥为3，不过使用者可以重写它。提供缺省密钥和-3作为密钥时的使用示例。**

~~~scala
class CryptoLogger {
  def crypto(str:String,key:Int=3):String = {
    for (i<-str) yield
      if(key >= 0 ) (97+(i-97+key)%26).toChar
      else (97+(i-97+25+key)%26).toChar
  }
}

object  CryptoLogger{
  def main(args: Array[String]): Unit = {
    val log = new CryptoLogger()

    val plain = "abcdefg"
    println("明文为："+plain)
    println("加密后为："+log.crypto(plain))
    println("加密后为："+log.crypto(plain,-3))
  }
}
~~~

~~~
明文为：abcdefg
加密后为：defghij
加密后为：wxyzabc
~~~

**5.JavaBeans规范里有一种提法叫做“属性变更监听器（property change listener）”，这是bean用来通知其属性变更的标准方式。PropertyChangeSupport类对于任何想要支持属性变更监听器的bean而言是个便捷的超类。但可惜已有其他超类的类（比如JComponent）必须重新实现相应的方法。将PropertyChangeSupport重新实现为一个特质，然后将它混入到java.awt.Point类。**

~~~scala
import java.awt.Point
import java.beans.{PropertyChangeEvent, PropertyChangeListener, PropertyChangeSupport}

trait PropertyChange {
  val pcs:PropertyChangeSupport
}

object Five{
  def main(args: Array[String]): Unit = {
    val p = new Point() with PropertyChange{
      val pcs = new PropertyChangeSupport(this)
      pcs.addPropertyChangeListener(new PropertyChangeListener {
        override def propertyChange(event: PropertyChangeEvent): Unit = {
          println(event.getPropertyName+" oldValue="+event.getOldValue+" newValue="+event.getNewValue)
        }
      })
    }
    val newX:Int=20
    p.pcs.firePropertyChange("x",p.getX,newX)
    p.move(newX,20)
  }
}
~~~

**8.做一个你自己的关于特质的继承层级，要求体现出叠加在一起的特质、具体的和抽象的方法，以及具体的和抽象的字段。**

~~~scala
trait Fly {
  def fly = () => println("flying")
  def flywithnowing()
}
trait Walk{
  def walk() { println("walk") }
}
class Bird{
  var name:String = _
}
class BlueBird extends Bird with Fly with Walk{
  def flywithnowing() {
    println("BlueBird flywithnowing")
  }
}
object Eight{
  def main(args: Array[String]) {
    val b = new BlueBird()
    b.walk()
    b.flywithnowing()
    b.fly()
  }
}
~~~

**9.在java.io类库中，你可以通过BufferedInputStream修饰器来给输入流增加缓冲机制。用特质来重新实现缓冲。简单起见，重写read方法。**

~~~scala
import java.io.{FileInputStream, InputStream}

trait Buffering {
  this:InputStream =>  //自身类型，只能被混入指定类型的子类
  val BUF_SIZE:Int = 5
  val buf:Array[Byte] = new Array[Byte](BUF_SIZE)
  var bufsize :Int=0//缓存数据大小
  var index : Int=0//buf数组的下标

  override def read(): Int = {
    if(index >= bufsize){//读取数据
      bufsize = this.read(buf,0,BUF_SIZE)
      if(bufsize <= 0) return bufsize
      index = 0
    }
    index += 1//移位输出
    buf(index -1)//返回数据
  }
}

object Nine{
  def main(args: Array[String]): Unit = {
    val buf = new FileInputStream("myapp.log") with Buffering
    for (i <- 1 to 20) print(buf.read()+" ")
  }
}
~~~

**10.使用本章的日志生成器特质，给前一个练习中的方案增加日志功能，要求体现出缓冲的效果。**

~~~scala
import java.io.{FileInputStream, InputStream}

trait Logger {
  def log(msg:String)
}

trait PrintLogger extends Logger{
  override def log(msg: String): Unit = println(msg)
}

trait LogBuffering{
  this:InputStream with Logger =>
  val BUF_SIZE:Int = 5
  val buf:Array[Byte] = new Array[Byte](BUF_SIZE)
  var bufsize :Int=0//缓存数据大小
  var position : Int=0//当前位置

  override def read(): Int = {
    if(position >= bufsize){//读取数据
      bufsize = this.read(buf,0,BUF_SIZE)
      if(bufsize <= 0) return bufsize
      log("buffered %d bytes:%s".format(bufsize,buf.mkString(",")))
      position = 0
    }
    position += 1
    buf(position-1)
  }
}

object Ten{
  def main(args: Array[String]): Unit = {
    val f = new FileInputStream("myapp.log")
      with LogBuffering with PrintLogger
    for(i <- 1 to 20) println(f.read()+" ")
  }
}
~~~

**11.实现一个IterableInputStream类，扩展java.io.InputStream并混入Iterable[Byte]特质。**

~~~scala
import java.io.{FileInputStream, InputStream}

trait IterableInputStream extends InputStream with Iterable[Byte] {

  class InputStreamIterator(outer: IterableInputStream) extends Iterator[Byte] {
    def hasNext: Boolean = outer.available() > 0

    def next: Byte = outer.read().toByte
  }

  override def iterator: Iterator[Byte] = new InputStreamIterator(this)
}

object Eleven extends App {
  val in = new FileInputStream("myapp.log")
    with IterableInputStream
  val iterator = in.iterator
  while (iterator.hasNext)  println(iterator.next())
  in.close()
}
~~~

## 第十三章 集合

**1.编写一个函数，给定字符串，产生一个包含所有字符的下标的映射**

~~~scala
def exercise1(){
    indexes("Mississippi").foreach(i => {
        println(i._1+" "+i._2.mkString("{",",","}"))
    })
}
def indexes(s:String):collection.mutable.Map[Char,Set[Int]] = {
    var map = collection.mutable.Map[Char,Set[Int]]()
    s.zipWithIndex.foreach(i => {
        map(i._1) = map.getOrElse(i._1,Set[Int]())+(i._2)
    })
    map
}
//输出
M {0}
s {2,3,5,6}
p {8,9}
i {1,4,7,10}
~~~

~~~scala
def indexes2(s:String):collection.mutable.Map[Char,List[Int]]={
    var map =collection.mutable.Map[Char, List[Int]]()
    s.zipWithIndex.foreach(i => {
        map(i._1)= i._2::map.getOrElse(i._1,List[Int]())
    })
    map
}
//输出
M {0}
s {6,5,3,2}
p {9,8}
i {10,7,4,1}
~~~

**2.重复前一个练习，这次用字符到列表的不可变映射**

~~~scala
def exercise2(){
    indexes3("Mississippi").foreach(i => {
        println(i._1+" "+i._2.mkString("{",",","}"))
    })
}

def indexes3(s:String):collection.immutable.HashMap[String,ArrayBuffer[String]] = {
    var map = new collection.immutable.HashMap[String,ArrayBuffer[String]]
    var index = 0
    for(ch <- s){
        if(!map.contains(ch.toString))
        map += (ch.toString -> new ArrayBuffer[String])
        var arr:ArrayBuffer[String] = map.get(ch.toString).get//Option
        arr += index.toString
        index += 1
    }
    map
}
~~~

**3.从整形链表中去除所有的0**

~~~scala
def exercise3(){
    fun3(LinkedList(1,2,0,3,4,0,5,6,0,1)).foreach(println(_))
}
//比较合理的实现 不过linkedlist已经不赞成使用了
def fun3(linkedList:collection.mutable.LinkedList[Int]):LinkedList[Int]={
    var list = linkedList
    var res = LinkedList[Int]()
    while(list != Nil){
        if(list.elem != 0) res ++= LinkedList(list.elem)
        list = list.next
    }
    res
}
~~~

**4.编写一个函数，接受一个字符串的集合，以及一个从字符串到整数值的映射。返回整型的集合，其值为能和集合中某个字符串相对应的映射的值。举例来说，给定Array("Tom","Fred","Harry")和Map("Tom" -> 3,"Dick" -> 4,"Harry" -> 5)，返回Array(3,5)。提示：用flatMap将get返回的Option值组合在一起。（从一个字符串的数组中去匹配一个[String,Int]的Map，将值也就是Int再生成一个集返回）**

~~~scala
def exercise4(): Unit ={
    var nameArr = Array("Tom","Fred","Harry")
    var nameMap = Map("Tom" -> 3,"Dick" -> 4,"Harry" -> 5)
    println(fun4(nameArr,nameMap).mkString("{",",","}"))
}
def fun4(nameArr:Array[String],nameMap:Map[String,Int]) = {
    var res = Set[Int]()
    nameArr.filter(i => nameMap.keys.toList.contains(i)).foreach(index => {
        res ++= nameMap.get(index).toList
    })
    res
}
~~~

**5.实现一个函数，作用域mkString相同，使用reduceLeft。**

~~~scala
def exercise5(): Unit ={
    val list = List[Int](1,2,3,4,5,6)
    println(fun5(list,"{",",","}"))
}
def fun5(seq:List[Any]):String = seq.reduceLeft(_ +" "+ _).toString
def fun5(seq:List[Any],str:String):String = seq.reduceLeft(_+str+_).toString
def fun5(seq:List[Any],start:String,str:String,end:String):String =	start+seq.reduceLeft(_+str+_).toString+end
~~~

**6.给定整型列表lst，（lst :\ List[Int]\(\)）(_ :: \_)得到什么？(List[Int]\(\) /: lst)(_ :+ _)又得到什么？如何修改它们中的一个，以对原列表进行反向排列？**

结果是一样的顺序序列，不一样的表达 

~~~scala
def exercise6(): Unit ={
    val list = List(1,2,3,4,5,6,7)
    //两个结果一样只不过是不同的表达方式而已，都是重新建了一个一样的list
    (list:\List[Int]())(_ :: _).foreach(print)
    println()
    (List[Int]() /: list)(_ :+ _).foreach(print)
    println()
    //反向排列
    (List[Int]() /: list.toVector.reverse.toList)((a,b) => {a:+b}).foreach(print(_))
}
//结果
1234567
1234567
7654321
~~~

**7.在13.10节中，表达式(prices zip quantities) map { p => p._1 * p.\_2}有些不够优雅。我们不能用(prices zip quantities) map { _ * \_}，因为\_ * _是一个带两个参数的函数，而我们需要的是一个带单个类型为元组的参数的函数。Function对象的tupled方法可以将带两个参数的函数改为以元组为参数的函数。将tupled应用于乘法函数，以便我们可以用它来映射由对偶组成的列表。（利用Function.tuple实现map接受一个元组）**

~~~scala
def exercise7(): Unit ={
    val prices = List(5.0,20.0,9.99)
    val quantities = List(10,2,1)
    (prices zip quantities).map(Function.tupled(_ * _)).foreach(println)
}
~~~

**8.编写一个函数，将Double数组转换成二维数组。传入列数作为参数。举例来说，Array（1，2，3，4，5，6）和三列，返回Array（Array（1,2,3），Array（4,5,6））。用grouped方法。**

~~~scala
def exercise8(): Unit ={
    val arr = Array[Int](1,2,3,4,5,6)
    val res = fun8(arr,3)
    for(a <- res) println(a.mkString(" "))
}

def fun8(arr:Array[Int],num:Int)={
    arr.grouped(num)
}
~~~

## 附：林子雨《Spark编程基础》Scala容器相关操作

### 1.遍历操作（foreach for）

**List**

~~~scala
scala> val list = List(1,2,3)
list: List[Int] = List(1, 2, 3)

scala> val f=(i:Int)=>println(i)
f: Int => Unit = <function1>

scala> list foreach f
scala> list foreach(i=>println(i))
scala> list foreach println
1
2
3
for(i<-list) println(i)
~~~

**Map**

~~~scala
map foreach{kv => println(kv._1+":"+kv._2)}
map foreach{case (k,v) => println(k+":"+v)}
map foreach{x=>x match {case (k,v) => println(k+":"+v)}
for(kv <- map) println(kv._1+":"+kv._2)
for((k,v) <= map) println(k+":"+v)
~~~

### 2.映射操作（map flatMap）

**map：将某个函数应用到集合中的每个元素，映射得到一个新元素**

~~~scala
scala> val books = List("Hadoop","Hive","HDFS")
books: List[String] = List(Hadoop, Hive, HDFS)

scala> books.map(s => s.toUpperCase)
res7: List[String] = List(HADOOP, HIVE, HDFS)

scala> books.map(s => s.length)
res8: List[Int] = List(6, 4, 4)
~~~

**flatMap：将某个函数应用到容器中的元素时，对每个元素都会返回一个容器（而不是一个元素），然后flatMap把生成的多个容器“拍扁”成为一个容器并返回。**

~~~scala
scala> books.flatMap(s => s.toList)  //toList,toSet,toMap
res9: List[Char] = List(H, a, d, o, o, p, H, i, v, e, H, D, F, S)
~~~

### 3.过滤操作（filter filterNot exists find）

**filter：遍历一个容器，从中获取满足指定条件的元素，返回一个新的容器。**

~~~scala
scala> val map = Map(1 -> 1,2 -> 2)
scala> map filter {kv => kv._2 < 2}
res15: scala.collection.immutable.Map[Int,Int] = Map(1 -> 1)
~~~

**exists**

~~~scala
scala> books exists {_ startsWith "H"}
res17: Boolean = true
~~~

**find**

~~~scala
scala> books find {_ startsWith "H"}
res18: Option[String] = Some(Hadoop)
~~~

### 4.规约操作（reduce reduceLeft reduceRight fold foldLeft foldRight）

**reduce：规约操作是对容器的元素进行两两运算，将其“规约”为一个值。**

~~~scala
scala> list
res19: List[Int] = List(1, 2, 3)

scala> list.reduce(_ + _)
res20: Int = 6

scala> list map (_.toString) reduce ((x,y) => s"f($x,$y)")
res22: String = f(f(1,2),3) //f表示传入reduce的二元函数
~~~

**reduceLeft：保证遍历顺序，第一个参数表示累计值**

~~~scala
scala> val list = List(1,2,3,4,5)
list: List[Int] = List(1, 2, 3, 4, 5)

scala> list reduceLeft(_ - _)
res23: Int = -13

scala> val s = list map (_.toString)	//将整型列表转换成字符串列表
s: List[String] = List(1, 2, 3, 4, 5)

scala> s reduceLeft {(accu,x)=>s"($accu-$x)"}
res24: String = ((((1-2)-3)-4)-5)
~~~

**reduceRight：保证遍历顺序，第二个参数表示累计值**

~~~scala
scala> list reduceRight (_ - _)
res26: Int = 3

scala> s reduceRight {(x,accu)=>s"($x-$accu)"}
res28: String = (1-(2-(3-(4-5))))
~~~

**fold：与reduce方法非常类似，是一个双参数列表的函数，第一个参数列表接受一个规约的初始值，第二个参数列表接受与reduce中一样的的二元函数参数。reduce是从容器的两个元素开始规约，而fold则是从提供的初始值开始规约。**

~~~scala
scala> list
res29: List[Int] = List(1, 2, 3, 4, 5)

scala> list.fold(10)(_ * _)
res30: Int = 1200

scala> (list fold 10)(_ * _)	//fold的中缀调用写法

scala> (list foldLeft 10)(_ - _)	//计算顺序(((((10-1)-2)-3)-4)-5)
res32: Int = -5

scala> (list foldRight 10)(_ - _)	//计算顺序(1-(2-(3-(4-(5-10)))))
res33: Int = -7
~~~

对空容器fold的结果为初始值，对空容器调用reduce会报错。

reduce操作总是返回于容器元素相同类型的结果，但是fold操作可以输出与容器元素类型完全不同类型的值，甚至是一个新容器。

~~~scala
//fold实现类似map的功能
scala> val list = List(1,2,3,4,5)
list: List[Int] = List(1, 2, 3, 4, 5)

scala> (list foldRight List.empty[Int]){(x,accu)=>x*2::accu}
res1: List[Int] = List(2, 4, 6, 8, 10)

scala> list map {_*2}
res2: List[Int] = List(2, 4, 6, 8, 10)
~~~

### 5.拆分操作（partition groupBy grouped sliding）

拆分操作是把一个容器里的元素按一定规则分割成多个子容器。

**partition：接受一个布尔函数，用该函数对容器元素进行遍历，以二元组的形式返回满足条件和不满足条件的两个C[T]类型的集合。**

~~~scala
scala> val list = List(1,2,3,4,5)

scala> list.partition(_<3)
res3: (List[Int], List[Int]) = (List(1, 2),List(3, 4, 5))
~~~

**groupBy：接受一个返回U类型的函数，用该函数对容器元素进行遍历，将返回值相同的元素作为一个子容器，并与该相同的值构成一个键值对，最后返回的是一个类型为Map[U,C[T]]的映射。**

~~~scala
scala> val gby = list.groupBy(x=>x%3)	//按被3整除的余数进行划分
gby: scala.collection.immutable.Map[Int,List[Int]] = Map(2 -> List(2, 5), 1 -> List(1, 4), 0 -> List(3))

scala> gby(2)	//获取键值为2（余数为2）的子容器
res4: List[Int] = List(2, 5)
~~~

**grouped和sliding方法都接受一个整型参数n，两个方法都将容器拆分为多个与原容器类型相同的子容器，并返回由这些子容器构成的迭代器，即Iterator[C[T]]。**

**grouped按从左到右的方式将容器划分为多个大小为n的子容器（最后一个的大小可能小于n）。**

**sliding使用一个长度为n的滑动窗口，从左到右将容器截取为多个大小为n的子容器。**

~~~scala
scala> list.grouped(3)	//拆分大小为3的子容器
res5: Iterator[List[Int]] = non-empty iterator

scala> res5.next	//第一个容器
res6: List[Int] = List(1, 2, 3)

scala> res5.next	//第二个容器
res7: List[Int] = List(4, 5)

scala> res5.hasNext	//已经迭代完
res8: Boolean = false
~~~

~~~scala
scala> list.sliding(3)	//滑动拆分大小为3的子容器
res9: Iterator[List[Int]] = non-empty iterator

scala> res9.next	//第一个容器
res10: List[Int] = List(1, 2, 3)

scala> res9.next	//第二个容器
res11: List[Int] = List(2, 3, 4)

scala> res9.next	//第三个容器
res12: List[Int] = List(3, 4, 5)

scala> res9.next	//下一个元素空，迭代报错
java.util.NoSuchElementException: next on empty iterator
  at scala.collection.Iterator$GroupedIterator.next(Iterator.scala:1138)
  at scala.collection.Iterator$GroupedIterator.next(Iterator.scala:1019)
  at scala.collection.Iterator$$anon$11.next(Iterator.scala:409)
  ... 32 elided

scala> res9.hasNext	//迭代器已经遍历完
res14: Boolean = false
~~~

### 6.函数式编程实例（单词统计）

~~~scala
import java.io.{File, PrintWriter}

import scala.io.Source
import collection.mutable.Map

object WordCount {
	def main(args:Array[String]){
		val dirFile = new File("F:/logs")
		val files = dirFile.listFiles()
        //可变的空的映射(Map)对象，保存统计结果。key是单词，value是次数
		val results = Map.empty[String,Int] 
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




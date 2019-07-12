---
title: Java集合
categories:
  - Java
tags: 面试
description: Java集合
abbrlink: '4509351'
date: 2019-07-12 09:37:59
keywords:
---

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4wt3plphhj20kr0s4go6.jpg)

## 1.Collection 和 Collections 有什么区别？

- java.util.Collection 是一个集合接口（集合类的一个顶级接口）。它提供了对集合对象进行基本操作的通用接口方法。Collection接口在Java 类库中有很多具体的实现。Collection接口的意义是为各种具体的集合提供了最大化的统一操作方式，其直接继承接口有List与Set。
- Collections则是集合类的一个工具类/帮助类，其中提供了一系列静态方法，用于对集合中元素进行排序、搜索以及线程安全等各种操作。

## 2.List、Set、Map 之间的区别是什么？

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4wtltzg4nj20nu0at0tk.jpg)

## 3.Arraylist默认分配多少？如果数据已满，自动增长多少？

默认分配10，当前容量*1.5

~~~java
/**
  * Default initial capacity.
  */
private static final int DEFAULT_CAPACITY = 10;

/**
 * Increases the capacity to ensure that it can hold at least the
 * number of elements specified by the minimum capacity argument.
 *
 * @param minCapacity the desired minimum capacity
 */
private void grow(int minCapacity) {
	// overflow-conscious code
	int oldCapacity = elementData.length;
	int newCapacity = oldCapacity + (oldCapacity >> 1);
	if (newCapacity - minCapacity < 0)
		newCapacity = minCapacity;
	if (newCapacity - MAX_ARRAY_SIZE > 0)
		newCapacity = hugeCapacity(minCapacity);
	// minCapacity is usually close to size, so this is a win:
	elementData = Arrays.copyOf(elementData, newCapacity);
}
~~~

## 4.Hashmap的实现原理

　　HashMap是基于哈希表的Map接口的非同步实现。此实现提供所有可选的映射操作，并允许使用null键和null值。此类不保证映射的顺序，特别是它不保证该顺序恒久不变。 HashMap使用“链表散列”数据结构，即数组和链表的结合体。

　　当我们往Hashmap中put元素时，首先根据key的hashcode重新计算hash值，根据hash值得到这个元素在数组中的位置(下标)，如果该数组在该位置上已经存放了其他元素，那么在这个位置上的元素将以链表的形式存放，新加入的放在链头，最先加入的放入链尾。Jdk 1.8中对HashMap的实现做了优化，当链表中的节点数据超过八个之后，该链表会转为红黑树来提高查询效率，从原来的O(n)到O(logn)。

## 5.HashMap 和 Hashtable 有什么区别？

- hashMap去掉了HashTable 的contains方法，但是加上了containsValue（）和containsKey（）方法。
- hashTable是同步的，而HashMap是非同步的，效率上比hashTable要高。
- hashMap允许空键值，而hashTable不允许。

## 6.如何决定使用 HashMap 还是 TreeMap？

　　对于在Map中插入、删除和定位元素这类操作，HashMap是最好的选择。然而，假如你需要对一个有序的key集合进行遍历，TreeMap是更好的选择。TreeMap是SortedMap接口基于红黑树的实现，该类保证了映射按照升序排列关键字。

## 7.说一下 HashSet 的实现原理？

- HashSet底层由HashMap实现
- HashSet的值存放于HashMap的key上
- HashMap的value统一为PRESENT

## 8.ArrayList 和 LinkedList 的区别是什么？

　　ArrrayList底层的数据结构是数组，支持随机访问。而 LinkedList 的底层数据结构是双向循环链表，不支持随机访问。使用下标访问一个元素，ArrayList 的时间复杂度是 O(1)，而 LinkedList 是 O(n)。

## 9.在 Queue 中 poll()和 remove()有什么区别？

　　poll() 和 remove() 都是从队列中取出一个元素，但是 poll() 在获取元素失败的时候会返回空，但是 remove() 失败的时候会抛出异常。

## 10.ArrayDeque

就其实现而言，ArrayDeque采用了循环数组的方式来完成双端队列的功能。 

1. 无限的扩展，自动扩展队列大小的。（当然在不会内存溢出的情况下。） 
2. 非线程安全的，不支持并发访问和修改。 
3. 支持fast-fail. 
4. 作为栈使用的话比比栈要快. 
5. 当队列使用比linklist要快。 
6. null元素被禁止使用。

> 1.添加元素
>         addFirst(E e)　　在deque前面添加元素
>         addLast(E e)　　在deque后面添加元素
>         offerFirst(E e) 　在deque前面添加元素，并返回是否添加成功
>         offerLast(E e)　在deque后面添加元素，并返回是否添加成功
>
> 2.删除元素
>         removeFirst()		删除第一个元素，并返回删除元素的值，如果元素为null，将抛出异常
>         pollFirst()				删除第一个元素，并返回删除元素的值，如果元素为null，将返回null
>         removeLast()		删除最后一个元素，并返回删除元素的值，如果为null，将抛出异常
>         pollLast()				删除最后一个元素，并返回删除元素的值，如果为null，将返回null
>         removeFirstOccurrence(Object o) 删除第一次出现的指定元素
>         removeLastOccurrence(Object o) 删除最后一次出现的指定元素
>
> 3.获取元素
>         getFirst() 		获取第一个元素,如果没有将抛出异常
>         getLast() 		获取最后一个元素，如果没有将抛出异常
>
> 4.队列操作
>         add(E e) 		在队列尾部添加一个元素
>         offer(E e) 		在队列尾部添加一个元素，并返回是否成功
>         remove() 		删除队列中第一个元素，并返回该元素的值，如果元素为null，将抛出异常(其实底层调用的是removeFirst())
>         poll()  		删除队列中第一个元素，并返回该元素的值,如果元素为null，将返回null(其实调用的是pollFirst())
>         element() 	获取第一个元素，如果没有将抛出异常
>         peek() 		获取第一个元素，如果返回null
>
> 5.栈操作
>         push(E e) 	栈顶添加一个元素
>         pop(E e) 	移除栈顶元素,如果栈顶没有元素将抛出异常
>
> 6.其他
>         size() 获取队列中元素个数
>         isEmpty() 判断队列是否为空
>         iterator() 迭代器，从前向后迭代
>         descendingIterator() 迭代器，从后向前迭代
>         contain(Object o) 判断队列中是否存在该元素
>         toArray() 转成数组
>         clear() 清空队列
>         clone() 克隆(复制)一个新的队列

## 11.哪些集合类是线程安全的？

- Vector：就比arraylist多了个同步化机制（线程安全），因为效率较低，现在已经不太建议使用。在web应用中，特别是前台页面，往往效率（页面响应速度）是优先考虑的。
- Stack：堆栈类，先进后出。
- HashTable：就比HashMap多了个线程安全。
- Enumeration：枚举，相当于迭代器。

## 12.迭代器 Iterator 是什么？

　　迭代器是一种设计模式，它是一个对象，可以遍历并选择序列中的对象。而开发人员不需要了解该序列的底层结构。迭代器通常被称为“轻量级”对象，因为创建它的代价小。


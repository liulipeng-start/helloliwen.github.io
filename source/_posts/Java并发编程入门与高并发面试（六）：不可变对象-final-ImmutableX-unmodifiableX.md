---
title: Java并发编程入门与高并发面试（六）：不可变对象 -final -ImmutableX -unmodifiableX
categories:
  - 高并发
abbrlink: e471f102
date: 2018-09-14 22:30:14
tags:
---

摘要：不可变对象一定是线程安全的，但是线程安全的对象不一定是不可变对象。本文介绍不可变对象的概率，如何创建一个不可变对象，final关键字，Java:unmodifiable相关方法和Google的Guava。

<!--more-->

------

#### 1、不可变对象

有一种对象只要它发布了就是安全的，它就是不可变对象。一个不可变对象需要满足的条件：

- 对象创建一个其状态不能修改

- 对象所有域都是final类型

- 对象是正确创建的(在对象创建期间，this引用没有逸出)


#### 2、创建一个不可变对象的方法

（1）自己定义 
这里可以采用的方式包括： 
1、将类声明为final，这样它就不能被继承。 
2、将所有的成员声明为私有的，这样就不允许直接访问这些成员。 
3、对变量不提供set方法，将所有可变的成员声明为final，这样就只能赋值一次。通过构造器初始化所有成员进行深度拷贝。 
4、在get方法中不直接返回对象的本身，而是克隆对象，返回对象的拷贝。

（2）使用Java中提供的Collection类中的各种unmodifiable开头的方法 
（3）使用Guava中的Immutable开头的类

#### 3、final关键字

final关键字可以修饰类、修饰方法、修饰变量

- 修饰类：类不能被集成。 
  基础类型的包装类都是final类型的类。final类中的成员变量可以根据需要设置为final，但是要注意的是，final类中的所有成员方法都会被隐式的指定为final方法
- 修饰方法： 
  (1)把方法锁定，以防任何继承类修改它的含义 
  (2)效率：在早期的java实现版本中，会将final方法转为内嵌调用。但是如果方法过于庞大，可能看不见效果。一个private方法会被隐式的指定为final方法
- 修饰变量： 
  基本数据类型变量，在初始化之后，它的值就不能被修改了。如果是引用类型变量，在它初始化之后便不能再指向另外的对象。 
  ![这里写图片描述](https://img-blog.csdn.net/20180411153701945?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

从上图我们可见， 
（1）对一个被final修饰的变量（Integer a、String b）被赋值时在编译过程中就出现了错误。 
（2）（map）在重新被指向一个新的map对象的时候也出现了错误。 
那么对被定义为final的map进行赋值呢？我们单独运行map.put(1,3)语句，结果是可以的。**被final修饰的引用类型变量，虽然不能重新指向，但是可以修改**,这一点尤为要注意。 
![这里写图片描述](https://img-blog.csdn.net/2018041115395523?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
（3）当final修饰方法的参数时：同样也是不允许在方法内部对其修改的。 
![这里写图片描述](https://img-blog.csdn.net/20180411154323558?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### 4、Java:unmodifiable相关方法

使用Java的Collection类的unmodifiable相关方法，可以创建不可变对象。unmodifiable相关方法包含：Collection、List、Map、Set…. 
![这里写图片描述](https://img-blog.csdn.net/20180411162003137?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 
举个栗子：

```
public class ImmutableExample {

    private static Map<Integer, Integer> map = Maps.newHashMap();

    static {
        map.put(1, 2);
        map.put(3, 4);
        map.put(5, 6);
        map = Collections.unmodifiableMap(map);
    }

    public static void main(String[] args) {
        map.put(1, 3);
        log.info("{}", map.get(1));
    }

}1234567891011121314151617
```

上面程序的执行结果为：在map.put（1，3）操作的位置抛出了异常。由此可见map对象已经成为不可变对象。 
![这里写图片描述](https://img-blog.csdn.net/20180411162457565?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

那么unmodifiable相关类的实现原理是什么呢？我们查看一下Collections.unmodifiableMap的源码：（以下源码只筛选出表达观点的部分，非全部）

```
public static <K,V> Map<K,V> unmodifiableMap(Map<? extends K, ? extends V> m) {
        return new UnmodifiableMap<>(m);
}
private static class UnmodifiableMap<K,V> implements Map<K,V>, Serializable {
    ...
    public V put(K key, V value) {
        throw new UnsupportedOperationException();
    }
    ...
}12345678910
```

Collections.unmodifiableMap在执行时，将参数中的map对象进行了转换，转换为Collection类中的内部类 UnmodifiableMap对象。而 UnmodifiableMap对map的**更新方法**（比如put、remove等）进行了重写，均返回UnsupportedOperationException异常，这样就做到了map对象的不可变。

#### 5、Guava:Immutable相关类

使用Guava的Immutable相关类也可以创建不可变对象。同样包含很多类型：Collection、List、Map、Set…. 
![这里写图片描述](https://img-blog.csdn.net/20180411165721456?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 
举栗子： 
（1）ImmutableList

```
private final static ImmutableList<Integer> list = ImmutableList.of(1, 2, 3);

list.add(4);//这一句在书写完就会被IDE提示该add方法为过时方法，实际为不可用方法123
```

对于ImmutableList.of方法，如果传多个参数，需要这样一直写下去，以逗号分隔每个参数。其源码中是这样实现的：

```
    //单个或少于12个参数时
    public static <E> ImmutableList<E> of() {
        return RegularImmutableList.EMPTY;
    }

    public static <E> ImmutableList<E> of(E element) {
        return new SingletonImmutableList(element);
    }

    public static <E> ImmutableList<E> of(E e1, E e2) {
        return construct(e1, e2);
    }

    public static <E> ImmutableList<E> of(E e1, E e2, E e3) {
        return construct(e1, e2, e3);
    }
    ....

    //多于12个参数时，参数列表中最后的E...other会以数组形式接收参数
    @SafeVarargs
    public static <E> ImmutableList<E> of(E e1, E e2, E e3, E e4, E e5,
     E e6, E e7, E e8, E e9, E e10, E e11, E e12, E... others) {
        Object[] array = new Object[12 + others.length];
        array[0] = e1;
        array[1] = e2;
        array[2] = e3;
        array[3] = e4;
        array[4] = e5;
        array[5] = e6;
        array[6] = e7;
        array[7] = e8;
        array[8] = e9;
        array[9] = e10;
        array[10] = e11;
        array[11] = e12;
        System.arraycopy(others, 0, array, 12, others.length);
        return construct(array);
    }1234567891011121314151617181920212223242526272829303132333435363738
```

运行结果仍然为抛出UnsupportedOperationException异常。 
分析源码：Immutable相关类使用了跟Java的unmodifiable相关类相似的实现方法。

```
/** @deprecated */
    @Deprecated
    @CanIgnoreReturnValue
    public final boolean add(E e) {
        throw new UnsupportedOperationException();
    }123456
```

（2）ImmutableSet 
ImmutableSet除了使用of的方法进行初始化，还可以使用copyof方法，将Collection类型、Iterator类型作为参数。

```
private final static ImmutableSet set = ImmutableSet.copyOf(list);

private final static ImmutableSet set = ImmutableSet.copyOf(list.iterator());123
```

（3）ImmutableMap 
ImmutableMap有特殊的builder写法：

```
 private final static ImmutableMap<Integer, Integer> map = ImmutableMap.of(1, 2, 3, 4);

 private final static ImmutableMap<Integer, Integer> map2 = ImmutableMap.<Integer, Integer>builder()
            .put(1, 2).put(3, 4).put(5, 6).build();
```

经作者授权转载，原文地址：https://blog.csdn.net/jesonjoke/article/details/79897550
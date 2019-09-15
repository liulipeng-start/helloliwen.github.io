---
title: Mybatis&Hibernate
categories: Java
tags: 面试
description: ORM框架集锦
abbrlink: 1cde7558
date: 2019-07-23 15:45:00
keywords:
---

## 1.Mybatis中 #{} 和 ${} 的区别

1. \#{}是预编译处理，${}是字符串替换；

2. Mybatis在处理#{}时，会将sql中的#{}替换为?号，调用PreparedStatement的set方法来赋值；

3. Mybatis在处理\${}时，就是把​\${}替换成变量的值；

4. 使用#{}可以有效的防止SQL注入，提高系统安全性。

## 2.实体类中的属性名和表中的字段名不一样的处理方式

1.  通过在查询的sql语句中**定义字段名的别名，让字段名的别名和实体类的属性名一致**
2. **通过`<resultMap>`来映射字段名和实体类属性名的一一对应的关系**

~~~java
<select id="getOrder" parameterType="int" resultMap="orderresultmap">
        select * from orders where order_id=#{id}
</select>
<resultMap type=”me.gacl.domain.order” id=”orderresultmap”> 
        <!–用id属性来映射主键字段–> 
        <id property=”id” column=”order_id”> 
        <!–用result属性来映射非主键字段，property为实体类属性名，column为数据表中的属性–> 
        <result property = “orderno” column =”order_no”/> 
        <result property=”price” column=”order_price” /> 
</reslutMap>
~~~

## 3.如何获取自动生成的(主)键值

　　第一是在数据库获得通过自带方法。在数据插入之后输入“select @@indentity”通常需要结合存储过程，比较复杂。 第二是在后台插入时获得。这里我们主要说后台刚插入时得到主键值。

　　**对于支持自动生成主键的数据库，如Mysql、sqlServer，可以通过 Mybatis元素useGeneratedKeys返回当前插入数据主键值到输入类中。**

~~~xml
<insert id="insertTest" useGeneratedKeys="true" keyProperty="id" 
　parameterType="com.kq.domain.IdentityTest">
        insert into identity_test(name)
        values(#{name,jdbcType=VARCHAR})
</insert>
~~~

　　当执行此条插入语句以后，实体类IdentityTest中的Id会被当前插入数据的主键自动填充。

　　**对于不支持自动生成主键的数据库。Oracle、DB2等，可以用元素selectKey 回当前插入数据主键值到输入类中。（同时生成一个自定义的随机主键）**

　　通过LAST_INSERT_ID()获取刚插入记录的自增主键值，**在insert语句执行后，执行select LAST_INSERT_ID()就可以获取自增主键。**

~~~xml
<insert id="insertTest" useGeneratedKeys="true" keyProperty="id" 
　parameterType="com.kq.domain.IdentityTest">
　<selectKey keyProperty="id" resultType="String" order="BEFORE">
        SELECT  REPLACE(UUID(),'-','')  
  </selectKey>
        insert into identity_test(name)
        values(#{name,jdbcType=VARCHAR})
</insert>
~~~

　　当执行此条插入语句以后，实体类IdentityTest中的Id也会被当前插入数据的主键自动填充。

## 4.在mapper中如何传递多个参数

1. 使用占位符的思想。在映射文件中使用#{0},#{1}代表传递进来的第几个参数（如果使用的是JDK8的话，那么会有Bug），使用@param注解:来命名参数，#{0},#{1}方式
2. 使用Map集合作为参数来装载

## 5.Mybatis不同的XML映射文件，id是否可以重复

　　如果配置了namespace那么当然是可以重复的，因为我们的Statement实际上就是namespace+id。

　　如果没有配置namespace的话，那么相同的id就会导致覆盖了。

## 6.Xml映射文件对应的Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？

- Dao接口，就是人们常说的Mapper接口。接口的全限名，就是映射文件中的namespace的值，接口的方法名，就是映射文件中MappedStatement的id值，接口方法内的参数，就是传递给sql的参数。

- Mapper接口是没有实现类的，当调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MappedStatement。

Dao接口里的方法，**是不能重载的，因为是全限名+方法名的保存和寻找策略**。

**Dao接口的工作原理是JDK动态代理，Mybatis运行时会使用JDK动态代理为Dao接口生成代理proxy对象，代理对象proxy会拦截接口方法，转而执行MappedStatement所代表的sql，然后将sql执行结果返回。**

## 7.Mybatis分页方式

　　数组分页、sql分页、拦截器分页、RowBounds分页

　　Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页。可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。

　　**分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql。根据dialect方言，添加对应的物理分页语句和物理分页参数。**

## 8.Mybatis物理分页和逻辑分页的区别

- 两者在速度上，没有谁比谁快之分；
- 物理分页总是优于逻辑分页：没有必要将属于数据库端的压力加诸到应用端来，就算速度上存在优势，然而其它性能上的优点足以弥补这个缺点。

## 9.Mybatis 是否支持延迟加载？延迟加载的原理是什么？

　　Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。

　　它的原理是，**使用CGLIB创建目标对象的代理对象**，当调用目标方法时，进入拦截器方法。比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来。然后调用a.setB(b)，于是a的对象b属性就有值了。接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。

　　当然了，不光是Mybatis，几乎所有的包括Hibernate，支持延迟加载的原理都是一样的。

## 10.Mybatis 的一级缓存和二级缓存

　　一级缓存: 基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当 Session flush 或 close 之后，该 Session 中的所有 Cache 就将清空，默认打开一级缓存。

　　二级缓存与一级缓存其机制相同，默认也是采用PerpetualCache的HashMap 存储。不同在于其存储作用域为 Mapper(Namespace)，并且可自定义存储源，如Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态)，可在它的映射文件中配置`<cache/>`。

　　对于缓存数据更新机制，当某一个作用域(一级缓存 Session/二级缓存Namespaces)的进行了C/U/D 操作后，默认该作用域下所有 select 中的缓存将被 clear。

## 11.Mybatis执行器（Executor）

Mybatis有三种基本的Executor执行器，**SimpleExecutor、ReuseExecutor、BatchExecutor**：

1. **SimpleExecutor**：每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象。

2. **ReuseExecutor**：执行update或select，以sql作为key查找Statement对象，存在就使用，不存在就创建。用完后，不关闭Statement对象，而是放置于Map内，供下一次使用。简言之，**就是重复使用Statement对象**。

3. **BatchExecutor**：执行update（没有select，JDBC批处理不支持select），将所有sql都添加到批处理中（addBatch()），等待统一执行（**executeBatch()**）。它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理。与JDBC批处理相同。

作用范围：Executor的这些特点，都严格限制在SqlSession生命周期范围内。

## 12.Mybatis 分页插件的实现原理

　　分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件。在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。

## 13.Mybatis 编写自定义插件

　　Mybatis自定义插件针对Mybatis四大对象（Executor、StatementHandler 、ParameterHandler 、ResultSetHandler ）进行拦截，具体拦截方式为： 

1. Executor：拦截执行器的方法(log记录) 

2. StatementHandler ：拦截Sql语法构建的处理 

3. ParameterHandler ：拦截参数的处理 

4. ResultSetHandler ：拦截结果集的处理 

前两种应用较为广泛。

Mybatis自定义插件必须实现Interceptor接口：

~~~java
public interface Interceptor {
    Object intercept(Invocation invocation) throws Throwable;
    Object plugin(Object target);
    void setProperties(Properties properties);
}
~~~

intercept方法：拦截器具体处理逻辑方法 

plugin方法：根据签名signatureMap生成动态代理对象 

setProperties方法：设置Properties属性

[Mybatis自定义插件](https://blog.csdn.net/qq_30051265/article/details/80266434)

## 14.Mybatis 和 Hibernate 的区别

1. Hibernate属于全自动ORM映射工具，使用Hibernate查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的。而Mybatis在查询关联对象或关联集合对象时，需要手动编写sql来完成，所以，称之为半自动ORM映射工具。

2. Mybatis直接编写原生态sql，可以严格控制sql执行性能，灵活度高，非常适合对关系数据模型要求不高的软件开发。因为这类软件需求变化频繁，一旦需求变化要求迅速输出成果。但是灵活的前提是mybatis无法做到数据库无关性，如果需要实现支持多种数据库的软件，则需要自定义多套sql映射文件，工作量大。 

3. Hibernate对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件。如果用hibernate开发可以节省很多代码，提高效率。 


---
title: Java Web
categories:
  - Java
tags: 面试
description: Java Web
abbrlink: fb60fea1
date: 2019-07-15 17:00:11
keywords:
---

## 1.JSP和Servlet的区别

1. jsp经编译后就变成了servlet。（jsp的本质就是servlet。JVM只能识别java的类，不能识别jsp的代码，Web容器将JSP的代码编译成JVM能够识别的java类）
2. jsp更擅长表现于页面显示，servlet更擅长于逻辑控制。
3. servlet中没有内置对象，jsp中的内置对象必须通过HttpServletRequest对象、HttpServletResponse对象以及HttpServlet对象得到。
4. jsp是servlet的一种简化，使用jsp只需要完成输出到客户端的内容。Jsp中的Java脚本如果镶嵌到一个类中，由Jsp容器完成。而Servlet则是个完整的Java类，这个类的Service方法用于生成对客户端的响应。

## 2.JSP的内置对象及作用

JSP有9个内置对象：

- request：封装客户端的请求，其中包含来自GET或POST请求的参数；
- response：封装服务器对客户端的响应；
- pageContext：通过该对象可以获取其他对象；
- session：封装用户会话的对象；
- application：封装服务器运行环境的对象；
- out：输出服务器响应的输出流对象；
- config：Web应用的配置对象；
- page：JSP页面本身（相当于Java程序中的this）；
- exception：封装页面抛出异常的对象。

## 3. JSP的 4 种作用域

JSP中的四种作用域包括page、request、session和application，具体来说：

- **page**：与一个页面相关的对象和属性。
- **request**：与Web客户机发出的一个请求相关的对象和属性。一个请求可能跨越多个页面，涉及多个Web组件；需要在页面显示的临时数据可以置于此作用域。
- **session**：与某个用户与服务器建立的一次会话相关的对象和属性。跟某个用户相关的数据应该放在用户自己的session中。
- **application**：与整个Web应用程序相关的对象和属性，它实质上是跨越整个Web应用程序，包括多个页面、请求和会话的一个全局作用域。

## 4.session和cookie的区别

由于HTTP协议是无状态的协议，所以服务端需要记录用户的状态时，就需要用某种机制来识具体的用户，这个机制就是session。典型的场景比如购物车，当你点击下单按钮时，由于HTTP协议无状态，所以并不知道是哪个用户操作的，所以服务端要为特定的用户创建了特定的session，用用于标识这个用户，并且跟踪用户，这样才知道购物车里面有几本书。这个session是保存在服务端的，有一个唯一标识。在服务端保存session的方法很多，**内存、数据库、文件**都有。集群的时候也要考虑session的转移，在大型的网站，一般会有专门的session服务器集群，用来保存用户会话，这个时候 session 信息都是放在内存的，使用一些缓存服务比如Memcached之类的来放 session。

思考一下服务端如何识别特定的客户？这个时候cookie就登场了。每次HTTP请求的时候，客户端都会发送相应的cookie信息到服务端。实际上大多数的应用都是用 cookie 来实现session跟踪的，第一次创建session的时候，服务端会在HTTP协议中告诉客户端，需要在 cookie 里面记录一个session ID，以后每次请求把这个会话ID发送到服务器，我就知道你是谁了。有人问，如果客户端的浏览器禁用了 cookie 怎么办？一般这种情况下，会使用一种叫做**URL重写**的技术来进行会话跟踪，即每次HTTP交互，URL后面都会被附加上一个诸如 sid=xxxxx 这样的参数，服务端据此来识别用户。

cookie其实还可以用在一些方便用户的场景下，设想你某次登陆过一个网站，下次登录的时候不想再次输入账号了，怎么办？这个信息可以写到cookie里面，访问网站的时候，网站页面的脚本可以读取这个信息，就自动帮你把用户名给填了，能够方便一下用户。这也是cookie名称的由来，给用户的一点甜头。所以，总结一下：<font color="red">session是在服务端保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中；cookie是客户端保存用户信息的一种机制，用来记录用户的一些信息，也是实现session的一种方式。</font>

区别：

1. cookie数据存放在客户的浏览器（客户端）上，session数据放在服务器上，但是服务端的session的实现对客户端的cookie有依赖关系的；

2. cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗，考虑到安全应当使用session；

3. session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能。考虑到减轻服务器性能方面，应当使用COOKIE；

4. 单个cookie在客户端的限制是3K，就是说一个站点在客户端存放的COOKIE不能超过3K；

## 5.session的工作原理

　　session是一个存在服务器上的类似于一个散列表格的文件。里面存有我们需要的信息，在我们需要用的时候可以从里面取出来。类似于一个大号的map吧，里面的键存储的是用户的sessionid，用户向服务器发送请求的时候会带上这个sessionid。这时就可以从中取出对应的值了。

## 6.客户端禁用cookie，session是否还能用

　　cookie与 session，一般认为是两个独立的东西，session采用的是在服务器端保持状态的方案，而cookie采用的是在客户端保持状态的方案。但为什么禁用cookie就不能得到session呢？因为session是用session ID来确定当前对话所对应的服务器session，而session ID是通过cookie来传递的，禁用cookie相当于失去了session ID，也就得不到session了。

假定用户关闭cookie的情况下使用session，其实现途径有以下几种：

1. 手动通过URL传值、隐藏表单传递session ID。
3. 用文件、数据库等形式保存session ID，在跨页过程中手动调用。

## 7.Spring mvc和Struts的区别

1. 拦截机制的不同

　　Struts2是**类级别**的拦截，每次请求就会创建一个Action，和Spring整合时Struts2的ActionBean注入作用域是原型模式prototype，然后通过setter，getter吧request数据注入到属性。Struts2中，一个Action对应一个request，response上下文，在接收参数时，可以通过属性接收，这说明属性参数是让多个方法共享的。Struts2中Action的一个方法可以对应一个url，而其类属性却被所有方法共享，这也就无法用注解或其他方式标识其所属方法了，只能设计为多例。

　　SpringMVC是**方法级别**的拦截，一个方法对应一个Request上下文，所以方法直接基本上是独立的，独享request，response数据。而每个方法同时又和一个url对应，参数的传递是直接注入到方法中的，是方法所独有的。处理结果通过ModelMap返回给框架。在Spring整合时，SpringMVC的Controller Bean默认单例模式Singleton，所以默认对所有的请求，只会创建一个Controller，没有共享的属性，所以是线程安全的。如果要改变默认的作用域，需要添加@Scope注解修改。

　　Struts2有自己的拦截Interceptor机制，SpringMVC这是用的是独立的Aop方式，这样导致Struts2的配置文件量还是比SpringMVC大。

2. 底层框架的不同

　　Struts2采用Filter（StrutsPrepareAndExecuteFilter）实现，SpringMVC（DispatcherServlet）则采用Servlet实现。Filter在容器启动之后即初始化，服务停止以后坠毁，晚于Servlet。Servlet在是在调用时初始化，先于Filter调用，服务停止后销毁。

3. 性能方面

　　Struts2是类级别的拦截，每次请求对应实例一个新的Action，需要加载所有的属性值注入，SpringMVC实现了零配置，由于SpringMVC基于方法的拦截，有加载一次单例模式bean注入。所以，SpringMVC开发效率和性能高于Struts2。

4. 配置方面

　　spring MVC和Spring是无缝的。从这个项目的管理和安全上也比Struts2高。

## 8.避免sql注入

1. PreparedStatement（简单又有效的方法）
2. 使用正则表达式过滤传入的参数
3. 字符串过滤
4. JSP中调用该函数检查是否包含非法字符
5. JSP页面判断代码


---
title: Java并发编程入门与高并发面试（一）：详细讲解 - 网站性能测试工具Apache Bench
categories:
  - 高并发
tags:
  - Apache
  - 高并发
abbrlink: 31ea1ae0
date: 2018-09-14 21:12:07
---

摘要：本文介绍网站服务器效能测试工具：Apache Bench 的简介、下载、简单的使用方法和测试

<!--more-->

------


### 1、Apache Bench 简介

Apache Bench是Apache 服务器附带的工具，专门用来执行网站服务器的运行效能，特别是针对Apache 网站服务器。原本用来检测Apache网站能够提供的效能，特别是能看出Apache能提供每秒能送出多少网页。

### 2、Apache Bench下载

我们到网站的官网去下载Apache服务器 
下载地址：[Apache服务器下载地址](https://www.apachelounge.com/download/) 
![这里写图片描述](https://img-blog.csdn.net/20180403151057554?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
下载成功后直接进行解压，无需安装即可使用（仅针对于ApacheBench而言）。 
![这里写图片描述](https://img-blog.csdn.net/20180403151217498?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



### 3、简单的Apache Bench使用方法

- 我们打开命令行界面，从命令行界面进入Apache Bench的解压路径下，进入后访问bin文件夹。 
  ![这里写图片描述](https://img-blog.csdn.net/20180403151541385?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
- 进入后我们输入基础的测试命令对接口进行测试（例如<http://localhost:8080/test>）
- `ab -n 1000 -c 50 http://localhost:8080/test`
- 这段命令的含义是对于上述接口进行1000次测试，在同一时间内允许50个并发请求，执行结果如下： 
  ![这里写图片描述](https://img-blog.csdn.net/20180403162346201?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plc29uam9rZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 4、测试结果讲解

（1）测试指令参数说明： 
Usage: ab [options][]hostname[:port]/path> 
我们上述的测试指令中，options为测试指令参数，其全部指参数说明如下：

Options are:（英文水平太烂，凑活看吧）

| 参数编码（-？） | 参数名       | 参数说明                                                     | 含义                                                         |
| --------------- | ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| -n              | requests     | Number of requests to perform                                | 总请求数                                                     |
| -c              | concurrency  | Number of multiple requests to make at a time                | 并发数量                                                     |
| -t              | timelimit    | Seconds to max. to spend on benchmarkin ,This implies -n 50000 | 测试时长最大秒数                                             |
| -s              | timeout      | Seconds to max. wait for each response, Default is 30 seconds | 每次请求等待响应的最长时间，默认30秒                         |
| -b              | windowsize   | Size of TCP send/receive buffer, in bytes                    | TCP发送\接收缓存大小（单位bytes）                            |
| -B              | address      | Address to bind to when making outgoing connections          | 发送连接时绑定的地址                                         |
| -p              | postfile     | File containing data to POST. Remember also to set -T        | 以POST方法发送文件，必须同时使用-T参数                       |
| -u              | putfile      | File containing data to PUT. Remember also to set -T         | 以PUT方法发送文件，必须同时使用-T参数                        |
| -T              | content-type | Content-type header to use for POST/PUT data, eg., ‘application/x-www-form-urlencoded’, Default is ‘text/plain’ | 使用POST\PUT方式时的Content-Type头，例如：’application/x-www-form-urlencoded’，默认：’text/plain’ |
| -v              | verbosity    | How much troubleshooting info to print                       | 设置显示信息的详细程度 – 4或更大值会显示头信息， 3或更大值可以显示响应代码(404, 200等), 2或更大值可以显示警告和其他信息。 -V 显示版本号并退出 |
| -w              |              | Print out results in HTML tables                             | 以HTML表的格式输出结果。默认时，它是白色背景的两列宽度的一张表 |
| -i              |              | Use HEAD instead of GET                                      | 使用HEAD请求                                                 |
| -x              | attributes   | String to insert as table attributes                         | 插入字符串作为表格属性                                       |
| -y              | attributes   | String to insert as tr attributes                            | 插入字符串作为tr属性                                         |
| -z              | attributes   | String to insert as td or th attributes                      | 插入字符串作为th属性                                         |
| -C              | attribute    | Add cookie, eg. ‘Apache=1234’. (repeatable)                  | 添加cookie，可重复                                           |
| -H              | attribute    | Add Arbitrary header line, eg. ‘Accept-Encoding: gzip’, Inserted after all normal header lines. (repeatable) | 添加任意的头信息，可重复                                     |
| -A              | attribute    | Add Basic WWW Authentication, the attributes ,are a colon separated username and password. | 添加基础的www认证、属性，是一个以冒号分割的账号与密码        |
| -P              | attribute    | Add Basic Proxy Authentication, the attributes ,are a colon separated username and password. | 添加基本的代理身份验证、属性，是一个以冒号分隔的用户名和密码。 |
| -X              | proxy:port   | Proxyserver and port number to use                           | 使用的代理服务和对应端口号                                   |
| -V              |              | Print version number and exit                                | 直接输出版本号退出                                           |
| -k              |              | Use HTTP KeepAlive feature                                   | 使用Http KeepAlice特性                                       |
| -d              |              | Do not show percentiles served table.                        |                                                              |
| -S              |              | Do not show confidence estimators and warnings.              | 不显示confidence estimators和警告                            |
| -q              |              | Do not show progress when doing more than 150 requests       | 当超过150个请求的时候不显示进程                              |
| -l              |              | Accept variable document length (use this for dynamic pages) | 接受可变文档长度(用于动态页面)                               |
| -g              | filename     | Output collected data to gnuplot format file.                | 以gnuplot格式文件输出收集的数据。                            |
| -e              | filename     | Output CSV file with percentages served                      | 以CSV文件的方式输出命中的服务                                |
| -r              |              | Don’t exit on socket receive errors.                         | 返回error时不要终端socket                                    |
| -m              | method       | Method name                                                  | 方法名                                                       |
| -h              |              | Display usage information (this message)                     | 显示使用信息                                                 |

（2）返回结果的说明：

| 字段名               | 解释                                                         |
| -------------------- | ------------------------------------------------------------ |
| Document Path        | 测试页面                                                     |
| Document Length      | 页面大小                                                     |
| Concurrency Level    | 并发量                                                       |
| Time taken for tests | 整个测试持续的时间                                           |
| Complete requests    | 完成的请求数量                                               |
| Failed requests      | 失败的请求数量                                               |
| Total transferred    | 所有请求的响应数据的长度总和，包括每个http响应数据的头信息和正文数据的长度，这里不包括http请求数据的长度。仅仅为WEB服务器流向用户PC的应用层数据总长度 |
| HTML transferred     | 所有请求的响应数据中**正文数据**的总和，数量上 = Total transferred - 响应数据头信息的长度 |
| Requests per second  | **吞吐率**,相当于LR中的每秒事务数，后面括号中的mean表示这是一个平均值 |
| Time per request     | **用户平均请求等待时间**,相当于LR中的平均事务响应时间，后面括号中的mean表示这是一个平均值 |
| Time per request     | **服务器平均请求等待时间**,每个连接请求实际运行时间的平均值  |
| Transfer rate        | （单位时间内从服务器获取的数据长度）平均每秒网络上的流量，可以帮助排除是否存在网络流量过大导致响应时间延长的问题。计算公式：（Total transferred : Time taken for tests） |

特别感谢：慕课网jimin老师的《Java并发编程与高并发解决方案》课程，以下知识点多数来自老师的课程内容。 
**jimin**老师课程地址：[Java并发编程与高并发解决方案](https://coding.imooc.com/class/195.html)

经作者授权转载，原文地址：https://blog.csdn.net/jesonjoke/article/details/79806358
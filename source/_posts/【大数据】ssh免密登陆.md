---
title: ssh免密登陆
categories:
  - 大数据
  - 安装
description: ssh免密登陆
abbrlink: 3032f6ee
date: 2019-11-20 15:15:02
tags:
keywords:
---

对于大数据组件的安装，ssh免密登录是最开始的一个步骤。约定：主节点master和两个从节点slave01、slave02。不仅master要免密登录slave，而且也需要ssh自己：master，就需要把master的密钥拷贝到自己的authorized_keys文件里面。假设master、slave01、slave02的用户名分别为：hadoop_master、hadoop_slave01、hadoop_slave02。

## 1.使用命令生成一对rsa公私钥

~~~shell
ssh-keygen -t rsa [-C "your_email@example.com"]  # 邮箱选填
~~~

然后敲三下回车

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g94j28mtg1j20h808ydfy.jpg)

生成的密钥会存放在~/.ssh目录下。

- id_rsa : 生成的私钥文件

- id_rsa.pub ： 生成的公钥文件

- know_hosts : 已知的主机公钥清单

- authorized_keys:存放远程免密登录的公钥,主要通过这个文件记录多台机器的公钥

## 2.为slave创建.ssh文件夹（如果存在则这一步省略） 

以slave01来演示，slave02类似。

~~~shell
ssh hadoop_slave01@slave01 mkdir -p .ssh
~~~

## 3.复制公钥到authorized_keys

本地：

~~~shell
cat id_rsa.pub > authorized_keys
~~~

远程（二选一）：

~~~shell
#远程复制
scp ~/.ssh/id_rsa.pub hadoop_slave01@slave01:~/.ssh/authorized_keys
#远程文件追加
cat .ssh/id_rsa.pub | ssh hadoop_slave01@slave01 'cat - >> ~/.ssh/authorized_keys'
~~~

（上述的创建目录并复制的操作也可以通过一个 ssh-copy-id 命令一步完成：ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop_slave01@slave01）

## 4.设置权限

.ssh目录的权限必须是700，.ssh/authorized_keys文件权限必须是600。否则会因为权限问题导致无法免密码登录。我们可以看到登陆后会有known_hosts文件生成。

同时要保证.ssh和authorized_keys都只有用户自己有写权限，否则验证无效。  

~~~shell
-rwx------  700    .ssh/ 

-rw-------  600    authorized_keys

-rw-------  600    id_rsa

-rw-r--r--  644    id_rsa.pub
~~~

命令如下：

~~~shell
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
~~~

附：

~~~shell
-rw-------	 (600)	 只有拥有者有读写权限。
-rw-r--r--	 (644)	 只有拥有者有读写权限；而属组用户和其他用户只有读权限。
-rwx------	 (700)	 只有拥有者有读、写、执行权限。
-rwxr-xr-x	 (755)	 拥有者有读、写、执行权限；而属组用户和其他用户只有读、执行权限。
-rwx--x--x	 (711)	 拥有者有读、写、执行权限；而属组用户和其他用户只有执行权限。
-rw-rw-rw-	 (666)	 所有用户都有文件读、写权限。
-rwxrwxrwx	 (777)	 所有用户都有读、写、执行权限。
~~~

对于git的ssh免密登录，可以使用以下命令查看问题：

~~~shell
ssh -vT git@yourhost
~~~

参考：

[centos配置ssh免密码登录后仍要输入密码的解决方法]( https://www.jb51.net/article/121180.htm )

[CentOS 6.5之SSH 免密码登录]( https://www.cnblogs.com/leo2li/p/9341554.html )


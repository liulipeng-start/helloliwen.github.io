---
title: CDH安装
categories: 大数据
keywords: CDH安装
description: CDH安装
abbrlink: 7de3978f
date: 2020-04-23 15:51:47
tags:
---

## 一、卸载

https://blog.csdn.net/weixin_35852328/article/details/81774627

https://www.jianshu.com/p/79d1411aaa42

## 二、安装

### 1.系统版本

~~~shell
CentOS 7.5.1804
#主机
node1.novalocal
node2.novalocal
node3.novalocal
#CDH
CDH 5.16.2
~~~

### 2.环境准备

#### 2.1 更改主机名

#### 2.2 配置hosts映射

特别注意去掉localhost

#### 2.3 关闭防火墙

查看防火墙状态：`firewall-cmd --state`  

关闭防火墙-本次有效：`systemctl stop firewalld.service`

禁用防火墙-永久生效：`systemctl disable firewalld.service`  

#### 2.4 关闭Selinux

`vim /etc/selinux/config`

将SELINUX=enforcing改为SELINUX=disabled

#### 2.5 配置SSH免密登录

#### 2.6 安装JDK

注意：CDH默认安装目录是`/usr/java/default`，如果不是安装这个目录下，需要软连接到这个目录：

~~~shell
ln -s /home/root/jdk/ /usr/java/default/
~~~

删除软连接：rm -rf  ./jdk

#### 2.7 安装Scala

#### 2.8 安装MySQL

[CentOS安装MySQL详解](https://juejin.im/post/5d07cf13f265da1bd522cfb6)

~~~shell
wget -P /root/install https://cdn.mysql.com/archives/mysql-5.7/mysql-5.7.28-linux-glibc2.12-x86_64.tar
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123' WITH GRANT OPTION;
flush privileges;
~~~

#### 2.9 安装时间同步NTP（可省略）

1、安装ntp：`yum install -y ntp`
2、设置NTP服务开机启动

~~~shell
chkconfig ntpd on
service nptd start
~~~

### 3.安装CDH

#### 3.1 相关下载

[1.Cloudera Manager下载地址]
(http://archive.cloudera.com/cm5/cm/5/)

[2.Cloudera Manager的安装包和pacels下载链接](https://blog.csdn.net/high2011/article/details/52884997)
http://archive.cloudera.com/cdh5/parcels/5.16.2/

CDH-5.16.2-1.cdh5.16.2.p0.8-el7.parcel

CDH-5.16.2-1.cdh5.16.2.p0.8-el7.parcel.sha

manifest.json

注意：记得把sha1改为sha，Centos7->el7

cloudera-manager-centos7-cm5.16.2_x86_64.tar.gz

#### 3.2 创建用户cloudera-scm

#### 3.3 创建目录

~~~shell
mkdir -p /opt/cloudera
mkdir -p /opt/cloudera-manager
mkdir -p /opt/cloudera/parcels 
mkdir -p /opt/cloudera/parcel-repo 
~~~

同时一定要把文件夹的权限给 cloudera-scm

~~~she
chown -R cloudera-scm:cloudera-scm cloudera-manager/
~~~

注意文件夹权限，一定要是cloudera-scm:cloudera-scm。

/opt目录下与cloudera有关的文件都是如此。

之前安装kafka的时候就是碰到文件夹权限为root而无法分发的问题。

#### 3.4 移动文件

~~~shell
# CDH包只需要放在Server节点，cloudera-manager每个节点都需要
|--/opt
	|--/cloudera
    	|--/parcels
        |--/parcel-repo
        	|--/CDH-5.16.2-1.cdh5.16.2.p0.8-el7.parcel
            |--/CDH-5.16.2-1.cdh5.16.2.p0.8-el7.parcel.sha
       		|--/manifest.json
	|--/cloudera-manager
		|--/cm-5.15.0
~~~

再次确认文件的权限为 cloudera-scm

#### 3.5 配置Agent

~~~shell
vim /opt/cloudera-manager/cm-5.16.2/etc/cloudera-scm-agent/config.ini
~~~

把server_host改为主节点node1.novalocal

#### 3.6 mysql连接jar包放入指定目录

~~~shell
mv mysql-connector-java-5.1.24.jar mysql-connector-java.jar # 一定要改名
cp mysql-connector-java.jar /opt/cloudera-manager/cm-5.16.2/share/cmf/lib/
cp mysql-connector-java.jar /usr/share/java
~~~

#### 3.7 初始化cloudera-manager-agent

~~~shell
#mysql：数据库类型，如果安装过程中用的oracle，那么该参数就应该改为oracle。
#-h：mysql安装节点
#-uroot：root身份运行mysql。-p123：mysql的root密码是123。
#--scm-host master：CMS的主机，和mysql安装的主机是在同一个主机上。
#最后三个参数是：数据库名，数据库用户名，数据库密码。
/opt/cloudera-manager/cm-5.16.2/share/cmf/schema/scm_prepare_database.sh mysql -hnode1.novalocal -uroot -p123 --scm-host node1.novalocal scm root 123
~~~

有可能有错误：

[java.sql.SQLException: Access denied for user 'root'@'d001' (using password: YES)](https://www.cnblogs.com/wqbin/p/11002884.html)

~~~shell
/opt/cloudera-manager/cm-5.16.2/share/cmf/schema/scm_prepare_database.sh -hnode1.novalocal -P3306 --scm-host node1.novalocal --force mysql scm root 123
~~~

[设置Cloudera Manager数据库](https://www.cnblogs.com/xiqing/p/9645724.html)

#### 3.8 启动

~~~shell
# Server节点(node1.novalocal)
/opt/cloudera-manager/cm-5.16.2/etc/init.d/cloudera-scm-server start

# 各个Agent节点都要
/opt/cloudera-manager/cm-5.16.2/etc/init.d/cloudera-scm-agent start
~~~

#### 3.9 访问界面

访问：http://masterIP:7180，若可以访问（用户名、密码：admin） ，则安装成功。

Manager 启动成功需要等待一段时间，过程中会在数据库中创建对应的表需要耗费一些时间

### 4.相关问题

#### 4.1 安装cloudera-manager-agent，下载失败

Cloudera Manager 添加主机无法安装 cloudera-manager-agent 包。

[CDH5离线Parcel安装单节点集群失败](https://segmentfault.com/q/1010000015638069)

#### 4.2 cloudera-manager 安装失败解决办法

[cloudera manager agent安装时出现Failed to connect to previous supervisor问题解决方法](http://www.wangluoshenghuo.com/2018/11/30/cloudera-manager-agent安装时出现failed-to-connect-to-previous-supervisor问题解决方法/)

cloudera-manager 节点状况不良
清空/opt/cloudera-manager/cm-5.16.2/lib/cloudera-scm-agent目录下的cm_guid和uuid文件。

#### 4.3 相关命令

~~~shell
/etc/init.d/cloudera-scm-agent status
# 查看进程
ps -ef |grep cloudera
# 查找文件
find / -name cloudera-manager
# 查看是否安装包
rpm -qa | grep "perl"
~~~

#### 4.4 建议

最好一次安装好，否则文件多了之后，报错了很难排查错误，查找问题比重装还耗时。只能卸载干净再重装。

### 5.参考

[1.Linux环境Cloudera CDH安装配置完全解决方案](https://juejin.im/post/5cee40e66fb9a07f0b03a4ef)

[2.CDH5.14安装指南和维护(亲自搭建好多次)](https://blog.csdn.net/silentwolfyh/article/details/54893826)

[3.CentOS7查看和关闭防火墙](https://blog.csdn.net/ytangdigl/article/details/79796961)

---
title: Hadoop
categories: 大数据
tags: 面试
description: Hadoop总结
abbrlink: b3349d42
date: 2019-08-02 10:35:54
keywords:
---

## 1.简单描述如何安装配置一个apache开源版hadoop

1. 安装JDK并配置环境变量（/etc/profile）
2. 关闭防火墙
3. 配置hosts文件，方便hadoop通过主机名访问（/etc/hosts）
4. 设置ssh免密码登录
5. 解压缩hadoop安装包，并配置环境变量
6. 修改配置文件（`$HADOOP_HOME/conf`）
   hadoop-env.sh core-site.xml hdfs-site.xml mapred-site.xml
7. 格式化hdfs文件系统 （hadoop namenode -format）
8. 启动hadoop （`$HADOOP_HOME/bin/start-all.sh`）
9. 使用jps查看进程

## 2.正常工作的hadoop集群中hadoop都分别需要启动那些进程，作用分别是什么

1. **NameNode**： HDFS的守护进程，负责记录文件是如何分割成数据块，以及这些数据块分别被存储到那些数据节点上，它的主要功能是对内存及IO进行集中管理
2. **Secondary NameNode**：辅助后台程序，与NameNode进行通信，以便定期保存HDFS元数据的快照。
3. **DataNode**：负责把HDFS数据块读写到本地的文件系统。
4. **JobTracker**：负责分配task，并监控所有运行的task。
5. **TaskTracker**：负责执行具体的task，并与JobTracker进行交互。

## 3.Hadoop调度器及其工作方法

　　比较流行的三种调度器有：默认调度器FIFO，计算能力调度器Capacity Scheduler，公平调度器Fair Scheduler

1. 默认调度器FIFO：hadoop中默认的调度器，采用先进先出的原则
2. 计算能力调度器Capacity Scheduler：选择占用资源小，优先级高的先执行
3. 公平调度器Fair Scheduler：同一队列中的作业公平共享队列中所有资源

## 4.Hadoop二级排序

　　在Hadoop中，默认情况下是按照key进行排序，如果要按照value进行排序怎么办？有两种方法进行二次排序，分别为：**buffer and in memory sort**和**value-to-key conversion**。

**buffer and in memory sort**

　　主要思想是：在reduce()函数中，将某个key对应的所有value保存下来，然后进行排序。 这种方法最大的缺点是：可能会造成out of memory。

**value-to-key conversion**

　　主要思想是：将key和部分value拼接成一个组合key（实现WritableComparable接口或者调setSortComparatorClass函数），这样reduce获取的结果便是先按key排序，后按value排序的结果。需要注意的是，用户需要自己实现Paritioner，以便只按照key进行数据划分。Hadoop显式的支持二次排序，在Configuration类中有个setGroupingComparatorClass()方法，可用于设置排序group的key值。

## 5.MapReduce中combiner、partition的作用

**combiner**

　　有时一个map可能会产生大量的输出，combiner的作用是在map端对输出先做一次合并，以减少网络传输到reducer的数量。注意：mapper的输出为combiner的输入，reducer的输入为combiner的输出。

**partition**

　　把map任务输出的中间结果按照key的范围划分成R份(R是预先定义的reduce任务的个数)，划分时通常使用hash函数，如：hash(key) mod R，这样可以保证一段范围内的key，一定会由一个reduce任务来处理。


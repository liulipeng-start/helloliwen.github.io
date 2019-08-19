---
title: 【LeetCode】24. Swap Nodes in Pairs
categories:
  - 算法与数据结构
  - LeetCode
description: 24. 两两交换链表中的节点。主题：链表。难度：中等。
abbrlink: 664d5542
date: 2019-08-17 16:55:17
tags:
keywords:
---

## 1.题目描述

Given a linked list, swap every two adjacent nodes and return its head.

You may **not** modify the values in the list's nodes, only nodes itself may be changed.

 给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

**你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

**Example:**

> Given 1->2->3->4, you should return the list as 2->1->4->3.

## 2.Solutions

递归版本：

~~~java
public static ListNode swapPairs(ListNode head) {
    if ((head == null)||(head.next == null))
        return head;
    ListNode node = head.next;
    head.next = swapPairs(head.next.next);
    node.next = head;
    return node;
}
~~~

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1g62xkgz3maj20ja0bf74l.jpg" style="zoom:60%">

迭代版本：

~~~java
public static ListNode swapPairsWithIteration(ListNode head){
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode current = dummy;
    while (current.next != null && current.next.next != null) {
        ListNode first = current.next;
        ListNode second = current.next.next;
        first.next = second.next;
        current.next = second;
        current.next.next = first;
        current = current.next.next;
    }
    return dummy.next;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
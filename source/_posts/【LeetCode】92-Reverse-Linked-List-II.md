---
title: 【LeetCode】92. Reverse Linked List II
categories:
  - 算法与数据结构
  - LeetCode
description: 92. 反转链表 II。主题：链表。难度：中等。
abbrlink: b62a3fe5
date: 2019-09-03 10:27:48
tags:
keywords:
---

## 1.题目描述

Reverse a linked list from position *m* to *n*. Do it in one-pass.

反转从位置 *m* 到 *n* 的链表。请使用一趟扫描完成反转。

**Note:** 1 ≤ *m* ≤ *n* ≤ length of list.

**Example:**

> Input: 1->2->3->4->5->NULL, m = 2, n = 4
> Output: 1->4->3->2->5->NULL

## 2.Solutions

~~~java
public static ListNode reverseBetween(ListNode head, int m, int n) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    //first part
    ListNode cur1 = dummy;
    ListNode pre1 = null;
    for(int i=0;i<m;i++){
        pre1 = cur1;
        cur1 = cur1.next;
    }

    //reverse
    ListNode cur2 = cur1;
    ListNode pre2 = pre1;
    ListNode next;
    for(int i=m;i<=n;i++){
        next = cur2.next;
        cur2.next = pre2;
        pre2 = cur2;
        cur2 = next;
    }

    //connect 
    pre1.next = pre2;
    cur1.next = cur2;

    return dummy.next;
}
~~~

参考：[【链表】打卡2：如何优雅着反转单链表](https://helloliwen.github.io/124f68d0.html)

<center><font style="font-weight:bold">（完）</font></center>
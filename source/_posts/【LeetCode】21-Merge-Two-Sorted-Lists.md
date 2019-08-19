---
title: 【LeetCode】21. Merge Two Sorted Lists
categories:
  - 算法与数据结构
  - LeetCode
description: 21. 合并两个有序链表。主题：链表。难度：容易。
abbrlink: d9f1239e
date: 2019-08-17 09:52:56
tags:
keywords:
---

## 1.题目描述

Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

将两个有序链表合并为一个新的有序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

**Example:**

> Input: 1->2->4, 1->3->4
> Output: 1->1->2->3->4->4

## 2.Solutions

递归版本：

~~~java
public static ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    if(l1 == null) return l2;
    if(l2 == null) return l1;
    if(l1.val < l2.val){
        l1.next = mergeTwoLists(l1.next, l2);
        return l1;
    } else{
        l2.next = mergeTwoLists(l1, l2.next);
        return l2;
    }
}
~~~

迭代版本：

~~~java
public static ListNode mergeTwoListsIterative(ListNode l1, ListNode l2) {
    if(l1 == null) return l2;
    if(l2 == null) return l1;
    ListNode start = new ListNode(0);
    ListNode cur = start;
    while(l1 != null && l2 != null){
        if(l1.val <= l2.val){
            cur.next = l1;
            l1 = l1.next;
        }else{
            cur.next = l2;
            l2 = l2.next;
        }
        cur = cur.next;
    }
    cur.next = l1 == null ? l2 : l1;
    return start.next;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
---
title: 【LeetCode】83. Remove Duplicates from Sorted List
categories:
  - 算法与数据结构
  - LeetCode
description: 83. 删除排序链表中的重复元素。主题：链表。难度：容易。
abbrlink: 7bd12ccc
date: 2019-09-02 09:31:51
tags:
keywords:
---

## 1.题目描述

Given a sorted linked list, delete all duplicates such that each element appear only *once*.

给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

**Example 1:**

> Input: 1->1->2
> Output: 1->2

**Example 2:**

> Input: 1->1->2->3->3
> Output: 1->2->3

## 2.Solutions

类似于82题解决思路：

~~~java
public static ListNode deleteDuplicates(ListNode head) {
    if(head == null || head.next == null) return head;
    ListNode dummy=new ListNode(0);
    dummy.next=head;
    //双指针
    ListNode pre=dummy;
    ListNode cur=head;
    while(cur!=null){
        while(cur.next!=null&&cur.val==cur.next.val){
            cur=cur.next;
        }
        if(pre.next==cur)
            pre=pre.next;
        else{
            pre.next=cur;
            pre=pre.next;
        }

        cur=cur.next;
    }
    return dummy.next;
}
~~~

单指针：

~~~java
public static ListNode deleteDuplicates(ListNode head) {
    if(head == null || head.next == null) 
        return head;
    ListNode cur = head;
    while(cur.next != null){
        if(cur.next == null)
            break;

        if(cur.val == cur.next.val)
            cur.next = cur.next.next;
        else
            cur = cur.next;
    }
    return head;
}
~~~

递归：

~~~java
public ListNode deleteDuplicates(ListNode head) {
        if(head == null || head.next == null)return head;
        head.next = deleteDuplicates(head.next);
        return head.val == head.next.val ? head.next : head;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
---
title: 【LeetCode】61. Rotate List
categories:
  - 算法与数据结构
  - LeetCode
description: 61. 旋转链表。主题：链表、双指针。难度：中等。
abbrlink: b8280d22
date: 2019-08-29 09:45:05
tags:
keywords:
---

## 1.题目描述

Given a linked list, rotate the list to the right by *k* places, where *k* is non-negative.

给定一个链表，旋转链表，将链表每个节点向右移动 *k* 个位置，其中 *k* 是非负数。

**Example 1:**

> Input: 1->2->3->4->5->NULL, k = 2
> Output: 4->5->1->2->3->NULL
> Explanation:
> rotate 1 steps to the right: 5->1->2->3->4->NULL
> rotate 2 steps to the right: 4->5->1->2->3->NULL

**Example 2:**

> Input: 0->1->2->NULL, k = 4
> Output: 2->0->1->NULL
> Explanation:
> rotate 1 steps to the right: 2->0->1->NULL
> rotate 2 steps to the right: 1->2->0->NULL
> rotate 3 steps to the right: 0->1->2->NULL
> rotate 4 steps to the right: 2->0->1->NULL

## 2.Solutions

Since n may be a large number compared to the length of list. So we need to know the length of linked list.After that, move the list after the (i-n%i)th node to the front to finish the rotation.

Ex: {1,2,3} k=2 Move the list after the 1st node to the front

Ex: {1,2,3} k=5, In this case Move the list after (3-5%3=1)st node to the front.

So the code has three parts.

1. Get the length
2. Move to the (i-n%i)th node

3. Do the rotation

~~~java
public static ListNode rotateRight(ListNode head, int n) {
    if (head==null||head.next==null) 
        return head;

    ListNode dummy=new ListNode(0);
    dummy.next=head;
    ListNode fast=dummy,slow=dummy;

    int i;
    for (i=0;fast.next!=null;i++)//Get the total length 
        fast=fast.next;

    for (int j=i-n%i;j>0;j--) //Get the i-n%i th node
        slow=slow.next;

    fast.next=dummy.next; //Do the rotation
    dummy.next=slow.next;
    slow.next=null;

    return dummy.next;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
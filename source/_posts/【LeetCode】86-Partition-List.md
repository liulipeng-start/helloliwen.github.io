---
title: 【LeetCode】86. Partition List
categories:
  - 算法与数据结构
  - LeetCode
description: 86. 分隔链表。主题：链表、双指针。难度：中等。
abbrlink: b4016ada
date: 2019-09-02 10:45:18
tags:
keywords:
---

## 1.题目描述

Given a linked list and a value *x*, partition it such that all nodes less than *x* come before nodes greater than or equal to *x*.

You should preserve the original relative order of the nodes in each of the two partitions.

给定一个链表和一个特定值 *x*，对链表进行分隔，使得所有小于 *x* 的节点都在大于或等于 *x* 的节点之前。

你应当保留两个分区中每个节点的初始相对位置。

**Example:**

> Input: head = 1->4->3->2->5->2, x = 3
> Output: 1->2->2->4->3->5

## 2.Solutions

the basic idea is to maintain two queues, the first one stores all nodes with val less than x , and the second queue stores all the rest nodes. Then concat these two queues. Remember to set the tail of second queue a null next, or u will get TLE.

~~~java
public static ListNode partition(ListNode head, int x) {
    //dummy heads of the 1st and 2nd queues
    ListNode smallerHead = new ListNode(0), greaterHead = new ListNode(0);
    //current tails of the two queues;
    ListNode smallerLast = smallerHead, greaterLast = greaterHead;

    while (head != null){
        if (head.val < x) {
            smallerLast.next = head;
            smallerLast = smallerLast.next;
        }else {
            greaterLast.next = head;
            greaterLast = greaterLast.next;
        }
        head = head.next;
    }
    // cut off anything after bigger
    greaterLast.next = null;
    //Skipping dummy head of greater and linking
    smallerLast.next = greaterHead.next;
    //Skipping dummy head of smaller and returning next
    return smallerHead.next;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
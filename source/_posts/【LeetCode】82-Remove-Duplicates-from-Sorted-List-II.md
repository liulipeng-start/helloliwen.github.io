---
title: 【LeetCode】82. Remove Duplicates from Sorted List II
categories:
  - 算法与数据结构
  - LeetCode
description: 82. 删除排序链表中的重复元素 II。主题：链表。难度：中等。
abbrlink: 16a3e67e
date: 2019-09-02 09:10:59
tags:
keywords:
---

## 1.题目描述

Given a sorted linked list, delete all nodes that have duplicate numbers, leaving only *distinct* numbers from the original list.

给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 *没有重复出现* 的数字。

**Example 1:**

> Input: 1->2->3->3->4->4->5
> Output: 1->2->5

**Example 2:**

> Input: 1->1->1->2->3
> Output: 2->3

## 2.Solutions

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
        else
            pre.next=cur.next;

        cur=cur.next;
    }
    return dummy.next;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
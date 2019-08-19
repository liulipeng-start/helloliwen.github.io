---
title: 【LeetCode】19. Remove Nth Node From End of List
categories:
  - 算法与数据结构
  - LeetCode
description: 19. 删除链表的倒数第N个节点。主题：链表、双指针。难度：中等。
abbrlink: fc2aa4a3
date: 2019-08-16 11:02:35
tags:
keywords:
---

## 1.题目描述

Given a linked list, remove the *n*-th node from the end of list and return its head.

给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。

**Example:**

> Given linked list: 1->2->3->4->5, and n = 2.
>
> After removing the second node from the end, the linked list becomes 1->2->3->5.

**Note:**

Given *n* will always be valid.

**Follow up（进阶）:**

Could you do this in one pass?

你能尝试使用一趟扫描实现吗？

## 2.Solutions

快慢指针（双指针），把慢指针定位到要删除节点的前面就可以了。

~~~java
public static void main(String[] args) {
    ListNode head = new ListNode(1);
    ListNode node = head;
    int i = head.val;
    while(++i<6){
        node.next = new ListNode(i);
        node = node.next;
    }
    node = removeNthFromEnd(head,1);
    while(node!=null){
        System.out.print(node.val+" ");
        node = node.next;
    }
}

public static ListNode removeNthFromEnd(ListNode head, int n) {
    //如果head.length=1并且只删除这一个节点，就没用了
    //ListNode slow = head,fast = head;
    ListNode start = new ListNode(0);
    start.next = head;
    ListNode slow = start,fast = start;

    //Move fast in front so that the gap between slow and fast becomes n
    for (int i = 0; i <= n; i++) {
        fast = fast.next;
    }
    //Move fast to the end, maintaining the gap
    while(fast!=null){
        slow = slow.next;
        fast = fast.next;
    }
    //删除节点
    slow.next = slow.next.next;

    return start.next;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>


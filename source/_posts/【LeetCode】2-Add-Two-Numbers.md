---
title: 【LeetCode】2.Add Two Numbers
categories:
  - 算法与数据结构
  - LeetCode
description: 2.链表两数相加。主题：链表、数学
abbrlink: caf13328
date: 2019-07-03 16:57:52
tags:
keywords:
---

## 1.题目描述

You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order** and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

**Example:**

> Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
> Output: 7 -> 0 -> 8
> Explanation: 342 + 465 = 807.

## 2.Solutions

~~~java
public class AddTwoNumbers02 {

    public static void main(String[] args) {
        int[] arr1 = {2,4,3};
        int[] arr2 = {5,6,4};
        ListNode l1 = new ListNode(arr1[0]);
        ListNode l2 = new ListNode(arr2[0]);
        ListNode cur = l1;
        int i = 0;
        while(++i<arr1.length){
            cur.next = new ListNode(arr1[i]);
            cur = cur.next;
        }
        i = 0;
        cur = l2;
        while(++i<arr2.length){
            cur.next = new ListNode(arr2[i]);
            cur = cur.next;
        }
        cur = addTwoNumbers(l1,l2);
        while(cur != null){
            System.out.print(cur.val+" ");
            cur = cur.next;
        }
    }

    public static ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = new ListNode(0);
        ListNode sentinel = head;
        int sum = 0;
        while (l1 != null || l2 != null) {
            sum /= 10;
            if (l1 != null) {
                sum += l1.val;
                l1 = l1.next;
            }
            if (l2 != null) {
                sum += l2.val;
                l2 = l2.next;
            }
            sentinel.next = new ListNode(sum % 10);
            sentinel = sentinel.next;
        }
        if (sum / 10 == 1)
            sentinel.next = new ListNode(1);
        return head.next;
    }
}

class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
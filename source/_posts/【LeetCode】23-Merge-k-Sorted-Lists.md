---
title: 【LeetCode】23. Merge k Sorted Lists
categories:
  - 算法与数据结构
  - LeetCode
description: 23. 合并K个排序链表。主题：堆、链表、分治算法。难度：困难。
abbrlink: 2997e0e6
date: 2019-08-17 15:45:57
tags:
keywords:
---

## 1.题目描述

Merge *k* sorted linked lists and return it as one sorted list. Analyze and describe its complexity.

合并 *k* 个排序链表，返回合并后的排序链表。请分析和描述算法的复杂度。

**Example:**

> Input:
> [
>   1->4->5,
>   1->3->4,
>   2->6
> ]
> Output: 1->1->2->3->4->4->5->6

## 2.Solutions

迭代、分治版本：

~~~java
public static ListNode mergeKLists(ListNode[] lists){
    return partion(lists,0,lists.length-1);
}

public static ListNode partion(ListNode[] lists,int s,int e){
    if(s==e)  return lists[s];
    if(s<e){
        int q=s+(e-s)/2;// avoid integer overflow,
        ListNode l1=partion(lists,s,q);
        ListNode l2=partion(lists,q+1,e);
        return merge(l1,l2);
    }else
        return null;
}

//This function is from Merge Two Sorted Lists.
public static ListNode merge(ListNode l1,ListNode l2){
    if(l1==null) return l2;
    if(l2==null) return l1;
    if(l1.val<l2.val){
        l1.next=merge(l1.next,l2);
        return l1;
    }else{
        l2.next=merge(l1,l2.next);
        return l2;
    }
}
~~~

优先队列：

　　If someone understand how priority queue works, then it would be trivial to walk through the codes.

~~~java
public ListNode mergeKLists(List<ListNode> lists) {
    if (lists==null||lists.size()==0) return null;

    PriorityQueue<ListNode> queue= new PriorityQueue<ListNode>(lists.size(),new Comparator<ListNode>(){
        @Override
        public int compare(ListNode o1,ListNode o2){
            if (o1.val<o2.val)
                return -1;
            else if (o1.val==o2.val)
                return 0;
            else 
                return 1;
        }
    });

    ListNode dummy = new ListNode(0);
    ListNode tail=dummy;

    for (ListNode node:lists)
        if (node!=null)
            queue.add(node);

    while (!queue.isEmpty()){
        tail.next=queue.poll();
        tail=tail.next;

        if (tail.next!=null)
            queue.add(tail.next);
    }
    return dummy.next;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>


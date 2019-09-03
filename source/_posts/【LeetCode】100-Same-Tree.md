---
title: 【LeetCode】100. Same Tree
categories:
  - 算法与数据结构
  - LeetCode
description: 100. 相同的树。主题：树、DFS。难度：容易。
abbrlink: 96d40b97
date: 2019-09-03 21:00:17
tags:
keywords:
---

## 1.题目描述

Given two binary trees, write a function to check if they are the same or not.

Two binary trees are considered the same if they are structurally identical and the nodes have the same value.

给定两个二叉树，编写一个函数来检验它们是否相同。

如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。

**Example 1:**

```
Input:     1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]

Output: true
```

**Example 2:**

```
Input:     1         1
          /           \
         2             2

        [1,2],     [1,null,2]

Output: false
```

**Example 3:**

```
Input:     1         1
          / \       / \
         2   1     1   2

        [1,2,1],   [1,1,2]

Output: false
```

## 2.Solutions

~~~java
public boolean isSameTree(TreeNode p, TreeNode q) {
    if(p == NULL || q == NULL) return (p == q);
    if(p.val == q.val)
        return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
    return false;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
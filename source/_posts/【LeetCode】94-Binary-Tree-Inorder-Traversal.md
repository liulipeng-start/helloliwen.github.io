---
title: 【LeetCode】94. Binary Tree Inorder Traversal
categories:
  - 算法与数据结构
  - LeetCode
description: 94. 二叉树的中序遍历。主题：栈、树、Hash Table。难度：中等。
abbrlink: 9da82578
date: 2019-09-03 12:58:07
tags:
keywords:
---

## 1.题目描述

Given a binary tree, return the *inorder* traversal of its nodes' values.

给定一个二叉树，返回它的*中序* 遍历。

**Example:**

```
Input: [1,null,2,3]
   1
    \
     2
    /
   3

Output: [1,3,2]
```

**Follow up:** Recursive solution is trivial, could you do it iteratively?

**进阶:** 递归算法很简单，你可以通过迭代算法完成吗？

## 2.Solutions

### 递归版本

~~~java
public List<Integer> inorderTraversalWithRecursive(TreeNode root) {
    List<Integer> res = new LinkedList<Integer>();
    helper(root,res);
    return res;
}

//helper function for method 1
private void helper(TreeNode root, List<Integer> res) {
    if (root != null) {
        helper(root.left, res);
        res.add(root.val);
        helper(root.right, res);		
    }
}
~~~

## 迭代版本（使用栈）

**Explanation**

The basic idea is referred from [here](https://leetcode.com/discuss/19765/iterative-solution-in-java-simple-and-readable): using stack to simulate the recursion procedure: for each node, travel to its left child until it's left leaf, then pop to left leaf's higher level node A, and switch to A's right branch. Keep the above steps until cur is null and stack is empty. As the following:

**Runtime = O(n)**: As each node is visited once

**Space = O(n)**

~~~java
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> res = new LinkedList<Integer>();
    if (root == null) return res;

    Stack<TreeNode> stack = new Stack<TreeNode>();
    TreeNode cur = root;
    while (cur != null || !stack.isEmpty()) { 
        while (cur != null) {//Travel to each node's left child, till reach the left leaf
            stack.push(cur);
            cur = cur.left;				
        }		 
        cur = stack.pop();// Backtrack to higher level node A
        res.add(cur.val);// Add the node to the result list
        cur = cur.right;// Switch to A'right branch
    }
    return res;
}
~~~

参考：[【树】二叉树的设计、实现与遍历](https://helloliwen.github.io/d9b9e86b.html)

<center><font style="font-weight:bold">（完）</font></center>
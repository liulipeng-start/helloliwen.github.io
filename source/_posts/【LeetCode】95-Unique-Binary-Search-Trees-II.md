---
title: 【LeetCode】95. Unique Binary Search Trees II
categories:
  - 算法与数据结构
  - LeetCode
description: 95. 不同的二叉搜索树 II。主题：树、动态规划。难度：中等。
abbrlink: 51286f15
date: 2019-09-03 14:10:58
tags:
keywords:
---

## 1.题目描述

Given an integer *n*, generate all structurally unique **BST's** (binary search trees) that store values 1 ... *n*.

给定一个整数 *n*，生成所有由 1 ... *n* 为节点所组成的**二叉搜索树**。

**Example:**

```
Input: 3
Output:
[
  [1,null,3,2],
  [3,2,null,1],
  [3,1,null,null,2],
  [2,1,3],
  [1,null,2,null,3]
]
Explanation:
The above output corresponds to the 5 unique BST's shown below:
以上的输出对应以下 5 种不同结构的二叉搜索树：
   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

## 2.Solutions

### 迭代版本

I start by noting that 1..n is the in-order traversal for any BST with nodes 1 to n. So if I pick i-th node as my root, the left subtree will contain elements 1 to (i-1), and the right subtree will contain elements (i+1) to n. I use recursive calls to get back all possible trees for left and right subtrees and combine them in all possible ways with the root.

~~~java
public List<TreeNode> generateTrees(int n) {
    return genTrees(1, n);
}

public List<TreeNode> genTrees(int start, int end) {

    List<TreeNode> list = new ArrayList<TreeNode>();

    if (start > end) {
        list.add(null);
        return list;
    }

    if (start == end) {
        list.add(new TreeNode(start));
        return list;
    }

    List<TreeNode> left, right;
    for (int i = start; i <= end; i++) {

        left = genTrees(start, i - 1);
        right = genTrees(i + 1, end);

        for (TreeNode lnode : left) {
            for (TreeNode rnode : right) {
                TreeNode root = new TreeNode(i);
                root.left = lnode;
                root.right = rnode;
                list.add(root);
            }
        }
    }
    return list;
}
~~~

### 动态规划版本

~~~java
public static List<TreeNode> generateTreesDP(int n) {
    List<TreeNode>[] result = new List[n + 1];
    result[0] = new ArrayList<TreeNode>();
    if (n == 0) {
        return result[0];
    }

    result[0].add(null);
    for (int len = 1; len <= n; len++) {
        result[len] = new ArrayList<TreeNode>();
        for (int j = 0; j < len; j++) {
            for (TreeNode nodeL : result[j]) {
                for (TreeNode nodeR : result[len - j - 1]) {
                    TreeNode node = new TreeNode(j + 1);
                    node.left = nodeL;
                    node.right = clone(nodeR, j + 1);
                    result[len].add(node);
                }
            }
        }
    }
    return result[n];
}

private static TreeNode clone(TreeNode n, int offset) {
    if (n == null) 
        return null;

    TreeNode node = new TreeNode(n.val + offset);
    node.left = clone(n.left, offset);
    node.right = clone(n.right, offset);
    return node;
}
~~~

**result[i]** stores the result until length **i**. For the result for length i+1, select the root node j from 0 to i, combine the result from left side and right side. Note for the right side we have to clone the nodes as the value will be offsetted by **j**.

<center><font style="font-weight:bold">（完）</font></center>
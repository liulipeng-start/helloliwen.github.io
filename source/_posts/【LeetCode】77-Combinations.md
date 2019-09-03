---
title: 【LeetCode】77. Combinations
categories:
  - 算法与数据结构
  - LeetCode
description: 77. 组合。主题：回溯算法。难度：中等。
abbrlink: 22322eb4
date: 2019-08-30 15:57:04
tags:
keywords:
---

## 1.题目描述

Given two integers *n* and *k*, return all possible combinations of *k* numbers out of 1 ... *n*.

给定两个整数 *n* 和 *k*，返回 1 ... *n* 中所有可能的 *k* 个数的组合。

**Example:**

> Input: n = 4, k = 2
> Output:
> [
>   [2,4],
>   [3,4],
>   [2,3],
>   [1,2],
>   [1,3],
>   [1,4],
> ]

## 2.Solutions

Please check out my more precise version, based on the following fact:

1. the valid range for the 1st position is [1, n-k+1]

1. the valid range for the i-th position is [number we selected for previous position+1, n-k+i]

~~~java
public static List<List<Integer>> combine(int n, int k) {
    List<List<Integer>> res = new ArrayList<>();
    dfs(res, new ArrayList<Integer>(), k, 1, n - k + 1);
    return res;
}

private static void dfs(List<List<Integer>> res, List<Integer> list, int remain,
                        int from, int to) {
    if (remain == 0) {
        res.add(new ArrayList<Integer>(list));
        return;
    }
    for (int i = from; i <= to; i++) {
        list.add(i);
        dfs(res, list, remain - 1, i + 1, to + 1);
        list.remove(list.size() - 1);
    }
}
~~~

排列：[46. Permutations](https://helloliwen.github.io/ca8ed476.html)

回溯算法：[39.组合总和](https://helloliwen.github.io/d88a68c8.html)、[40.组合总和 II](https://helloliwen.github.io/9b36a93.html)、[46.全排列](https://helloliwen.github.io/ca8ed476.html)、[47.全排列 II](https://helloliwen.github.io/b9cd9e7f.html)、78.子集、90.子集 II。

<center><font style="font-weight:bold">（完）</font></center>
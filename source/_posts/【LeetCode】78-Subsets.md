---
title: 【LeetCode】78. Subsets
categories:
  - 算法与数据结构
  - LeetCode
description: 78. 子集。主题：位运算、数组、回溯算法。难度：中等。
abbrlink: e279c358
date: 2019-08-30 16:41:18
tags:
keywords:
---

## 1.题目描述

Given a set of **distinct** integers, *nums*, return all possible subsets (the power set).

给定一组**不含重复元素**的整数数组 *nums*，返回该数组所有可能的子集（幂集）。

**Note:** The solution set must not contain duplicate subsets.

**说明：**解集不能包含重复的子集。

**Example:**

> Input: nums = [1,2,3]
> Output:
> [
>   [3],
>   [1],
>   [2],
>   [1,2,3],
>   [1,3],
>   [2,3],
>   [1,2],
>   []
> ]

## 2.Solutions

~~~java
public List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<>(), nums, 0);
    return list;
}

private void backtrack(List<List<Integer>> list , 
                       List<Integer> tempList, int [] nums, int start){
    list.add(new ArrayList<>(tempList));
    for(int i = start; i < nums.length; i++){
        tempList.add(nums[i]);
        backtrack(list, tempList, nums, i + 1);
        tempList.remove(tempList.size() - 1);
    }
}
~~~

I drew an ugly but clear pic about how backtracking works. Once nums[i] arrived at end, remove the last element and go back to last level until all possible solutions are stored.

<img src="https://discuss.leetcode.com/assets/uploads/files/1503221799085-78.subsets-resized.png" align="left" />

位运算：[My solution using bit manipulation](https://leetcode.com/problems/subsets/discuss/27288/My-solution-using-bit-manipulation)

<center><font style="font-weight:bold">（完）</font></center>
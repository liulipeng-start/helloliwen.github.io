---
title: 【LeetCode】90. Subsets II
categories:
  - 算法与数据结构
  - LeetCode
description: 90. 子集 II。主题：数组、回溯算法。难度：中等。
abbrlink: d38e2698
date: 2019-09-02 20:06:12
tags:
keywords:
---

## 1.题目描述

Given a collection of integers that might contain duplicates, **nums**, return all possible subsets (the power set).

给定一个可能包含重复元素的整数数组 ***nums***，返回该数组所有可能的子集（幂集）。

**Note:** The solution set must not contain duplicate subsets.

**说明：**解集不能包含重复的子集。

**Example:**

> Input: [1,2,2]
> Output:
> [
>   [2],
>   [1],
>   [1,2,2],
>   [2,2],
>   [1,2],
>   []
> ]

## 2.Solutions

~~~java
public static List<List<Integer>> subsetsWithDup(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<>(), nums, 0);
    return list;
}

private static void backtrack(List<List<Integer>> list, 
                              List<Integer> tempList, int [] nums, int start){
    list.add(new ArrayList<>(tempList));
    for(int i = start; i < nums.length; i++){
        if(i > start && nums[i] == nums[i-1]) continue; // skip duplicates
        tempList.add(nums[i]);
        backtrack(list, tempList, nums, i + 1);
        tempList.remove(tempList.size() - 1);
    }
}
~~~

参考：[78. Subsets](https://helloliwen.github.io/e279c358.html)

<center><font style="font-weight:bold">（完）</font></center>


---
title: 【LeetCode】47. Permutations II
categories:
  - 算法与数据结构
  - LeetCode
description: 47. 全排列 II。主题：回溯算法。难度：中等。
abbrlink: b9cd9e7f
date: 2019-08-21 19:36:46
tags:
keywords:
---

## 1.题目描述

Given a collection of numbers that might contain duplicates, return all possible unique permutations.

给定一个可包含重复数字的序列，返回所有不重复的全排列。

**Example:**

> Input: [1,1,2]
> Output:
> [
>   [1,1,2],
>   [1,2,1],
>   [2,1,1]
> ]

## 2.Solutions

Use an extra boolean array " boolean[] used" to indicate whether the value is added to list.

Sort the array "int[] nums" to make sure we can skip the same value.

when a number has the same value with its previous, we can use this number only if his previous is used

~~~java
public static List<List<Integer>> permuteUnique(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<Integer>(), nums,
              new boolean[nums.length]);
    return list;
}

// 也属于DFS
private static void backtrack(List<List<Integer>> list,
                              List<Integer> tempList, int[] nums, boolean[] used) {
    if (tempList.size() == nums.length) {
        list.add(new ArrayList<>(tempList));
        return;
    }
    for (int i = 0; i < nums.length; i++) {
        if (used[i])
            continue;
        /*
		 * 只需要判断i和i-1(而不需要判断i与i-2...) 相同的元素一定是相邻的。
		 * 如果i-2，i-1，i相同，那么在上一轮循环就已经判断了i-1,i，本轮循环不需要重复判断 
		 * nums[i] == nums[i-1] 剪枝
		 */
        if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1])
            continue;

        used[i] = true;
        tempList.add(nums[i]);

        backtrack(list, tempList, nums, used);

        used[i] = false;
        tempList.remove(tempList.size() - 1);
    }
}
~~~

<center><font style="font-weight:bold">（完）</font></center>


---
title: 【LeetCode】40. Combination Sum II
categories:
  - 算法与数据结构
  - LeetCode
description: 40. 组合总和 II。主题：数组、回溯法。难度：中等。
abbrlink: 9b36a93
date: 2019-08-19 15:33:43
tags:
keywords:
---

## 1.题目描述

Given a collection of candidate numbers (`candidates`) and a target number (`target`), find all unique combinations in `candidates` where the candidate numbers sums to `target`.

Each number in `candidates` may only be used **once** in the combination.

给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的每个数字在每个组合中只能使用一次。

**Note:**

- All numbers (including `target`) will be positive integers.
- The solution set must not contain duplicate combinations.

**说明：**

- 所有数字（包括目标数）都是正整数。
- 解集不能包含重复的组合。 

**Example 1:**

> Input: candidates = [10,1,2,7,6,1,5], target = 8,
> A solution set is:
> [
>   [1, 7],
>   [1, 2, 5],
>   [2, 6],
>   [1, 1, 6]
> ]

**Example 2:**

> Input: candidates = [2,5,2,1,2], target = 5,
> A solution set is:
> [
>   [1,2,2],
>   [5]
> ]

## 2.Solutions

回溯算法，也是使用了DFS。

~~~java
public static List<List<Integer>> combinationSum2(int[] nums, int target) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<Integer>(), nums, target, 0);
    return list;

}

public static void backtrack(List<List<Integer>> list, 
        List<Integer> tempList, int[] nums, int remain, int start){
    if(remain < 0) 
        return;
    else if(remain == 0) 
        list.add(new ArrayList<>(tempList));
    else{
        for(int i = start; i < nums.length; i++){
            //不能使用数组中的重复数字，所以可以去掉重复路径。如果可以使用重复数字，那么这一步就不行。
            if(i > start && nums[i] == nums[i-1]) 
                continue;//去除搜索时的重复路径
            tempList.add(nums[i]);
            //i+1表明不能使用数组中的重复数字
            backtrack(list, tempList, nums, remain-nums[i], i+1);
            tempList.remove(tempList.size() - 1); 
        }
    }
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
---
title: 【LeetCode】46. Permutations
categories:
  - 算法与数据结构
  - LeetCode
description: 46. 全排列。主题：回溯算法。难度：中等。
abbrlink: ca8ed476
date: 2019-08-21 12:50:43
tags:
keywords:
---

## 1.题目描述

Given a collection of **distinct** integers, return all possible permutations.

给定一个**没有重复**数字的序列，返回其所有可能的全排列。

**Example:**

> Input: [1,2,3]
> Output:
> [
>   [1,2,3],
>   [1,3,2],
>   [2,1,3],
>   [2,3,1],
>   [3,1,2],
>   [3,2,1]
> ]

## 2.Solutions

~~~java
public static List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    // Arrays.sort(nums); // not necessary
    backtrack(list, new ArrayList<Integer>(), nums);
    return list;
}

private static void backtrack(List<List<Integer>> list, List<Integer> tempList,
                              int[] nums) {
    //打印中间结果，便于理解。
    printList(tempList);
    if (tempList.size() == nums.length) {
        list.add(new ArrayList<>(tempList));
    } else {
        for (int i = 0; i < nums.length; i++) {
            if (tempList.contains(nums[i]))
                continue; // element already exists, skip
            tempList.add(nums[i]);
            backtrack(list, tempList, nums);
            tempList.remove(tempList.size() - 1);
        }
    }
}

private static void printList(List<Integer> list) {
    if (list.size() == 0){
        return;
    }
    for (Integer integer : list) {
        System.out.print(integer);
    }
    System.out.println();
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
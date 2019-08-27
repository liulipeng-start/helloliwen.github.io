---
title: 【LeetCode】41. First Missing Positive
categories:
  - 算法与数据结构
  - LeetCode
description: 41. 缺失的第一个正数。主题：数组。难度：困难。
abbrlink: e81613bd
date: 2019-08-19 17:00:39
tags:
keywords:
---

## 1.题目描述

Given an unsorted integer array, find the smallest missing positive integer.

给定一个未排序的整数数组，找出其中没有出现的最小的正整数。

**Example 1:**

> Input: [1,2,0]
> Output: 3

**Example 2:**

>Input: [3,4,-1,1]
>Output: 2

**Example 3:**

> Input: [7,8,9,11,12]
> Output: 1

**Note:**

Your algorithm should run in *O*(*n*) time and uses constant extra space.

你的算法的时间复杂度应为O(*n*)，并且只能使用常数级别的空间。

## 2.Solutions

　　这里的关键是使用交换来保持恒定的空间并且还利用数组的长度，这意味着最多可以有n个正整数。 所以每次遇到一个有效的整数，找到它的正确位置和交换，否则继续。

~~~java
public static int firstMissingPositive(int[] nums) {
    int i = 0;
    while(i < nums.length){
        if(nums[i] >= 1 && nums[i] <= nums.length && nums[nums[i]-1] != nums[i]) {
            swap(nums, i, nums[i]-1);
        }else{
            i++;
        }
    }
    i = 0;
    while(i < nums.length && nums[i] == i+1) 
        i++;
    return i+1;
}

private static void swap(int[] nums, int i, int j){
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>


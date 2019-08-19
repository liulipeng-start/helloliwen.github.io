---
title: 【LeetCode】35. Search Insert Position
categories:
  - 算法与数据结构
  - LeetCode
description: 35. 搜索插入位置。主题：数组、二分查找。难度：容易。
abbrlink: fa60640c
date: 2019-08-18 15:02:52
tags:
keywords:
---

## 1.题目描述

Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You may assume no duplicates in the array.

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

你可以假设数组中无重复元素。

**Example 1:**

> Input: [1,3,5,6], 5
> Output: 2

**Example 2:**

> Input: [1,3,5,6], 2
> Output: 1

**Example 3:**

> Input: [1,3,5,6], 7
> Output: 4

**Example 4:**

> Input: [1,3,5,6], 0
> Output: 0

## 2.Solutions

~~~java
public static int searchInsert(int[] nums, int target) {
    int low = 0,high = nums.length;
    int mid = 0;
    while(low < high){
        mid = low + (high - low >> 1);// low <= mid < high
        if(nums[mid] >= target){
            high = mid; // high always decreases (even high-low==1)
        }else
            low = mid + 1;//low always increases
    }
    return low;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
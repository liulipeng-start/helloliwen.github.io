---
title: 【LeetCode】33. Search in Rotated Sorted Array
categories:
  - 算法与数据结构
  - LeetCode
description: 33. 搜索旋转排序数组。主题：数组、二分查找。难度：中等。
abbrlink: '66578634'
date: 2019-08-18 10:00:34
tags:
keywords:
---

## 1.题目描述

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., `[0,1,2,4,5,6,7]` might become `[4,5,6,7,0,1,2]`).

You are given a target value to search. If found in the array return its index, otherwise return `-1`.

You may assume no duplicate exists in the array.

Your algorithm's runtime complexity must be in the order of *O*(log *n*).

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

你可以假设数组中不存在重复的元素。

你的算法时间复杂度必须是 O(log n) 级别。

**Example 1:**

> Input: nums = [4,5,6,7,0,1,2], target = 0
> Output: 4

**Example 2:**

> Input: nums = [4,5,6,7,0,1,2], target = 3
> Output: -1

## 2.Solutions

~~~java
public static int search(int[] nums, int target) {
    if(nums.length == 0)
		return -1;
    int minIdx = findMinIdx(nums);
    if (target == nums[minIdx]) 
        return minIdx;
    int len = nums.length;
    int start = (target <= nums[len - 1]) ? minIdx : 0;
    int end = (target > nums[len - 1]) ? minIdx : len - 1;

    while (start <= end) {
        int mid = start + ((end - start) >> 1);
        if (nums[mid] == target) 
            return mid;
        else if (target > nums[mid]) 
            start = mid + 1;
        else 
            end = mid - 1;
    }
    return -1;
}

public static int findMinIdx(int[] nums) {
    int start = 0, end = nums.length - 1;
    while (start < end) {
        int mid = start + ((end -  start) >> 1);
        if (nums[mid] > nums[end]) 
            start = mid + 1;
        else 
            end = mid;
    }
    return start;
}
~~~

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1g63n6z30hzj20qa08nmxp.jpg" align='left' style="zoom:60%"/>

<center><font style="font-weight:bold">（完）</font></center>

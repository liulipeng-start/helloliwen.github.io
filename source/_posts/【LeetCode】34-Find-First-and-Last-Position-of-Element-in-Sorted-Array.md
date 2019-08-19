---
title: 【LeetCode】34. Find First and Last Position of Element in Sorted Array
categories:
  - 算法与数据结构
  - LeetCode
description: 34. 在排序数组中查找元素的第一个和最后一个位置。主题：数组、二分查找。难度：中等。
abbrlink: 2a1e5e50
date: 2019-08-18 11:09:13
tags:
keywords:
---

## 1.题目描述

Given an array of integers `nums` sorted in ascending order, find the starting and ending position of a given `target` value.

Your algorithm's runtime complexity must be in the order of *O*(log *n*).

If the target is not found in the array, return `[-1, -1]`.

给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。

你的算法时间复杂度必须是 O(log n) 级别。

如果数组中不存在目标值，返回 [-1, -1]。

**Example 1:**

> Input: nums = [5,7,7,8,8,10], target = 8
> Output: [3,4]

**Example 2:**

> Input: nums = [5,7,7,8,8,10], target = 6
> Output: [-1,-1]

## 2.Solutions

~~~java
public static int[] searchRange(int[] nums, int target) {
    int start = firstGreaterOrEqual(nums, target);
    if (start == nums.length || nums[start] != target) {
        return new int[]{-1, -1};
    }
    int end = firstGreaterOrEqual(nums, target + 1) - 1;
    return new int[]{start, end};
}

/**
  * find the first number that is greater than or equal to target.
  * could return nums.length if target is greater than nums[nums.length-1].
  * actually this is the same as lower_bound in C++ STL.
  */
private static int firstGreaterOrEqual(int[] nums, int target) {
    int low = 0, high = nums.length;
    while (low < high) {
        int mid = low + (high - low >> 1);
        //low <= mid < high
        if (nums[mid] < target) {
            low = mid + 1;
        } else {
            //should not be mid-1 when nums[mid]==target.
            //could be mid even if nums[mid]>target because mid<high.
            high = mid;
        }
    }
    return low;
}
~~~

target + 1可能造成内存溢出。

测试用例：nums = [1,1,2,2,5,2147483647]，target=2147483647

输出：[5,-1]

因为target+1之后变成了-2147483648，然后第二次调用firstGreaterOrEqual方法返回0，所以end=-1。



附：二分查找

循环版本：

~~~java
public static int binarySearch(int[] arr, int target){
    int result = -1;

    int start = 0,end = arr.length - 1;
    while (start <= end){
        int mid = start + (end - start >> 1);    //防止溢位
        if (arr[mid] > target)
            end = mid - 1;
        else if (arr[mid] < target)
            start = mid + 1;
        else {
            result = mid ;  
            break;
        }
    }
    return result;
}
~~~

递归版本：

~~~java
public static int binarySearch(int[] arr, int start, int end, int target){
    if (start > end)
        return -1;

    int mid = start + (end - start >> 1);//防止溢位
    if (arr[mid] > target)
        return binarySearch(arr, start, mid - 1, target);
    if (arr[mid] < target)
        return binarySearch(arr, mid + 1, end, target);

    return mid;  
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
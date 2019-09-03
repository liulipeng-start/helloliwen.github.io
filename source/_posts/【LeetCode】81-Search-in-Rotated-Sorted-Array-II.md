---
title: 【LeetCode】81. Search in Rotated Sorted Array II
categories:
  - 算法与数据结构
  - LeetCode
description: 81. 搜索旋转排序数组 II。主题：数组、二分查找。难度：中等。
abbrlink: 2554194f
date: 2019-08-31 10:50:02
tags:
keywords:
---

## 1.题目描述

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., `[0,0,1,2,2,5,6]` might become `[2,5,6,0,0,1,2]`).

You are given a target value to search. If found in the array return `true`, otherwise return `false`.

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,0,1,2,2,5,6] 可能变为 [2,5,6,0,0,1,2] )。

编写一个函数来判断给定的目标值是否存在于数组中。若存在返回 true，否则返回 false。

**Example 1:**

> Input: nums = [2,5,6,0,0,1,2], target = 0
> Output: true

**Example 2:**

> Input: nums = [2,5,6,0,0,1,2], target = 3
> Output: false

**Follow up:**

- This is a follow up problem to [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/description/), where `nums` may contain duplicates.
- Would this affect the run-time complexity? How and why?

**进阶:**

- 这是 [搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/description/) 的延伸题目，本题中的 `nums`  可能包含重复元素。
- 这会影响到程序的时间复杂度吗？会有怎样的影响，为什么？

## 2.Solutions

~~~java
public static boolean search(int[] nums, int target) {
    int start  = 0, end = nums.length - 1;

    //check each num so we will check start == end
    //We always get a sorted part and a half part
    //we can check sorted part to decide where to go next
    while(start <= end){
        int mid = start + (end - start >> 1);
        if(nums[mid] == target) return true;

        //if left part is sorted
        if(nums[start] < nums[mid]){
            if(target < nums[start] || target > nums[mid]){
                //target is in rotated part
                start = mid + 1;
            }else{
                end = mid - 1;
            }
        }else if(nums[start] > nums[mid]){
            //right part is rotated

            //target is in rotated part
            if(target < nums[mid] || target > nums[end]){
                end = mid -1;
            }else{
                start = mid + 1;
            }
        }else{
            //duplicates, we know nums[mid] != target, so nums[start] != target
            //based on current information, we can only move left pointer to skip one cell
            //thus in the worest case, we would have target: 2, and array like 11111111, then
            //the running time would be O(n)
            start++;
        }
    }

    return false;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
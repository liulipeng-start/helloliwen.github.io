---
title: 【LeetCode】15. 3Sum
categories:
  - 算法与数据结构
  - LeetCode
description: 15.3个数相加。主题：数组、双指针。难度：中等。
abbrlink: 5f395550
date: 2019-07-09 20:43:18
tags:
keywords:
---

## 1.题目描述

Given an array `nums` of *n* integers, are there elements *a*, *b*, *c* in `nums` such that *a* + *b* + *c* = 0? Find all unique triplets（三胞胎） in the array which gives the sum of zero.

给定一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？找出所有满足条件且不重复的三元组。

**Note:**

The solution set must not contain duplicate triplets.

注意：答案中不可以包含重复的三元组。

**Example:**

> Given array nums = [-1, 0, 1, 2, -1, -4],
>
> A solution set is:
> [
>   [-1, 0, 1],
>   [-1, -1, 2]
> ]

## 2.Solutions

The idea is to sort an input array and then run through all indices（指数） of a possible first element of a triplet. For each possible first element we make a standard bi-directional（双向） 2Sum sweep（扫描） of the remaining part of the array. Also we want to skip equal elements to avoid duplicates in the answer without making a set or something like that.

我们的想法是对输入数组进行排序，然后运行三元组中可能的第一个元素的所有索引。 对于每个可能的第一个元素，我们对阵列的其余部分进行标准的双向2Sum扫描。 此外，我们希望跳过相同的元素，以避免在答案中重复，而不需要设置或类似的东西。

~~~java
public static List<List<Integer>> threeSum(int[] nums) {
    Arrays.sort(nums);
    List<List<Integer>> res = new LinkedList<>(); 
    for (int i = 0; i < nums.length-2; i++) {
        if (i == 0 || nums[i] != nums[i-1] ) {
            int left = i+1, right = nums.length-1, sum = 0 - nums[i];
            while (left < right) {
                if (nums[left] + nums[right] == sum) {
                    res.add(Arrays.asList(nums[i], nums[left], nums[right]));
                    //去除重复
                    while (left < right && nums[left] == nums[left+1]) left++;
                    while (left < right && nums[right] == nums[right-1]) right--;
                    left++; right--;
                }else if (nums[left] + nums[right] < sum){
                    //排序过，所以这里小于必须left++
                    left++;
                }else{
                    right--;
                }
            }
        }
    }
    return res;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>

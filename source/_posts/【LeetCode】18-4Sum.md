---
title: 【LeetCode】18. 4Sum
categories:
  - 算法与数据结构
  - LeetCode
description: 18.4个数之和等于目标值。主题：数组、Hash Table、双指针。难度：中等。
abbrlink: a3390c29
date: 2019-07-12 15:11:22
tags:
keywords:
---

## 1.题目描述

Given an array `nums` of *n* integers and an integer `target`, are there elements *a*, *b*, *c*, and *d* in `nums` such that *a* + *b*+ *c* + *d* = `target`? Find all unique quadruplets（四胞胎） in the array which gives the sum of `target`.

给定一个包含 n 个整数的数组 nums 和一个目标值 target，判断 nums 中是否存在四个元素 a，b，c 和 d ，使得 a + b + c + d 的值与 target 相等？找出所有满足条件且不重复的四元组。

**Note:**

The solution set must not contain duplicate quadruplets.

答案中不可以包含重复的四元组。

**Example:**

> Given array nums = [1, 0, -1, 0, -2, 2], and target = 0.
>
> A solution set is:
> [
>   [-1,  0, 0, 1],
>   [-2, -1, 1, 2],
>   [-2,  0, 0, 2]
> ]

## 2.Solutions

　　If you have already read and implement the 3sum and 4sum by using the sorting approach: reduce them into 2sum at the end, you might already got the feeling that, all ksum problem can be divided into two problems:

1. 2sum Problem
2. Reduce K sum problem to K – 1 sum Problem

Therefore, the ideas is simple and straightforward. We could use recursive（递归） to solve this problem. Time complexity is O(N^(K-1)).

~~~java
public static List<List<Integer>> fourSum(int[] nums, int target) {
    Arrays.sort(nums);
    return kSum(nums, target, 4, 0);
}
private static List<List<Integer>> kSum(int[] nums, int target, int k,int index) {
    int len = nums.length;
    List<List<Integer>> res = new ArrayList<List<Integer>>();
    if (index >= len) {
        return res;
    }
    if (k == 2) {
        int left = index, right = len - 1;
        while (left < right) {
            // find a pair
            if (target - nums[left] == nums[right]) {
                List<Integer> temp = new ArrayList<>();
                temp.add(nums[left]);
                temp.add(target - nums[left]);
                res.add(temp);
                // skip duplication
                while (left < right && nums[left] == nums[left + 1]) left++;
                while (left < right && nums[right - 1] == nums[right]) right--;
                left++;
                right--;
            } else if (target - nums[left] > nums[right]) {
                left++;
            } else {
                right--;
            }
        }
    } else {
        for (int i = index; i < len - k + 1; i++) {
            // use current number to reduce ksum into k-1sum
            List<List<Integer>> temp = kSum(nums, target - nums[i],k-1,i+1);
            if (temp != null) {
                // add previous results
                for (List<Integer> t : temp) {
                    t.add(0, nums[i]);
                }
                res.addAll(temp);
            }
            while (i < len - 1 && nums[i] == nums[i + 1]) i++;
        }
    }
    return res;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>

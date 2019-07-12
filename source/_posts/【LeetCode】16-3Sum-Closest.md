---
title: 【LeetCode】16. 3Sum Closest
categories:
  - 算法与数据结构
  - LeetCode
description: 16. 3数之和最接近目标值的值。主题：数组、双指针
abbrlink: 458f351f
date: 2019-07-11 16:51:30
tags:
keywords:
---

## 1.题目描述

Given an array `nums` of *n* integers and an integer `target`, find three integers in `nums` such that the sum is closest to `target`. Return the sum of the three integers. You may assume that each input would have exactly one solution.

**Example:**

> Given array nums = [-1, 2, 1, -4], and target = 1.
>
> The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).

## 2.Solutions

　　Similar to 3 Sum problem, use 3 pointers to point current element, next element and the last element. If the sum is less than target, it means we have to add a larger element so next element move to the next. If the sum is greater, it means we have to add a smaller element so last element move to the second last element. Keep doing this until the end. Each time compare the difference between sum and target, if it is less than minimum difference so far, then replace result with it, otherwise keep iterating.

~~~java
public static int threeSumClosest(int[] nums, int target){
    Arrays.sort(nums);
    int len = nums.length;
    int sum = nums[0]+nums[1]+nums[len-1];
    int closest = sum;

    for (int i = 0; i < len - 2; i++) {
        if(i==0 || nums[i]!=nums[i-1]){
            int left = i+1,right = len - 1;
            while(left < right){
                sum = nums[i]+nums[left]+nums[right];
                if(sum < target){
                    while(left<right && nums[left]==nums[left+1]) left++;
                    left++;
                }else if (sum > target){
                    //去除重复值
                    while(left<right && nums[right]==nums[right-1]) right--;
                    right--;
                }else{
                    return sum;//及时退出循环
                }
                //更新closest
                if(Math.abs(sum-target) < Math.abs(closest-target)){
                    closest = sum;
                }
            }
        }
    }
    return closest;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
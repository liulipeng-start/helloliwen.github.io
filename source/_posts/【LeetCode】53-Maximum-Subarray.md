---
title: 【LeetCode】53. Maximum Subarray
categories:
  - 算法与数据结构
  - LeetCode
description: 53. 最大子序和（经典动态规划题目）。主题：数组、分治算法、动态规划。难度：容易。
abbrlink: '90764743'
date: 2019-08-23 17:42:55
tags:
keywords:
---

## 1.题目描述

Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**Example:**

> Input: [-2,1,-3,4,-1,2,1,-5,4],
> Output: 6
> Explanation: [4,-1,2,1] has the largest sum = 6.

**Follow up:**

If you have figured out the O(*n*) solution, try coding another solution using the divide and conquer approach, which is more subtle.

**进阶:**

如果你已经实现复杂度为 O(*n*) 的解法，尝试使用更为精妙的分治法求解。

## 2.Solutions

This problem was discussed by Jon Bentley (Sep. 1984 Vol. 27 No. 9 Communications of the ACM P885).

The paragraph below was copied from his paper (with a little modifications):

　　Algorithm that operates on arrays: it starts at the left end (element nums[1]) and scans through to the right end (element nums[n]), keeping track of the maximum sum subvector seen so far. The maximum is initially nums[0]. Suppose we've solved the problem for nums[1 .. i - 1]; how can we extend that to nums[1 .. i]?The maximum sum in the first elements is either the maximum sum in the first i - 1 elements (which we'll call MaxSoFar), or it is that of a subvector that ends in position i (which we'll call MaxEndingHere).

　　MaxEndingHere is either nums[i] plus the previous MaxEndingHere, or just nums[i], whichever is larger.

Jon Bentley（1984年9月第27卷第9期ACM通讯 P885）讨论了这个问题。

以下段落是从他的论文中复制的（稍作修改）：

　　对数组进行操作的算法：它从左端开始（元素nums [1]）并扫描到右端（元素nums [n]），跟踪到目前为止看到的最大和子向量。 最大值最初为nums [0]，假设我们已经解决了nums [1 .. i - 1]的问题。我们怎样才能将它扩展到nums[1 ... i]？第一个元素的最大总和要么是第一个i-1元素（我们称之为MaxSoFar）的最大总和，或者是结束的子向量的总和 在位置i（我们称之为MaxEndingHere）。

　　MaxEndingHere是nums [i]加上之前的MaxEndingHere，或者只是nums [i]，以较大者为准。

~~~java
public static int maxSubArray(int[] nums) {
    int maxSoFar=nums[0], maxEndingHere=nums[0];
    for (int i=1;i<nums.length;++i){
        maxEndingHere= Math.max(maxEndingHere+nums[i],nums[i]);
        maxSoFar=Math.max(maxSoFar, maxEndingHere);	
    }
    return maxSoFar;
}
~~~

DP:

Analysis of this problem:

　　Apparently, this is a optimization problem, which can be usually solved by DP. So when it comes to DP, the first thing for us to figure out is the format of the sub problem(or the state of each sub problem). The format of the sub problem can be helpful when we are trying to come up with the recursive relation.

　　At first, I think the sub problem should look like: `maxSubArray(int nums[], int i, int j)`, which means the maxSubArray for nums[i: j]. In this way, our goal is to figure out what `maxSubArray(nums, 0, nums.length - 1)` is. However, if we define the format of the sub problem in this way, it's hard to find the connection from the sub problem to the original problem(at least for me). In other words, I can't find a way to divided the original problem into the sub problems and use the solutions of the sub problems to somehow create the solution of the original one.

　　So I change the format of the sub problem into something like: `maxSubArray(int nums[], int i)`, which means the maxSubArray for nums[0:i ] which must has nums[i] as the end element. Note that now the sub problem's format is less flexible and less powerful than the previous one because there's a limitation that nums[i] should be contained in that sequence and we have to keep track of each solution of the sub problem to update the global optimal value. However, now the connect between the sub problem & the original one becomes clearer:

分析这个问题：

　　显然，这是一个优化问题，通常可以由DP解决。因此，当谈到DP时，我们要弄清楚的第一件事是子问题的格式（或每个子问题的状态）。当我们试图提出递归关系时，子问题的格式可能会有所帮助。

　　首先，我认为子问题应该是这样的：maxSubArray（int nums[]，int i，int j），这意味着nums [i：j]的maxSubArray。通过这种方式，我们的目标是找出maxSubArray（nums，0，nums.length  -  1）是什么。但是，如果我们以这种方式定义子问题的格式，很难找到从子问题到原始问题的连接（至少对我而言）。换句话说，我找不到将原始问题划分为子问题的方法，并使用子问题的解决方案以某种方式创建原始问题的解决方案。

　　所以我将子问题的格式改为：maxSubArray（int nums[]，int i），这意味着nums [0：i]的maxSubArray必须有nums [i]作为结束元素。请注意，现在子问题的格式不像前一个格式那样灵活且功能不强，因为nums [i]应该包含在该序列中，我们必须跟踪子问题的每个解决方案以更新全局最优值。但是，现在子问题和原始问题之间的联系变得更加清晰：

> maxSubArray(nums, i) = maxSubArray(nums, i - 1) > 0 ? maxSubArray(nums, i - 1) : 0 + nums[i]; 

~~~java
public int maxSubArrayUseDP(int[] nums) {
    int n = nums.length;
    int[] dp = new int[n];//dp[i] means the maximum subarray ending with A[i];
    dp[0] = nums[0];
    int max = dp[0];

    for(int i = 1; i < n; i++){
        dp[i] = nums[i] + (dp[i - 1] > 0 ? dp[i - 1] : 0);
        max = Math.max(max, dp[i]);
    }

    return max;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>

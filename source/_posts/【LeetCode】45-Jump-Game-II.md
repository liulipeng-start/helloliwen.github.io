---
title: 【LeetCode】45. Jump Game II
categories:
  - 算法与数据结构
  - LeetCode
description: 45. 跳跃游戏 II。主题：贪心算法，数组。难度：困难。
abbrlink: dc64a916
date: 2019-08-20 21:46:24
tags:
keywords:
---

## 1.题目描述

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the minimum number of jumps.

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

你的目标是使用最少的跳跃次数到达数组的最后一个位置。

**Example:**

> Input: [2,3,1,1,4]
> Output: 2
> Explanation: The minimum number of jumps to reach the last index is 2.
>     Jump 1 step from index 0 to 1, then 3 steps to the last index.

**Note:**

You can assume that you can always reach the last index.

## 2.Solutions

The main idea is based on greedy. Let's say the range of the current jump is [curBegin, curEnd], curFarthest is the farthest point that all points in [curBegin, curEnd] can reach. Once the current point reaches curEnd, then trigger another jump, and set the new curEnd with curFarthest, then keep the above steps, as the following:

主要思想是基于贪婪。 假设当前跳跃的范围是[curBegin，curEnd]，curFarthest是[curBegin，curEnd]中所有点都可以达到的最远点。 一旦当前点达到curEnd，然后触发另一次跳转，并使用curFarthest设置新的curEnd，然后保持上述步骤，如下所示：

~~~java
public static int jump(int[] nums) {
    int jumps = 0, curEnd = 0, curFarthest = 0;
    for (int i = 0; i < nums.length - 1; i++) {
        curFarthest = Math.max(curFarthest, i + nums[i]);
        if (i == curEnd) {
            jumps++;
            curEnd = curFarthest;
        }
    }
    return jumps;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
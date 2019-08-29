---
title: 【LeetCode】55. Jump Game
categories:
  - 算法与数据结构
  - LeetCode
description: 55. 跳跃游戏（经典贪心算法）。主题：贪心算法、数组。难度：中等。
abbrlink: 3f3e814d
date: 2019-08-28 00:18:11
tags:
keywords:
---

## 1.题目描述

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个位置。

**Example 1:**

> Input: [2,3,1,1,4]
> Output: true
> Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
>
> 从位置 0 到 1 跳 1 步, 然后跳 3 步到达最后一个位置。

**Example 2:**

> Input: [3,2,1,0,4]
> Output: false
> Explanation: You will always arrive at index 3 no matter what. Its maximum
>              jump length is 0, which makes it impossible to reach the last index.
>
> 无论怎样，你总会到达索引为 3 的位置。但该位置的最大跳跃长度是 0 ， 所以你永远不可能到达最后一个位置。

## 2.Solutions

贪心算法：

　　The basic idea is this: at each step, we keep track of the furthest reachable index. The nature of the problem (eg. maximal jumps where you can hit a range of targets instead of singular jumps where you can only hit one target) is that for an index to be reachable, each of the previous indices have to be reachable.

　　Hence, it suffices that we iterate over each index, and If we ever encounter an index that is not reachable, we abort and return false. By the end, we will have iterated to the last index. If the loop finishes, then the last index is reachable.

　　基本思路是：在每一步，我们都会跟踪最远的可达指数。 问题的本质（例如，你可以击中一系列目标的最大跳跃而不是只能击中一个目标的单一跳跃）是一个索引可以到达，每个先前的索引都必须是可达的。

　　因此，我们遍历每个索引就足够了，如果我们遇到一个无法访问的索引，我们就会中止并返回false。 到最后，我们将迭代到最后一个索引。 如果循环结束，则可以访问最后一个索引。

~~~java
//i+nums[i]：你是从特定的索引跳跃，所以你需要i和你可以跳多少步由nums[i]决定。
public static boolean canJump(int[] nums) {
    int reachable = 0;
    for (int i=0; i<nums.length; ++i) {
        if (i > reachable) return false;
        reachable = Math.max(i + nums[i],reachable);
    }
    return true;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
---
title: 【LeetCode】70. Climbing Stairs
categories:
  - 算法与数据结构
  - LeetCode
description: 70. 爬楼梯。主题：动态规划。难度：容易。
abbrlink: dcbc443a
date: 2019-08-29 16:56:20
tags:
keywords:
---

## 1.题目描述

You are climbing a stair case. It takes *n* steps to reach to the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

**Note:** Given *n* will be a positive integer.

假设你正在爬楼梯。需要 *n* 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

**注意：**给定 *n* 是一个正整数。

**Example 1:**

```
Input: 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps
```

**Example 2:**

```
Input: 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```

## 2.Solutions

**Basically it's a fibonacci.**

The problem seems to be a *dynamic programming* one. **Hint**: the tag also suggests that!
Here are the steps to get the solution incrementally.

- Base cases:
  if n <= 0, then the number of ways should be zero.
  if n == 1, then there is only way to climb the stair.
  if n == 2, then there are two ways to climb the stairs. One solution is one step by another; the other one is two steps at one time.
- The key intuition to solve the problem is that given a number of stairs n, if we know the number ways to get to the points `[n-1]` and `[n-2]` respectively, denoted as `n1` and `n2` , then the total ways to get to the point `[n]` is `n1 + n2`. Because from the `[n-1]` point, we can take one single step to reach `[n]`. And from the `[n-2]` point, we could take two steps to get there.
- The solutions calculated by the above approach are ***complete*** and ***non-redundant***. The two solution sets (`n1` and `n2`) cover all the possible cases on how the final step is taken. And there would be NO overlapping among the final solutions constructed from these two solution sets, because they differ in the final step.

Now given the above intuition, one can construct an array where each node stores the solution for each number n. Or if we look at it closer, it is clear that this is basically a fibonacci number, with the starting numbers as 1 and 2, instead of 1 and 1.

~~~java
public int climbStairs(int n) {
    int[] dp = new int[n + 1];
    if (n == 1) {
        return 1;
    }
    if (n == 2) {
        return 2;
    }
    dp[0] = 0;
    dp[1] = 1;
    dp[2] = 2;
    for (int i = 3; i <= n; i++) {
        dp[i] = dp[i-1] + dp[i - 2];
    }
    return dp[n];
}
~~~

补DP基础：

[Python - 3000字长文解释DP基础 | 公瑾™](https://leetcode.com/problems/climbing-stairs/discuss/163347/Python-3000DP-or-tm)

<center><font style="font-weight:bold">（完）</font></center>


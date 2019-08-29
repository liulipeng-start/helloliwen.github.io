---
title: 【LeetCode】63. Unique Paths II
categories:
  - 算法与数据结构
  - LeetCode
description: 63. 不同路径 II。主题：数组，动态规划。难度：中等。
abbrlink: 49b5ab4b
date: 2019-08-29 10:45:38
tags:
keywords:
---

## 1.题目描述

A robot is located at the top-left corner of a *m* x *n* grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

Now consider if some obstacles are added to the grids. How many unique paths would there be?

一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

<img src="https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png" align="left"/>

An obstacle and empty space is marked as `1` and `0` respectively in the grid.

网格中的障碍物和空位置分别用 `1` 和 `0` 来表示。

**Note:** *m* and *n* will be at most 100.

**Example 1:**

> Input:
> [
>   [0,0,0],
>   [0,1,0],
>   [0,0,0]
> ]
> Output: 2
> Explanation:
> There is one obstacle in the middle of the 3x3 grid above.
> There are two ways to reach the bottom-right corner:
> 1. Right -> Right -> Down -> Down
> 2. Down -> Down -> Right -> Right

## 2.Solutions

This is a typical 2D DP problem, we can store value in 2D DP array, but since we only need to use value at dp\[i-1][j] and dp\[i][j-1] to update dp\[i][j], we don't need to store the whole 2D table, but instead store value in an 1D array, and update data by using dp[j] = dp[j] + dp[j - 1] (where here dp[j] corresponding to the dp\[i - 1][j]) and dp[j - 1] corresponding to the dp\[i][j - 1] in the 2D array)

~~~java
public int uniquePathsWithObstacles(int[][] obstacleGrid) {
    int width = obstacleGrid[0].length;
    int[] dp = new int[width];
    dp[0] = 1;
    for (int[] row : obstacleGrid) {
        for (int j = 0; j < width; j++) {
            if (row[j] == 1)
                dp[j] = 0;
            else if (j > 0)
                dp[j] += dp[j - 1];
        }
    }
    return dp[width - 1];
}
~~~

`dp[j] += dp[j - 1];`
is
`dp[j] = dp[j] + dp[j - 1];`
which is `new dp[j] = old dp[j] + dp[j-1]`
which is `current cell = top cell + left cell`

<center><font style="font-weight:bold">（完）</font></center>


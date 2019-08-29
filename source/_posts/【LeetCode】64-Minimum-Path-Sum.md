---
title: 【LeetCode】64. Minimum Path Sum
categories:
  - 算法与数据结构
  - LeetCode
description: 64. 最小路径和。主题：数组、动态规划。难度：中等。
abbrlink: 8d66ee80
date: 2019-08-29 10:53:14
tags:
keywords:
---

## 1.题目描述

Given a *m* x *n* grid filled with non-negative numbers, find a path from top left to bottom right which *minimizes* the sum of all numbers along its path.

给定一个包含非负整数的 *m* x *n* 网格，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

**Note:** You can only move either down or right at any point in time.

**Example:**

> Input:
> [
>   [1,3,1],
>   [1,5,1],
>   [4,2,1]
> ]
> Output: 7
> Explanation: Because the path 1→3→1→1→1 minimizes the sum.

## 2.Solutions

This is a typical DP problem. Suppose the minimum path sum of arriving at point `(i, j)` is `S[i][j]`, then the state equation is `S[i][j] = min(S[i - 1][j], S[i][j - 1]) + grid[i][j]`.

Well, some boundary conditions need to be handled. The boundary conditions happen on the topmost row (`S[i - 1][j]` does not exist) and the leftmost column (`S[i][j - 1]` does not exist). Suppose `grid` is like `[1, 1, 1, 1]`, then the minimum sum to arrive at each point is simply an accumulation of previous points and the result is `[1, 2, 3, 4]`.

I'd like to mention one thing: The reason why DP works here (but not in an actual shortest path problem) is because we can only move right and down through the matrix. If we can move in all 4 directions, DP would give the wrong answer. In that case, only shortest path algorithms such as Dijkstra's or A* work.

~~~java
public int minPathSum(int[][] grid) {
    int m = grid.length;
    int n = grid[0].length;
    for(int i = 1;i<n;i++){
        grid[0][i] += grid[0][i-1];
    }
    for(int i = 1;i<m;i++){
        grid[i][0] += grid[i-1][0];
        for(int j = 1;j<n;j++){
            grid[i][j] += Math.min(grid[i][j-1],grid[i-1][j]);
        }
    }
    return grid[m-1][n-1];
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
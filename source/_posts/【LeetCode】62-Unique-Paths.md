---
title: 【LeetCode】62. Unique Paths
categories:
  - 算法与数据结构
  - LeetCode
description: 62. 不同路径。主题：数组、动态规划。难度：中等。
abbrlink: 5b1babd7
date: 2019-08-29 10:24:01
tags:
keywords:
---

## 1.题目描述

A robot is located at the top-left corner of a *m* x *n* grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

How many possible unique paths are there?

一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

问总共有多少条不同的路径？

<img src="https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png"  align="left" />

Above is a 7 x 3 grid. How many possible unique paths are there?

**Example 1:**

> Input: m = 3, n = 2
> Output: 3
> Explanation:
> From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
> 1. Right -> Right -> Down
> 2. Right -> Down -> Right
> 3. Down -> Right -> Right

**Example 2:**

> Input: m = 7, n = 3
> Output: 28

## 2.Solutions

Since the robot can only move right and down, when it arrives at a point, it either arrives from left or above. If we use `dp[i][j]` for the number of unique paths to arrive at the point `(i, j)`, then the state equation is `dp[i][j] = dp[i][j - 1] + dp[i - 1][j]`. Moreover, we have the base cases `dp[0][j] = dp[i][0] = 1` for all valid `i` and `j`.

~~~java
public int uniquePaths(int rows, int cols) {
    int[][] dp = new int[rows][cols];

    for(int row=0;row<rows;row++)
        dp[row][0] = 1;

    for(int col=0;col<cols;col++)
        dp[0][col] = 1;

    for(int i=1;i<rows;i++)
        for(int j=1;j<cols;j++)
            dp[i][j] = dp[i-1][j] + dp[i][j-1];

    return dp[rows-1][cols-1];
}
~~~

The above solution runs in `O(m * n)` time and costs `O(m * n)` space. However, you may have noticed that each time when we update `dp[i][j]`, we only need `dp[i - 1][j]` (at the previous row) and `dp[i][j - 1]` (at the current row). So we can reduce the memory usage to just two rows (`O(n)`).

~~~java
public int uniquePaths(int rows, int cols) {
    int[] cur = new int[cols];
    int[] pre = new int[cols];

    for(int i=0;i<cols;i++){
        pre[i] = 1;
        cur[i] = 1;            
    }

    for(int i=1;i<rows;i++){
        for(int j=1;j<cols;j++)
            cur[j] = cur[j-1] + pre[j];

        pre = cur;
    }

    return cur[cols-1];
}
~~~

Further inspecting the above code, `pre[j]` is just the `cur[j]` before the update. So we can further reduce the memory usage to one row.

~~~java
public int uniquePaths(int rows, int cols) {
    int[] cur = new int[cols];

    for(int i=0;i<cols;i++)
        cur[i] = 1;            


    for(int i=1;i<rows;i++){
        for(int j=1;j<cols;j++)
            cur[j] = cur[j-1] + cur[j];
    } 
    return cur[cols-1];
}
~~~

Now, you may wonder whether we can further reduce the memory usage to just `O(1)` space since the above code seems to use only two variables (`cur[j]` and `cur[j - 1]`). However, since the whole row `cur` needs to be updated for `m - 1` times (the outer loop) based on old values, all of its values need to be saved and thus `O(1)`-space is impossible. However, if you are having a DP problem without the outer loop and just the inner one, then it will be possible.

现在，您可能想知道我们是否可以进一步将内存使用量减少到O（1）空间，因为上面的代码似乎只使用了两个变量（cur [j]和cur [j  -  1]）。 但是，由于整个行cur需要根据旧值更新m-1次（外循环），因此需要保存所有值，因此O（1）-space是不可能的。 但是，如果你遇到没有外环而只有内环的DP问题，那么它就有可能。

Similar questions:
[91. Decode Ways](https://leetcode.com/problems/decode-ways)
[70. Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)
[509. Fibonacci Number](https://leetcode.com/problems/fibonacci-number/)

<center><font style="font-weight:bold">（完）</font></center>
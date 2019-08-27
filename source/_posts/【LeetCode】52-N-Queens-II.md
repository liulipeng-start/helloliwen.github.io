---
title: 【LeetCode】52. N-Queens II
categories:
  - 算法与数据结构
  - LeetCode
description: 52. N皇后 II。主题：回溯算法。难度：困难。
abbrlink: 57a886ae
date: 2019-08-23 17:32:54
tags:
keywords:
---

## 1.题目描述

The *n*-queens puzzle is the problem of placing *n* queens on an *n*×*n* chessboard such that no two queens attack each other.

*n* 皇后问题研究的是如何将 *n* 个皇后放置在 *n*×*n* 的棋盘上，并且使皇后彼此之间不能相互攻击。

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1g69pcfoz8dj207607o748.jpg" align="left"/>

Given an integer *n*, return the number of distinct solutions to the *n*-queens puzzle.

给定一个整数 *n*，返回 *n* 皇后不同的解决方案的数量。

**Example:**

> Input: 4
> Output: 2
> Explanation: There are two distinct solutions to the 4-queens puzzle as shown below.
> [
>  [".Q..",  // Solution 1
>   "...Q",
>   "Q...",
>   "..Q."],
>
>  ["..Q.",  // Solution 2
>   "Q...",
>   "...Q",
>   ".Q.."]
> ]

## 2.Solutions

This is a classic backtracking problem.

Start row by row, and loop through columns. At each decision point, skip unsafe positions by using three boolean arrays.

Start going back when we reach row n.

Just FYI, if using HashSet, running time will be at least 3 times slower!

~~~java
int count = 0;
public int totalNQueens(int n) {
    boolean[] cols = new boolean[n];     // columns   |
    boolean[] d1 = new boolean[2 * n];   // diagonals \
    boolean[] d2 = new boolean[2 * n];   // diagonals /
    backtracking(0, cols, d1, d2, n);
    return count;
}

public void backtracking(int row, boolean[] cols, boolean[] d1, boolean []d2, int n) {
    if(row == n) count++;

    for(int col = 0; col < n; col++) {
        int id1 = col - row + n;
        int id2 = col + row;
        if(cols[col] || d1[id1] || d2[id2]) continue;

        cols[col] = true; d1[id1] = true; d2[id2] = true;
        backtracking(row + 1, cols, d1, d2, n);
        cols[col] = false; d1[id1] = false; d2[id2] = false;
    }
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
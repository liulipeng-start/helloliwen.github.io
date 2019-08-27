---
title: 【LeetCode】51. N-Queens
categories:
  - 算法与数据结构
  - LeetCode
description: 51. N皇后。主题：回溯算法。难度：困难。
abbrlink: f18869c6
date: 2019-08-23 16:39:39
tags:
keywords:
---

## 1.题目描述

The *n*-queens puzzle is the problem of placing *n* queens on an *n*×*n* chessboard such that no two queens attack each other.

*n* 皇后问题研究的是如何将 *n* 个皇后放置在 *n*×*n* 的棋盘上，并且使皇后彼此之间不能相互攻击。

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1g69pcfoz8dj207607o748.jpg" align="left" />

Given an integer *n*, return all distinct solutions to the *n*-queens puzzle.

Each solution contains a distinct board configuration of the *n*-queens' placement, where `'Q'` and `'.'` both indicate a queen and an empty space respectively.

给定一个整数 n，返回所有不同的 n 皇后问题的解决方案。

每一种解法包含一个明确的 n 皇后问题的棋子放置方案，该方案中 'Q' 和 '.' 分别代表了皇后和空位。

**Example:**

> Input: 4
> Output: [
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
> Explanation: There exist two distinct solutions to the 4-queens puzzle as shown above.

## 2.Solutions

~~~java
public List<List<String>> solveNQueens(int n) {
    char[][] board = new char[n][n];
    for(int i = 0; i < n; i++)
        for(int j = 0; j < n; j++)
            board[i][j] = '.';
    List<List<String>> res = new ArrayList<List<String>>();
    dfs(board, 0, res);
    return res;
}

private void dfs(char[][] board, int colIndex, List<List<String>> res) {
    if(colIndex == board.length) {
        res.add(construct(board));
        return;
    }

    for(int i = 0; i < board.length; i++) {
        if(validate(board, i, colIndex)) {
            board[i][colIndex] = 'Q';
            dfs(board, colIndex + 1, res);
            board[i][colIndex] = '.';
        }
    }
}

private boolean validate(char[][] board, int x, int y) {
    for(int i = 0; i < board.length; i++) {
        for(int j = 0; j < y; j++) {
            if(board[i][j] == 'Q' && (x + j == y + i || x + y == i + j || x == i))
                return false;
        }
    }
    return true;
}

private List<String> construct(char[][] board) {
    List<String> res = new LinkedList<String>();
    for(int i = 0; i < board.length; i++) {
        String s = new String(board[i]);
        res.add(s);
    }
    return res;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>

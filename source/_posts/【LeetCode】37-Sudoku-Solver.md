---
title: 【LeetCode】37. Sudoku Solver
categories:
  - 算法与数据结构
  - LeetCode
description: 37. 解数独。主题：Hash Table，回溯法。难度：困难。
abbrlink: 6b1ed872
date: 2019-08-18 21:44:39
tags:
keywords:
---

## 1.题目描述

Write a program to solve a Sudoku puzzle by filling the empty cells.

A sudoku solution must satisfy **all of the following rules**:

1. Each of the digits `1-9` must occur exactly once in each row.
2. Each of the digits `1-9` must occur exactly once in each column.
3. Each of the the digits `1-9` must occur exactly once in each of the 9 `3x3` sub-boxes of the grid.

Empty cells are indicated by the character `'.'`.

编写一个程序，通过已填充的空格来解决数独问题。

一个数独的解法需遵循如下规则：

数字 1-9 在每一行只能出现一次。

数字 1-9 在每一列只能出现一次。

数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。

空白格用 '.' 表示。

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1g6424lexoej208t08sq39.jpg" align="left" style="zoom:70%"/>

一个数独。

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1g64razxwwzj208w08zq3t.jpg" align="left" style="zoom:70%"/>

答案被标成红色。

**Note:**

- 给定的数独序列只包含数字 `1-9` 和字符 `'.'` 。
- 你可以假设给定的数独只有唯一解。
- 给定数独永远是 `9x9` 形式的。

## 2.Solutions

Straight Forward Java Solution Using Backtracking

Try 1 through 9 for each cell. The time complexity should be 9 ^ m (m represents the number of blanks to be filled in), since each blank can have 9 choices. Details see comments inside code.

~~~java
public void solveSudoku(char[][] board) {
    if(board == null || board.length == 0)
        return;
    solve(board);
}

public boolean solve(char[][] board){
    for(int i = 0; i < board.length; i++){
        for(int j = 0; j < board[0].length; j++){
            if(board[i][j] == '.'){
                for(char c = '1'; c <= '9'; c++){//trial. Try 1 through 9
                    if(isValid(board, i, j, c)){
                        board[i][j] = c; //Put c for this cell

                        if(solve(board))
                            return true; //If it's the solution return true
                        else
                            board[i][j] = '.'; //Otherwise go back
                    }
                }

                return false;
            }
        }
    }
    return true;
}

private boolean isValid(char[][] board, int row, int col, char c){
    for(int i = 0; i < 9; i++) {
        if(board[i][col] != '.' && board[i][col] == c) return false; //check row
        if(board[row][i] != '.' && board[row][i] == c) return false; //check column
        if(board[3 * (row / 3) + i / 3][ 3 * (col / 3) + i % 3] != '.' && 
           board[3 * (row / 3) + i / 3][3 * (col / 3) + i % 3] == c) return false; //check 3*3 block
    }
    return true;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
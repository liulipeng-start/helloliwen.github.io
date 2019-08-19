---
title: 【LeetCode】36. Valid Sudoku
categories:
  - 算法与数据结构
  - LeetCode
description: 36. 有效的数独。主题：Hash Table。难度：中等。
abbrlink: 793fe09c
date: 2019-08-18 19:28:05
tags:
keywords:
---

## 1.题目描述

Determine if a 9x9 Sudoku board is valid. Only the filled cells need to be validated **according to the following rules**:

1. Each row must contain the digits `1-9` without repetition.

2. Each column must contain the digits `1-9` without repetition.

3. Each of the 9 `3x3` sub-boxes of the grid must contain the digits `1-9` without repetition.

判断一个 9x9 的数独是否有效。只需要根据以下规则，验证已经填入的数字是否有效即可。

数字 1-9 在每一行只能出现一次。
数字 1-9 在每一列只能出现一次。
数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1g6424lexoej208t08sq39.jpg" align="left" style="zoom:70%"/>

上图是一个部分填充的有效的数独。

The Sudoku board could be partially filled, where empty cells are filled with the character `'.'`.

数独部分空格内已填入了数字，空白格用 `'.'` 表示。

**Example 1:**

> Input:
> [
>   ["5","3",".",".","7",".",".",".","."],
>   ["6",".",".","1","9","5",".",".","."],
>   [".","9","8",".",".",".",".","6","."],
>   ["8",".",".",".","6",".",".",".","3"],
>   ["4",".",".","8",".","3",".",".","1"],
>   ["7",".",".",".","2",".",".",".","6"],
>   [".","6",".",".",".",".","2","8","."],
>   [".",".",".","4","1","9",".",".","5"],
>   [".",".",".",".","8",".",".","7","9"]
> ]
> Output: true

**Example 2:**

> Input:
> [
> ["8","3",".",".","7",".",".",".","."],
> ["6",".",".","1","9","5",".",".","."],
> [".","9","8",".",".",".",".","6","."],
> ["8",".",".",".","6",".",".",".","3"],
> ["4",".",".","8",".","3",".",".","1"],
> ["7",".",".",".","2",".",".",".","6"],
> [".","6",".",".",".",".","2","8","."],
> [".",".",".","4","1","9",".",".","5"],
> [".",".",".",".","8",".",".","7","9"]
> ]
> Output: false
> Explanation: Same as Example 1, except with the 5 in the top left corner being 
>  modified to 8. Since there are two 8's in the top left 3x3 sub-box, it is invalid.
>
> 解释: 除了第一行的第一个数字从 5 改为 8 以外，空格内其他数字均与 示例1 相同。
>      但由于位于左上角的 3x3 宫内有两个 8 存在, 因此这个数独是无效的。

**Note:**

- A Sudoku board (partially filled) could be valid but is not necessarily solvable.
- Only the filled cells need to be validated according to the mentioned rules.
- The given board contain only digits `1-9` and the character `'.'`.
- The given board size is always `9x9`.

说明:

一个有效的数独（部分已被填充）不一定是可解的。

只需要根据以上规则，验证已经填入的数字是否有效即可。

给定数独序列只包含数字 1-9 和字符 '.' 。

给定数独永远是 9x9 形式的。

## 2.Solutions

~~~java
public boolean isValidSudoku(char[][] board) {
    Set seen = new HashSet();
    for (int i=0; i<9; ++i) {
        for (int j=0; j<9; ++j) {
            char number = board[i][j];
            if (number != '.')
                if (!seen.add(number + " in row " + i) ||
                    !seen.add(number + " in column " + j) ||
                    !seen.add(number + " in block " + i/3 + "-" + j/3))
                    return false;
        }
    }
    return true;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>


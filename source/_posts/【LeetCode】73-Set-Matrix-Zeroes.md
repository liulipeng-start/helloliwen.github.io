---
title: 【LeetCode】73. Set Matrix Zeroes
categories:
  - 算法与数据结构
  - LeetCode
description: 73. 矩阵置零。主题：数组。难度：中等。
abbrlink: 4ff1e209
date: 2019-08-29 20:17:30
tags:
keywords:
---

## 1.题目描述

Given a *m* x *n* matrix, if an element is 0, set its entire row and column to 0. Do it [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm).

给定一个 *m* x *n* 的矩阵，如果一个元素为 0，则将其所在行和列的所有元素都设为 0。请使用**原地**算法。

**Example 1:**

> Input: 
> [
>   [1,1,1],
>   [1,0,1],
>   [1,1,1]
> ]
> Output: 
> [
>   [1,0,1],
>   [0,0,0],
>   [1,0,1]
> ]

**Example 2:**

> Input: 
> [
>   [0,1,2,0],
>   [3,4,5,2],
>   [1,3,1,5]
> ]
> Output: 
> [
>   [0,0,0,0],
>   [0,4,5,0],
>   [0,3,1,0]
> ]

**Follow up:**

- A straight forward solution using O(*m**n*) space is probably a bad idea.
- A simple improvement uses O(*m* + *n*) space, but still not the best solution.
- Could you devise a constant space solution?

进阶:

- 一个直接的解决方案是使用  O(mn) 的额外空间，但这并不是一个好的解决方案。
- 一个简单的改进方案是使用 O(m + n) 的额外空间，但这仍然不是最好的解决方案。
- 你能想出一个常数空间的解决方案吗？

## 2.Solutions

My idea is simple: store states of each row in the first of that row, and store states of each column in the first of that column. Because the state of row0 and the state of column0 would occupy the same cell, I let it be the state of row0, and use another variable "col0" for column0. In the first phase, use matrix elements to set states in a top-down way. In the second phase, use states to set matrix elements in a bottom-up way.

我的想法很简单：在该行的第一行中存储每行的状态，并在该列的第一列中存储每列的状态。 因为row0的状态和column0的状态将占用相同的单元格，所以我让它成为row0的状态，并使用另一个变量“col0”作为column0。 在第一阶段，使用矩阵元素以自上而下的方式设置状态。 在第二阶段，使用状态以自下而上的方式设置矩阵元素。

~~~java
public static void setZeroes(int[][] matrix) {
    if (matrix == null || matrix.length == 0 || matrix[0].length == 0)
        return;

    int col0 = 1, rows = matrix.length, cols = matrix[0].length;
    //第一阶段，使用矩阵元素以自上而下的方式设置状态
    for (int i = 0; i < rows; i++) {
        if (matrix[i][0] == 0) {
            col0 = 0;
        }
        for (int j = 1; j < cols; j++) {
            if (matrix[i][j] == 0) {
                matrix[i][0] = 0;
                matrix[0][j] = 0;
            }
        }
    }
    //第二阶段，使用状态以自下而上的方式设置矩阵元素
    for (int i = rows - 1; i >= 0; i--) {
        for (int j = cols - 1; j >= 1; j--) {
            if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                matrix[i][j] = 0;
            }
        }
        if (col0 == 0) {
            matrix[i][0] = 0;
        }
    }
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
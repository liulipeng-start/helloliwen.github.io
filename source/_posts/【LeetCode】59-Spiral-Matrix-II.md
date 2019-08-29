---
title: 【LeetCode】59. Spiral Matrix II
categories:
  - 算法与数据结构
  - LeetCode
description: 59. 螺旋矩阵 II。主题：数组。难度：中等。
abbrlink: 41cddbb8
date: 2019-08-28 16:47:54
tags:
keywords:
---

## 1.题目描述

Given a positive integer *n*, generate a square matrix filled with elements from 1 to *n*2 in spiral order.

给定一个正整数 *n*，生成一个包含 1 到 *n*2 所有元素，且元素按顺时针顺序螺旋排列的正方形矩阵。

**Example:**

> Input: 3
> Output:
> [
>  [ 1, 2, 3 ],
>  [ 8, 9, 4 ],
>  [ 7, 6, 5 ]
> ]

## 2.Solutions

~~~java
public static int[][] generateMatrix(int n) {
    int[][] ret = new int[n][n];
    int left = 0,top = 0;
    int right = n -1,down = n - 1;
    int count = 1;
    while (left <= right) {

        for (int j = left; j <= right; j++) {
            ret[top][j] = count++;
        }
        top++;

        for (int i = top; i <= down; i++) {
            ret[i][right] = count++;
        }
        right--;

        for (int j = right; j >= left; j--) {
            ret[down][j] = count++;
        }
        down--;

        for (int i = down; i >= top; i--) {
            ret[i][left] = count++;
        }
        left++;
    }
    return ret;
}
~~~

参考：[【LeetCode】54. Spiral Matrix](https://helloliwen.github.io/ca77e76d.html)

<center><font style="font-weight:bold">（完）</font></center>
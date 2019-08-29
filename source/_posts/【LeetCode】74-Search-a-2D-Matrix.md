---
title: 【LeetCode】74. Search a 2D Matrix
categories:
  - 算法与数据结构
  - LeetCode
description: 74. 搜索二维矩阵。主题：数组、二分查找。难度：中等。
abbrlink: 7883fad1
date: 2019-08-29 21:01:17
tags:
keywords:
---

## 1.题目描述

Write an efficient algorithm that searches for a value in an *m* x *n* matrix. This matrix has the following properties:

- Integers in each row are sorted from left to right.

- The first integer of each row is greater than the last integer of the previous row.

编写一个高效的算法来判断 m x n 矩阵中，是否存在一个目标值。该矩阵具有如下特性：

- 每行中的整数从左到右按升序排列。
- 每行的第一个整数大于前一行的最后一个整数。


**Example 1:**

> Input:
> matrix = [
>   [1,   3,  5,  7],
>   [10, 11, 16, 20],
>   [23, 30, 34, 50]
> ]
> target = 3
> Output: true

**Example 2:**

> Input:
> matrix = [
>   [1,   3,  5,  7],
>   [10, 11, 16, 20],
>   [23, 30, 34, 50]
> ]
> target = 13
> Output: false

## 2.Solutions

Use binary search.

n * m matrix convert to an array => matrix\[x][y] => a[x * m + y]	m为一列的长度 

an array convert to n * m matrix => a[x] =>matrix\[x / m][x % m];

~~~java
public boolean searchMatrix(int[][] matrix, int target) {
    if (matrix == null || matrix.length == 0)
        return false;

    int start = 0, rows = matrix.length, cols = matrix[0].length;
    int end = rows * cols - 1;

    while (start <= end) {
        int mid = start + (end - start >> 1);
        if (matrix[mid / cols][mid % cols] == target) {
            return true;
        } 
        if (matrix[mid / cols][mid % cols] < target) {
            start = mid + 1;
        } else {
            end = mid - 1;
        }
    }
    return false;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
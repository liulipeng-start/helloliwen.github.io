---
title: 【LeetCode】54. Spiral Matrix
categories:
  - 算法与数据结构
  - LeetCode
description: 54. 螺旋矩阵。主题：数组。难度：中等。
abbrlink: ca77e76d
date: 2019-08-27 19:11:54
tags:
keywords:
---

## 1.题目描述

Given a matrix of *m* x *n* elements (*m* rows, *n* columns), return all elements of the matrix in spiral order.

给定一个包含 *m* x *n* 个元素的矩阵（*m* 行, *n* 列），请按照顺时针螺旋顺序，返回矩阵中的所有元素。

**Example 1:**

> Input:
> [
>  [ 1, 2, 3 ],
>  [ 4, 5, 6 ],
>  [ 7, 8, 9 ]
> ]
> Output: [1,2,3,6,9,8,7,4,5]

**Example 2:**

> Input:
> [
>   [1, 2, 3, 4],
>   [5, 6, 7, 8],
>   [9,10,11,12]
> ]
> Output: [1,2,3,4,8,12,11,10,9,5,6,7]

## 2.Solutions

This is a very simple and easy to understand solution. I traverse right and increment rowBegin, then traverse down and decrement colEnd, then I traverse left and decrement rowEnd, and finally I traverse up and increment colBegin.

The only tricky part is that when I traverse left or up I have to check whether the row or col still exists to prevent duplicates. If anyone can do the same thing without that check, please let me know!

这是一个非常简单易懂的解决方案。 我向右移动并递增rowBegin，然后向下遍历并递减colEnd，然后我遍历并递减rowEnd，最后我遍历并递增colBegin。

唯一棘手的部分是，当我向左或向上移动时，我必须检查行或列是否仍然存在以防止重复。 如果有人在没有检查的情况下可以做同样的事情，请告诉我！

~~~java
public static List<Integer> spiralOrder(int[][] matrix) {

    List<Integer> res = new ArrayList<Integer>();

    if (matrix.length == 0) 
        return res;

    int top = 0;
    int bottom = matrix.length - 1;
    int left = 0;
    int right = matrix[0].length - 1;

    while (top <= bottom && left <= right) {
        // Traverse Right
        for (int j = left; j <= right; j++) {
            res.add(matrix[top][j]);
        }
        top++;

        // Traverse Down
        for (int j = top; j <= bottom; j++) {
            res.add(matrix[j][right]);
        }
        right--;

        if (top <= bottom) {
            // Traverse Left
            for (int j = right; j >= left; j--) 
                res.add(matrix[bottom][j]);
        }
        bottom--;

        if (left <= right) {
            // Traver Up
            for (int j = bottom; j >= top; j--) 
                res.add(matrix[j][left]);
        }
        left++;
    }

    return res;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
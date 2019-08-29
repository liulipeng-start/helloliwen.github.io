---
title: 【LeetCode】69. Sqrt(x)
categories:
  - 算法与数据结构
  - LeetCode
description: 69. x 的平方根。主题：数学，二分查找。难度：容易。
abbrlink: f5da73fe
date: 2019-08-29 16:27:31
tags:
keywords:
---

## 1.题目描述

Implement `int sqrt(int x)`.

Compute and return the square root of *x*, where *x* is guaranteed to be a non-negative integer.

Since the return type is an integer, the decimal digits are truncated and only the integer part of the result is returned.

实现 int sqrt(int x) 函数。

计算并返回 x 的平方根，其中 x 是非负整数。

由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

**Example 1:**

```
Input: 4
Output: 2
```

**Example 2:**

```
Input: 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since 
             the decimal part is truncated, 2 is returned.
```

## 2.Solutions

牛顿迭代法

~~~java
public static int sqrt(int r) {
    //迭代公式：X(n+1)=[X(n)+r/X(n)]/2
    long x = r;
    while (x * x > r) {
        x = (x + r / x) / 2;
    }
    return (int) x;
}
~~~

二分查找法

~~~java
public static int sqrt2(int x) {
    int left = 1, right = x;
    while (left <= right) {
        int mid = left + ((right - left) >> 1);
        if (mid == x / mid) {
            return mid;
        } else if (mid < x / mid) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return right;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
---
title: '【LeetCode】50. Pow(x, n)'
categories:
  - 算法与数据结构
  - LeetCode
description: '50. Pow(x, n)。主题：数学，二分查找。难度：中等。'
abbrlink: 912017cc
date: 2019-08-23 12:10:31
tags:
keywords:
---

## 1.题目描述

Implement [pow(*x*, *n*)](http://www.cplusplus.com/reference/valarray/pow/), which calculates *x* raised to the power *n* (x<sup>n</sup>).

实现 [pow(*x*, *n*)](https://www.cplusplus.com/reference/valarray/pow/) ，即计算 x 的 n 次幂函数。

**Example 1:**

> Input: 2.00000, 10
> Output: 1024.00000

**Example 2:**

> Input: 2.10000, 3
> Output: 9.26100

**Example 3:**

> Input: 2.00000, -2
> Output: 0.25000
> Explanation: 2<sup>-2</sup> = 1/2<sup>2</sup> = 1/4 = 0.25

**Note:**

- -100.0 < *x* < 100.0
- *n* is a 32-bit signed integer, within the range [−2<sup>31</sup>, 2<sup>31</sup> − 1]

## 2.Solutions

~~~java
public static double pow(double x, int n) {
    if (n == 0)
        return 1;
    if (n < 0) {
        //If n is Integer.MIN_VALUE, the code will have overflow runtime error.
        if (n == Integer.MIN_VALUE) {
            n++;
            n = -n;
            x = 1 / x;
            return x * x * pow(x * x, n / 2);
        }
        n = -n;
        x = 1 / x;
    }
    //偶数判断
    if((n & 1) == 0){
        return pow(x * x, n / 2);
    }else{
        return x * pow(x * x, n / 2);
    }
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
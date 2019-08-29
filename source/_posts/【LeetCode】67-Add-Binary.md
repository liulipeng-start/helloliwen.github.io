---
title: 【LeetCode】67. Add Binary
categories:
  - 算法与数据结构
  - LeetCode
description: 67. 二进制求和。主题：数学、字符串。难度：容易。
abbrlink: ff42b8e0
date: 2019-08-29 15:59:51
tags:
keywords:
---

## 1.题目描述

Given two binary strings, return their sum (also a binary string).

The input strings are both **non-empty** and contains only characters `1` or `0`.

给定两个二进制字符串，返回他们的和（用二进制表示）。

输入为**非空**字符串且只包含数字 `1` 和 `0`。

**Example 1:**

> Input: a = "11", b = "1"
> Output: "100"

**Example 2:**

> Input: a = "1010", b = "1011"
> Output: "10101"

## 2.Solutions

~~~java
public static String addBinary(String a, String b) {
    //Google不变性或字符串vs stringbuilder，如果您不知道我们为什么使用它而不是常规字符串
    StringBuilder sb = new StringBuilder();
    //双指针从后面开始，从后面添加两个常规的数值
    int i = a.length() - 1, j = b.length() -1, carry = 0;//carry 进位
    while (i >= 0 || j >= 0) {
        int sum = carry;//如果有最后一次添加的进位，请将其添加到进位
        if (i >= 0) sum += a.charAt(i--) - '0';
        if (j >= 0) sum += b.charAt(j--) - '0';
        //如果sum为偶数，append 0导致1+1=0，在这种情况下因为这是基数2（就像在列中添加整数时1 + 9是0）
        sb.append(sum % 2);
        //如果sum==2，我们有一个进位，否则1/2等于0，没有进位。
        carry = sum / 2;
    }
    if (carry != 0) sb.append(carry);

    return sb.reverse().toString();
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
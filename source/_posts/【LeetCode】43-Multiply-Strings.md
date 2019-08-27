---
title: 【LeetCode】43. Multiply Strings
categories:
  - 算法与数据结构
  - LeetCode
description: 43. 字符串相乘。主题：数学、字符串。难度：中等。
abbrlink: ac8eaac7
date: 2019-08-20 17:09:49
tags:
keywords:
---

## 1.题目描述

Given two non-negative integers `num1` and `num2` represented as strings, return the product of `num1` and `num2`, also represented as a string.

给定两个以字符串形式表示的非负整数 `num1` 和 `num2`，返回 `num1` 和 `num2` 的乘积，它们的乘积也表示为字符串形式。

**Example 1:**

> Input: num1 = "2", num2 = "3"
> Output: "6"

**Example 2:**

> Input: num1 = "123", num2 = "456"
> Output: "56088"

**Note:**

1. The length of both `num1` and `num2` is < 110.
2. Both `num1` and `num2` contain only digits `0-9`.
3. Both `num1` and `num2` do not contain any leading zero, except the number 0 itself.
4. You **must not use any built-in BigInteger library** or **convert the inputs to integer** directly.

## 2.Solutions

Remember how we do multiplication?

Start from right to left, perform multiplication on every pair of digits, and add them together. Let's draw the process! From the following draft, we can immediately conclude:

从右到左开始，对每对数字执行乘法运算，并将它们加在一起。 让我们画出这个过程吧！ 从以下草案中，我们可以立即得出结论：

```
 `num1[i] * num2[j]` will be placed at indices `[i + j`, `i + j + 1]` 
```

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1g669pmilrij20qx0lxgn1.jpg" align="left" style="zoom:50%"/>

## 2.Solutions

~~~java
public static String multiply(String num1, String num2) {
    int m = num1.length(), n = num2.length();
    int[] indices = new int[m + n];

    for(int i = m - 1; i >= 0; i--) {
        for(int j = n - 1; j >= 0; j--) {
            int mul = (num1.charAt(i) - '0') * (num2.charAt(j) - '0'); 
            int p1 = i + j, p2 = i + j + 1;
            int sum = mul + indices[p2];

            indices[p1] += sum / 10;
            indices[p2] = sum % 10;
        }
    }  

    StringBuilder sb = new StringBuilder();
    for(int p : indices) 
        //去掉前面的0
        if(!(sb.length() == 0 && p == 0)) 
            sb.append(p);
    return sb.length() == 0 ? "0" : sb.toString();
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
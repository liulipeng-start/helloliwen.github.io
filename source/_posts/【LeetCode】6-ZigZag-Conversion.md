---
title: 【LeetCode】6. ZigZag Conversion
categories:
  - 算法与数据结构
  - LeetCode
description: 6. Z字形变换，主题：字符串，难度：中等
abbrlink: d0654adb
date: 2019-07-29 21:09:16
tags:
keywords:
---

## 1.题目描述

The string `"PAYPALISHIRING"` is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)

将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 `"PAYPALISHIRING"` 行数为 3 时，排列如下：

```
P   A   H   N
A P L S I I G
Y   I   R
```

And then read line by line: `"PAHNAPLSIIGYIR"`

之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如：`"PAHNAPLSIIGYIR"`

Write the code that will take a string and make this conversion given a number of rows:

```
string convert(string s, int numRows);
```

**Example 1:**

```
Input: s = "PAYPALISHIRING", numRows = 3
Output: "PAHNAPLSIIGYIR"
```

**Example 2:**

```
Input: s = "PAYPALISHIRING", numRows = 4
Output: "PINALSIGYAHRPI"
Explanation:

P     I    N
A   L S  I G
Y A   H R
P     I
```

## 2.Solutions

~~~java
public static String convert(String s, int numRows) {
    char[] c = s.toCharArray();
    int len = c.length;
    StringBuffer[] sb = new StringBuffer[numRows];
    for (int i = 0; i < sb.length; i++)
        sb[i] = new StringBuffer();

    int i = 0;
    while (i < len) {
        for (int row = 0; row < numRows && i < len; row++)
            sb[row].append(c[i++]);// vertically down 垂直向下

        for (int row = numRows - 2; row >= 1 && i < len; row--) {
            sb[row].append(c[i++]);// obliquely up 斜向上
        }
    }
    for (int row = 1; row < sb.length; row++)
        sb[0].append(sb[row]);

    return sb[0].toString();
}
~~~

<center><font style="font-weight:bold">（完）</font></center>

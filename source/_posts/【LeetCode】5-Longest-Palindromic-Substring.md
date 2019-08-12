---
title: 【LeetCode】5. Longest Palindromic Substring
categories:
  - 算法与数据结构
  - LeetCode
description: 5.最长回文子串。主题：字符串，动态规划。难度：中等。
abbrlink: 96028b45
date: 2019-07-16 20:15:10
tags:
keywords:
---

## 1.题目描述

Given a string **s**, find the longest palindromic substring in **s**. You may assume that the maximum length of **s** is 1000.

给定一个字符串 `s`，找到 `s` 中最长的回文子串。你可以假设 `s` 的最大长度为 1000。

**Example 1:**

> Input: "babad"
> Output: "bab"
> Note: "aba" is also a valid answer.

**Example 2:**

> Input: "cbbd"
> Output: "bb"

## 2.Solutions

　　`dp(i, j)` represents whether `s(i ... j)` can form a palindromic substring, `dp(i, j)` is true when `s(i)` equals to `s(j)` and `s(i+1 ... j-1)` is a palindromic substring. When we found a palindrome, check if it's the longest one. Time complexity O(n^2).

~~~java
public static String longestPalindrome(String s) {
    int n = s.length();
    int palindromeStartsAt = 0, maxLen = 0;
    //dp[i][j]表示从i开始到j结束的子串是否是回文
    boolean[][] dp = new boolean[n][n];

    for (int i = n - 1; i >= 0; i--) {
        for (int j = i; j < n; j++) { //找到(i,j)中最大的回文串
            //s.charAt(i)==s.charAt(j)：判断在(i,j)区间中是否是回文串
            //j-i<3:如果(i,j)的范围小于等于3，那么只有结束字符匹配
            //dp[i+1][j-1]:如果范围大于3，那么(i+1,j-1)也是回文串
            dp[i][j] = s.charAt(i)==s.charAt(j) && (j-i<3 || dp[i+1][j-1]);
            //更新最大回文字符串
            if (dp[i][j] && (j-i+1 > maxLen)) {
                palindromeStartsAt = i;
                maxLen = j-i+1;
            }
        }
    }
    return s.substring(palindromeStartsAt, palindromeStartsAt+maxLen);
}
~~~

<center><font style="font-weight:bold">（完）</font></center>

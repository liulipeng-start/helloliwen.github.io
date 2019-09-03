---
title: 【LeetCode】97. Interleaving String
categories:
  - 算法与数据结构
  - LeetCode
description: 97. 交错字符串。主题：字符串、动态规划。难度：困难。
abbrlink: '51767899'
date: 2019-09-03 14:33:53
tags:
keywords:
---

## 1.题目描述

Given *s1*, *s2*, *s3*, find whether *s3* is formed by the interleaving of *s1* and *s2*.

给定三个字符串 *s1*, *s2*, *s3*, 验证 *s3* 是否是由 *s1* 和 *s2* 交错组成的。

**Example 1:**

> Input: s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"
> Output: true

**Example 2:**

> Input: s1 = "aabcc", s2 = "dbbca", s3 = "aadbbbaccc"
> Output: false

## 2.Solutions

Here is some explanation:

DP table represents if s3 is interleaving at (i+j)th position when s1 is at ith position, and s2 is at jth position. 0th position means empty string.

So if both s1 and s2 is currently empty, s3 is empty too, and it is considered interleaving. If only s1 is empty, then if previous s2 position is interleaving and current s2 position char is equal to s3 current position char, it is considered interleaving. similar idea applies to when s2 is empty. when both s1 and s2 is not empty, then if we arrive i, j from i-1, j, then if i-1,j is already interleaving and i and current s3 position equal, it s interleaving. If we arrive i,j from i, j-1, then if i, j-1 is already interleaving and j and current s3 position equal. it is interleaving.

~~~java
public boolean isInterleave(String s1, String s2, String s3) {
    if(s3.length() != s1.length() + s2.length())
        return false;

    boolean[][] table = new boolean[s1.length()+1][s2.length()+1];

    for(int i=0; i<s1.length()+1; i++)
        for(int j=0; j< s2.length()+1; j++){
            if(i==0 && j==0)
                table[i][j] = true;
            else if(i == 0)
                table[i][j] = ( table[i][j-1] && s2.charAt(j-1) == s3.charAt(i+j-1));
            else if(j == 0)
                table[i][j] = ( table[i-1][j] && s1.charAt(i-1) == s3.charAt(i+j-1));
            else
                table[i][j] = (table[i-1][j] && s1.charAt(i-1) == s3.charAt(i+j-1) ) 
                || (table[i][j-1] && s2.charAt(j-1) == s3.charAt(i+j-1) );
        }

    return table[s1.length()][s2.length()];
}
~~~

参考：[Summary of solutions, BFS, DFS, DP](https://leetcode.com/problems/interleaving-string/discuss/31904/Summary-of-solutions-BFS-DFS-DP)

<center><font style="font-weight:bold">（完）</font></center>
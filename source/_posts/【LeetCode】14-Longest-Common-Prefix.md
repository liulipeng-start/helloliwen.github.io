---
title: 【LeetCode】14. Longest Common Prefix
categories:
  - 算法与数据结构
  - LeetCode
description: 14. 最长公共前缀。主题：字符串，难度：容易。
abbrlink: e4d324d5
date: 2019-08-15 21:50:33
tags:
keywords:
---

## 1.题目描述

Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string `""`.

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 `""`。

**Example 1:**

> Input: ["flower","flow","flight"]
> Output: "fl"

**Example 2:**

> Input: ["dog","racecar","car"]
> Output: ""
> Explanation: There is no common prefix among the input strings.

**Note:**

All given inputs are in lowercase letters `a-z`.

## 2.Solutions

~~~java
public static String longestCommonPrefix(String[] strs) {
    if(strs == null || strs.length == 0)    
        return "";
    String pre = strs[0];
    for (int i = 1; i < strs.length; i++) {
        //Meanwhile startsWith() is faster than indexOf() in JAVA world
        while(!strs[i].startsWith(pre))
            pre = pre.substring(0,pre.length()-1);
        //第一轮结束，pre为空字符串，那么就没有最长相同公共前缀，
        if(pre.equals("")) 
            return "";
    }
    return pre;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>


---
title: 【LeetCode】3.Longest Substring Without Repeating Characters
categories:
  - 算法与数据结构
  - LeetCode
description: 3.最长非重复子串，主题：Hash Table，双指针，字符串，滑动窗口。难度：中等。
abbrlink: d9fa86f8
date: 2019-07-05 10:59:27
tags:
keywords:
---

## 1.题目描述

Given a string, find the length of the **longest substring** without repeating characters.

给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。

**Example 1:**

> Input: "abcabcbb"
> Output: 3 
> Explanation: The answer is "abc", with the length of 3. 

**Example 2:**

>Input: "bbbbb"
>Output: 1
>Explanation: The answer is "b", with the length of 1.

**Example 3:**

> Input: "pwwkew"
> Output: 3
> Explanation: The answer is "wke", with the length of 3. 
>           Note that the answer must be a substring, "pwke" is a subsequence and not a substring.
>
> 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
>      请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。

　　乍一看这道题目的时候，直接哇的一声，因为今年投大厂实习的时候，在线笔试碰到这道题。日拱一卒！加油！

　　附上一段LeetCode评论：

> By the way, I was offered a job at Google, and I work there as a SWE now. Guys, practice leet code oj online, it really really helps!

## 2.Solutions

　　作者思路：使用一个HashMap存储字符串：key为字符，value为位置。并且使用两个指针i、j用来定义最大子串。使用指针i遍历整个数组，同时更新hashmap：如果字符出现在hashmap中，则移动指针j到上次找到的同样字符串加1的位置。注意：两个指针只能往前移动。

~~~java
public static int lengthOfLongestSubstring(String s) {
    if (s.length()==0) 
        return 0;
    HashMap<Character, Integer> map = new HashMap<Character, Integer>();
    int max=0;
    for (int i=0, j=0; i<s.length(); ++i){
        if(map.containsKey(s.charAt(i))){
            //这里为什么要这么写，光看是很难看懂的
            //引用一句话：This issue is hard to explain unless you write it!
            j = Math.max(j, map.get(s.charAt(i))+1);
        }
        map.put(s.charAt(i),i);
        max = Math.max(max,i-j+1);
    }
    return max;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
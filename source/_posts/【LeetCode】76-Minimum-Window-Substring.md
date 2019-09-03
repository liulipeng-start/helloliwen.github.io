---
title: 【LeetCode】76. Minimum Window Substring
categories:
  - 算法与数据结构
  - LeetCode
description: 76. 最小覆盖子串。主题：Hash Table、双指针、字符串、滑动窗口。难度：困难。
abbrlink: 2826ec56
date: 2019-08-30 15:39:36
tags:
keywords:
---

## 1.题目描述

Given a string S and a string T, find the minimum window in S which will contain all the characters in T in complexity O(n).

给你一个字符串 S、一个字符串 T，请在字符串 S 里面找出：包含 T 所有字母的最小子串。

**Example:**

> Input: S = "ADOBECODEBANC", T = "ABC"
> Output: "BANC"

**Note:**

- If there is no such window in S that covers all characters in T, return the empty string `""`.
- If there is such window, you are guaranteed that there will always be only one unique minimum window in S.

**说明：**

- 如果 S 中不存这样的子串，则返回空字符串 `""`。
- 如果 S 中存在这样的子串，我们保证它是唯一的答案。

## 2.Solutions

~~~java
public String minWindow(String s, String t) {
    if(s == null || s.length() < t.length() || s.length() == 0)
        return "";
    
    HashMap<Character,Integer> map = new HashMap<Character,Integer>();
    for(char c : t.toCharArray()){
        if(map.containsKey(c)){
            map.put(c,map.get(c)+1);
        }else{
            map.put(c,1);
        }
    }
    int left = 0;
    int minLeft = 0;
    int minLen = s.length()+1;
    int count = 0;
    for(int right = 0; right < s.length(); right++){
        if(map.containsKey(s.charAt(right))){
            map.put(s.charAt(right),map.get(s.charAt(right))-1);
            if(map.get(s.charAt(right)) >= 0){
                count++;
            }
            while(count == t.length()){
                if(right-left+1 < minLen){
                    minLeft = left;
                    minLen = right-left+1;
                }
                if(map.containsKey(s.charAt(left))){
                    map.put(s.charAt(left),map.get(s.charAt(left))+1);
                    if(map.get(s.charAt(left)) > 0){
                        count--;
                    }
                }
                left++;
            }
        }
    }
    if(minLen>s.length())  
        return "";      
    
    return s.substring(minLeft,minLeft+minLen);
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
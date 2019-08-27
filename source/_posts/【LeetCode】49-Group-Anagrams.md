---
title: 【LeetCode】49. Group Anagrams
categories:
  - 算法与数据结构
  - LeetCode
description: 49. 字母异位词分组。主题：Hash Table，字符串。难度：中等。
abbrlink: 57c5ba
date: 2019-08-23 11:47:30
tags:
keywords:
---

## 1.题目描述

Given an array of strings, group anagrams together.

给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

**Example:**

> Input: ["eat", "tea", "tan", "ate", "nat", "bat"],
> Output:
> [
>   ["ate","eat","tea"],
>   ["nat","tan"],
>   ["bat"]
> ]

**Note:**

- All inputs will be in lowercase.
- The order of your output does not matter.

**说明：**

- 所有输入均为小写字母。
- 不考虑答案输出的顺序。

## 2.Solutions

~~~java
public static List<List<String>> groupAnagrams(String[] strs) {
    if (strs == null || strs.length == 0) 
        return new ArrayList<List<String>>();
    Map<String, List<String>> map = new HashMap<String, List<String>>();
    for (String s : strs) {
        char[] ca = s.toCharArray();
        Arrays.sort(ca);
        String keyStr = String.valueOf(ca);
        if (!map.containsKey(keyStr)) 
            map.put(keyStr, new ArrayList<String>());
        map.get(keyStr).add(s);
    }
    return new ArrayList<List<String>>(map.values());
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
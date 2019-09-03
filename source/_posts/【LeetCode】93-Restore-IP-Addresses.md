---
title: 【LeetCode】93. Restore IP Addresses
categories:
  - 算法与数据结构
  - LeetCode
description: 93. 复原IP地址。主题：字符串、回溯算法。难度：中等。
abbrlink: 24c97930
date: 2019-09-03 11:12:52
tags:
keywords:
---

## 1.题目描述

Given a string containing only digits, restore it by returning all possible valid IP address combinations.

给定一个只包含数字的字符串，复原它并返回所有可能的 IP 地址格式。

**Example:**

> Input: "25525511135"
> Output: ["255.255.11.135", "255.255.111.35"]

## 2.Solutions

这道题也是一个用DFS找所有的可能性的问题。这样一串数字：`25525511135`，我们可以对他进行切分，但是根据IP的性质，有些切分就是明显不可能的：比如`011.11.11.11`，这个的问题是以`0`开头了，不合法，直接跳过。比如`257.11.11.11`, 257大于IP里的最大数 255了，不合法，直接跳过。然后我们把这一层切分后剩下的字符串传到下一层，继续去找。直到最后我们得到4个section为止，把这个结果存到我们的result list。

~~~java
public static List<String> restoreIpAddresses(String s) {
    List<String> solutions = new ArrayList<String>();
    dfs(s, solutions, 0, "", 0);
    return solutions;
}
//回溯
private static void dfs(String ip, List<String> solutions, int idx, String restored, int count) {
    if (count > 4) return;
    if (count == 4 && idx == ip.length()) solutions.add(restored);

    for (int i=1; i<4; i++) {
        if (idx+i > ip.length()) break;
        String s = ip.substring(idx,idx+i);
        //剪枝
        if ((s.startsWith("0") && s.length()>1) || (i==3 && Integer.parseInt(s) >= 256)) continue;
        dfs(ip, solutions, idx+i, restored+s+(count==3?"" : "."), count+1);
    }
}
~~~

第二种回溯：

~~~java
public static ArrayList<String> restoreIpAddresses2(String s) {
    ArrayList<String> res = new ArrayList<>();
    ArrayList<String> ip = new ArrayList<>(); // 存放中间结果
    dfs3(s, res, ip, 0);
    return res;
}

private static void dfs3(String s, ArrayList<String> res,
                         ArrayList<String> ip, int start) {
    if (ip.size() == 4 && start == s.length()) { // 找到一个合法解
        res.add(ip.get(0) + '.' + ip.get(1) + '.' + ip.get(2) + '.'
                + ip.get(3));
        return;
    }
    if (s.length() - start > 3 * (4 - ip.size())) // 剪枝
        return;
    if (s.length() - start < (4 - ip.size())) // 剪枝
        return;

    int num = 0;
    for (int i = start; i < start + 3 && i < s.length(); i++) {
        num = num * 10 + (s.charAt(i) - '0');
        if (num < 0 || num > 255) // 剪枝
            continue;
        ip.add(s.substring(start, i + 1));
        dfs3(s, res, ip, i + 1);
        ip.remove(ip.size() - 1);

        if (num == 0) // 不允许前缀0
            break;
    }
}
~~~

<center><font style="font-weight:bold">（完）</font></center>


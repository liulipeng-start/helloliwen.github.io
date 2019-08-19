---
title: 【LeetCode】22. Generate Parentheses
categories:
  - 算法与数据结构
  - LeetCode
description: 22. 括号生成。主题：字符串，回溯。难度：中等。
abbrlink: b2297cf9
date: 2019-08-17 10:54:03
tags:
keywords:
---

## 1.题目描述

Given *n* pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

For example, given *n* = 3, a solution set is:

给出 *n* 代表生成括号的对数，请你写出一个函数，使其能够生成所有可能的并且**有效的**括号组合。

例如，给出 *n* = 3，生成结果为：

> [
>   "((()))",
>   "(()())",
>   "(())()",
>   "()(())",
>   "()()()"
> ]

## 2.Solutions

~~~java
/**
  * The idea here is to only add '(' and ')' 
  * that we know will guarantee us a solution (instead of adding 1 too many close). 
  * Once we add a '(' we will then discard it and try a ')' which can only close a valid '('. 
  * Each of these steps are recursively called.
  */
public static List<String> generateParenthesis(int n) {
    List<String> list = new ArrayList<String>();
    backtrack(list, "", 0, 0, n);
    return list;
}

public static void backtrack(List<String> list, String str, int open, int close, int max){
    if(str.length() == max*2){
        list.add(str);
        return;
    }
    if(open < max)
        backtrack(list, str+"(", open+1, close, max);
    if(close < open)
        backtrack(list, str+")", open, close+1, max);
}
~~~

　　I would like to share my understanding about this algorithm. Hope will make people easier to understand the beauty of this code. Chip in your ideas if I am wrong.

　　The goal is to print a string of “(“ ,”)” in certain order. The length of string is 2n. The constraints(限制) are that “(“s need to match “)”s.
　　Without constraints, we just simply print out “(“ or “)” until length hits n. So the base case will be length ==2n, recursive(递归) case is print out “(“ and “)”. The code will look like

~~~java
//base case
if(string length == 2*n) {
    add(string);
    return;
}
//recursive case
add a “(“
add a “)"
~~~

　　Let’s add in constraints now. We need to interpret the meanings of constraints. 

　　First, the first character should be “(“. 

　　Second, at each step, you can either print “(“ or “)”, but print “)” only when there are more “(“s than “)”s. 

　　Stop printing out “(“ when the number of “(“ s hit n. The first actually merges into the second condition.

　　The code will be:

~~~java
//base case
if(string length == 2*n) {
	add(string);
	return;
}
//recursive case
if(number of “(“s < n){
	add a “(“
}
if(number of “(“s > number of “)”s){
	add a “)"
}
~~~

程序运行图：

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g62pjbdr7kj20vl0j1tao.jpg)

<center><font style="font-weight:bold">（完）</font></center>


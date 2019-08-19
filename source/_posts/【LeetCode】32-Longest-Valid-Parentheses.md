---
title: 【LeetCode】32. Longest Valid Parentheses
categories:
  - 算法与数据结构
  - LeetCode
description: 32. 最长有效括号。主题：字符串、动态规划。难度：困难。
abbrlink: 826765dd
date: 2019-08-18 09:07:44
tags:
keywords:
---

## 1.题目描述

Given a string containing just the characters `'('` and `')'`, find the length of the longest valid (well-formed) parentheses substring.

给定一个只包含 `'('` 和 `')'` 的字符串，找出最长的包含有效括号的子串的长度。

**Example 1:**

> Input: "(()"
> Output: 2
> Explanation: The longest valid parentheses substring is "()"

**Example 2:**

> Input: ")()())"
> Output: 4
> Explanation: The longest valid parentheses substring is "()()"

## 2.Solutions

This is my DP solution, just one pass

V[i] represents number of valid parentheses matches from S[j to i] (j<i)

V[i] = V[i-1] + 2 + previous matches V[i- (V[i-1] + 2) ] if S[i] = ')' and '(' count > 0

~~~java
public int longestValidParentheses(String s) {
    char[] S = s.toCharArray();
    int[] V = new int[S.length];
    int open = 0;
    int max = 0;
    for (int i=0; i<S.length; i++) {
        if (S[i] == '(') open++;
        if (S[i] == ')' && open > 0) {
            V[i] = 2+ V[i-1];// matches found
            if(i-V[i]>0)// add matches from previous
                V[i] += V[i-V[i]];
            open--;
        }
        if (V[i] > max) 
            max = V[i];
    }
    return max;
}
~~~

The idea is simple, we only update the result (max) when we find a "pair".

If we find a pair. We throw this pair away and see how big the gap is between current and previous invalid.

EX: "( )( )"

stack: -1, 0,

when we get to index 1 ")", the peek is "(" so we pop it out and see what's before "(".

In this example it's -1. So the gap is "current_index" - (-1) = 2.

The idea **only update the result (max) when we find a "pair"** and **push -1 to stack first** covered all edge cases.

~~~java
public int longestValidParenthesesUseStack(String s) {
    LinkedList<Integer> stack = new LinkedList<>();
    int result = 0;
    stack.push(-1);
    for (int i = 0; i < s.length(); i++) {
        if (s.charAt(i) == ')' && stack.size() > 1 && s.charAt(stack.peek()) == '(') {
            stack.pop();
            result = Math.max(result, i - stack.peek());
        } else {
            stack.push(i);
        }
    }
    return result;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>


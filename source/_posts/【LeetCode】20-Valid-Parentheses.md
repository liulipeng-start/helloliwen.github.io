---
title: 【LeetCode】20. Valid Parentheses
categories:
  - 算法与数据结构
  - LeetCode
description: 20. 有效的括号。主题：栈、字符串。难度：容易。
abbrlink: 8ccacc1f
date: 2019-08-16 21:23:36
tags:
keywords:
---

## 1.题目描述

Given a string containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.

Note that an empty string is also considered valid.

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。

左括号必须以正确的顺序闭合。

注意空字符串可被认为是有效字符串。

**Example 1:**

> Input: "()"
> Output: true

**Example 2:**

> Input: "()[]{}"
> Output: true

**Example 3:**

> Input: "(]"
> Output: false

**Example 4:**

> Input: "([)]"
> Output: false

**Example 5:**

> Input: "{[]}"
> Output: true

## 2.Solutions

~~~java
public static boolean isValid(String s) {
    //按位与运算符，只有对应的两个二进位均为1时，结果位才为1，否则为0。
    //s.length() & 1 = 1,s.length()为奇数
    if((s.length() & 1) == 1) 
        return false;
    Stack<Character> stack = new Stack<Character>();
    for (char c : s.toCharArray()) {
        //目前只有三种符号，扩展的话可以使用HashMap
        switch (c) {
            case '(': stack.push(')'); break;
            case '{': stack.push('}'); break;
            case '[': stack.push(']'); break;
            case ')': 
            case '}': 
            case ']': if(stack.isEmpty() || stack.pop() != c) return false;
        }
    }
    return stack.isEmpty();
}
~~~

<center><font style="font-weight:bold">（完）</font></center>

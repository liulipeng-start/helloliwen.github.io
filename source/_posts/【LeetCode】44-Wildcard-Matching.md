---
title: 【LeetCode】44. Wildcard Matching
categories:
  - 算法与数据结构
  - LeetCode
description: 44. 通配符匹配。主题：贪心算法、字符串、动态规划、回溯算法。难度：困难。
abbrlink: 7463a464
date: 2019-08-20 21:14:32
tags:
keywords:
---

## 1.题目描述

Given an input string (`s`) and a pattern (`p`), implement wildcard pattern matching with support for `'?'`and `'*'`.

```
'?' Matches any single character.
'*' Matches any sequence of characters (including the empty sequence).
```

The matching should cover the **entire** input string (not partial).

给定一个字符串 (s) 和一个字符模式 (p) ，实现一个支持 '?' 和 '*' 的通配符匹配。

> '?' 可以匹配任何单个字符。
> '*' 可以匹配任意字符串（包括空字符串）。

两个字符串完全匹配才算匹配成功。

**Note:**

- `s` could be empty and contains only lowercase letters `a-z`.

- `p` could be empty and contains only lowercase letters `a-z`, and characters like `?` or `*`.

**说明:**

- `s` 可能为空，且只包含从 `a-z` 的小写字母。
- `p` 可能为空，且只包含从 `a-z` 的小写字母，以及字符 `?` 和 `*`。

**Example 1:**

> Input:
> s = "aa"
> p = "a"
> Output: false
> Explanation: "a" does not match the entire string "aa".

**Example 2:**

> Input:
> s = "aa"
> p = "\*"
> Output: true
> Explanation: '*' matches any sequence.

**Example 3:**

> Input:
> s = "cb"
> p = "?a"
> Output: false
> Explanation: '?' matches 'c', but the second letter is 'a', which does not match 'b'.

**Example 4:**

> Input:
> s = "adceb"
> p = "\*a\*b"
> Output: true
> Explanation: The first '\*' matches the empty sequence, while the second '*' matches the substring "dce".

**Example 5:**

> Input:
> s = "acdcb"
> p = "a*c?b"
> Output: false

## 2.Solutions

The original post has DP 2d array index from high to low, which is not quite intuitive.

Below is another 2D dp solution. Ideal is identical.

dp[i][j] denotes whether s[0....i-1] matches p[0.....j-1],

First, we need to initialize dp[i][0], i= [1,m]. All the dp[i][0] should be false because p has nothing in it.

Then, initialize dp[0][j], j = [1, n]. In this case, s has nothing, to get dp[0][j] = true, p must be '*', '**', '***',etc. Once p.charAt(j-1) != '*', all the dp[0][j] afterwards will be false.

Then start the typical DP loop.

Though this solution is clear and easy to understand. It is not good enough in the interview. it takes O(mn) time and O(mn) space.

Improvement: 1) optimize 2d dp to 1d dp, this will save space, reduce space complexity to O(N). 2) use iterative 2-pointer.

~~~java
public boolean isMatch_2d_method(String s, String p) {
	int m=s.length(), n=p.length();
	boolean[][] dp = new boolean[m+1][n+1];
	dp[0][0] = true;
	for (int i=1; i<=m; i++) {
		dp[i][0] = false;
	}
	
	for(int j=1; j<=n; j++) {
		if(p.charAt(j-1)=='*'){
			dp[0][j] = true;
		} else {
			break;
		}
	}
	
	for(int i=1; i<=m; i++) {
		for(int j=1; j<=n; j++) {
			if (p.charAt(j-1)!='*') {
				dp[i][j] = dp[i-1][j-1] && (s.charAt(i-1)==p.charAt(j-1) || p.charAt(j-1)=='?');
			} else {
				dp[i][j] = dp[i-1][j] || dp[i][j-1];
			}
		}
	}
	return dp[m][n];
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
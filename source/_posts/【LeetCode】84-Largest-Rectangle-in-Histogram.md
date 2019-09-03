---
title: 【LeetCode】84. Largest Rectangle in Histogram
categories:
  - 算法与数据结构
  - LeetCode
description: 84. 柱状图中最大的矩形。主题：栈、数组。难度：困难。
abbrlink: dea7562e
date: 2019-09-02 10:16:13
tags:
keywords:
---

## 1.题目描述

Given *n* non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.

给定 *n* 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。

求在该柱状图中，能够勾勒出来的矩形的最大面积。

<img src="https://assets.leetcode.com/uploads/2018/10/12/histogram.png" align="left"/>

Above is a histogram where width of each bar is 1, given height = `[2,1,5,6,2,3]`.

<img src="https://assets.leetcode.com/uploads/2018/10/12/histogram_area.png" align="left"/>

The largest rectangle is shown in the shaded area, which has area = `10` unit.

**Example:**

> Input: [2,1,5,6,2,3]
> Output: 10

## 2.Solutions

For any bar `i` the maximum rectangle is of width `r - l - 1` where r - is the last coordinate of the bar to the **right** with height `h[r] >= h[i]` and l - is the last coordinate of the bar to the **left** which height `h[l] >= h[i]`

So if for any `i` coordinate we know his utmost higher (or of the same height) neighbors to the right and to the left, we can easily find the largest rectangle:

```java
int maxArea = 0;
for (int i = 0; i < height.length; i++) {
    maxArea = Math.max(maxArea, height[i] * (lessFromRight[i] - lessFromLeft[i] - 1));
}
```

The main trick is how to effectively calculate `lessFromRight` and `lessFromLeft` arrays. The trivial solution is to use **O(n^2)** solution and for each `i` element first find his left/right heighbour in the second inner loop just iterating back or forward:

```java
for (int i = 1; i < height.length; i++) {              
    int p = i - 1;
    while (p >= 0 && height[p] >= height[i]) {
        p--;
    }
    lessFromLeft[i] = p;              
}
```

The only line change shifts this algorithm from **O(n^2)** to **O(n)** complexity: we don't need to rescan each item to the left - we can reuse results of previous calculations and "jump" through indices in quick manner:

```java
while (p >= 0 && height[p] >= height[i]) {
      p = lessFromLeft[p];
}
```

Here is the whole solution:

```java
public static int largestRectangleArea(int[] height) {
    if (height == null || height.length == 0) {
        return 0;
    }
    int[] lessFromLeft = new int[height.length]; // idx of the first bar the left that is lower than current
    int[] lessFromRight = new int[height.length]; // idx of the first bar the right that is lower than current
    lessFromRight[height.length - 1] = height.length;
    lessFromLeft[0] = -1;

    for (int i = 1; i < height.length; i++) {
        int p = i - 1;

        while (p >= 0 && height[p] >= height[i]) {
            p = lessFromLeft[p];
        }
        lessFromLeft[i] = p;
    }

    for (int i = height.length - 2; i >= 0; i--) {
        int p = i + 1;

        while (p < height.length && height[p] >= height[i]) {
            p = lessFromRight[p];
        }
        lessFromRight[i] = p;
    }

    int maxArea = 0;
    for (int i = 0; i < height.length; i++) {
        maxArea = Math.max(maxArea, height[i] * (lessFromRight[i] - lessFromLeft[i] - 1));
    }

    return maxArea;
}
```

使用栈：

For explanation, please see http://www.geeksforgeeks.org/largest-rectangle-under-histogram/

```java
public class Solution {
    public int largestRectangleArea(int[] height) {
        int len = height.length;
        Stack<Integer> s = new Stack<Integer>();
        int maxArea = 0;
        for(int i = 0; i <= len; i++){
            int h = (i == len ? 0 : height[i]);
            if(s.isEmpty() || h >= height[s.peek()]){
                s.push(i);
            }else{
                int tp = s.pop();
                maxArea = Math.max(maxArea, height[tp] * (s.isEmpty() ? i : i - 1 - s.peek()));
                i--;
            }
        }
        return maxArea;
    }
}
```

OP's Note: Two years later I need to interview again. I came to this problem and I couldn't understand this solution. After reading the explanation through the link above, I finally figured this out again.
Two key points that I found helpful while understanding the solution:

1. Do push all heights including 0 height.
2. `i - 1 - s.peek()` uses the starting index where `height[s.peek() + 1] >= height[tp]`, because the index on top of the stack right now is the first index left of `tp` with height smaller than tp's height.

<center><font style="font-weight:bold">（完）</font></center>


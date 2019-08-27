---
title: 【LeetCode】42. Trapping Rain Water
categories:
  - 算法与数据结构
  - LeetCode
description: 42. 接雨水。主题：栈、数组、双指针。难度：困难。
abbrlink: c1b9387f
date: 2019-08-20 16:33:13
tags:
keywords:
---

## 1.题目描述

Given *n* non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

给定 *n* 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

<img src="http://ww1.sinaimg.cn/large/75a4a8eegy1g668bj08i3j20bg04hmx3.jpg" align="left"/>

The above elevation map is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped. **Thanks Marcos** for contributing this image!

上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 感谢 Marcos 贡献此图。

**Example:**

> Input: [0,1,0,2,1,0,1,3,2,1,2,1]
> Output: 6

## 2.Solutions

Indeed this question can be solved in one pass and O(1) space, but it's probably hard to come up with in a short interview. If you have read the stack O(n) solution for Largest Rectangle in Histogram, you will find this solution is very very similar.

The main idea is : if we want to find out how much water on a bar(bot), we need to find out the left larger bar's index (il), and right larger bar's index(ir). So that the water is (min(height[il],height[ir])-height[bot])*(ir-il-1). use min since only the lower boundary can hold water, and we also need to handle the edge case that there is no il.

To implement this we use a stack that store the indices(指数/索引) with decreasing bar height. Once we find a bar who's height is larger, then let the top of the stack be bot, the cur bar is ir, and the previous bar is il.

~~~java
public int trap(int[] height) {
    if (height == null || height.length < 2) 
        return 0;

    Stack<Integer> stack = new Stack<>();
    int water = 0, i = 0;
    while (i < height.length) {
        if (stack.isEmpty() || height[i] <= height[stack.peek()]) {
            stack.push(i++);
        } else {
            int pre = stack.pop();
            if (!stack.isEmpty()) {
                // find the smaller height between the two sides
                int minHeight = Math.min(height[stack.peek()], height[i]);
                // calculate the area
                water += (minHeight - height[pre]) * (i - stack.peek() - 1);
            }
        }
    }
    return water;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>

---
title: 【LeetCode】57. Insert Interval
categories:
  - 算法与数据结构
  - LeetCode
description: 57. 插入区间。主题：排序、数组。难度：中等。
abbrlink: 2b35f4b7
date: 2019-08-28 15:48:57
tags:
keywords:
---

## 1.题目描述

Given a set of *non-overlapping* intervals, insert a new interval into the intervals (merge if necessary).

You may assume that the intervals were initially sorted according to their start times.

给出一个*无重叠的 ，*按照区间起始端点排序的区间列表。

在列表中插入一个新的区间，你需要确保列表中的区间仍然有序且不重叠（如果有必要的话，可以合并区间）。

**Example 1:**

> Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
> Output: [[1,5],[6,9]]

**Example 2:**

> Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
> Output: [[1,2],[3,10],[12,16]]
> Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].

## 2.Solutions

Here's a pretty straight-forward and concise solution below.

~~~java
public static int[][] insert(int[][] intervals, int[] newInterval) {
    List<int[]> result = new LinkedList<>();
    int i = 0;
    // add all the intervals ending before newInterval starts
    while (i < intervals.length && intervals[i][1] < newInterval[0]){
        result.add(intervals[i]);
        i++;
    }

    // merge all overlapping intervals to one considering newInterval
    while (i < intervals.length && intervals[i][0] <= newInterval[1]) {
        // we could mutate newInterval here also
        newInterval[0] = Math.min(newInterval[0], intervals[i][0]);
        newInterval[1] = Math.max(newInterval[1], intervals[i][1]);
        i++;
    }

    // add the union of intervals we got
    result.add(newInterval); 

    // add all the rest
    while (i < intervals.length){
        result.add(intervals[i]); 
        i++;
    }

    return result.toArray(new int[result.size()][]);
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
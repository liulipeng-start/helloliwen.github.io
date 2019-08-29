---
title: 【LeetCode】56. Merge Intervals
categories:
  - 算法与数据结构
  - LeetCode
description: 56. 合并区间。主题：排序、数组。难度：中等。
abbrlink: 7386f83e
date: 2019-08-28 09:39:54
tags:
keywords:
---

## 1.题目描述

Given a collection of intervals, merge all overlapping intervals.

给出一个区间的集合，请合并所有重叠的区间。

**Example 1:**

> Input: [[1,3],[2,6],[8,10],[15,18]]
> Output: [[1,6],[8,10],[15,18]]
> Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].

**Example 2:**

> Input: [[1,4],[4,5]]
> Output: [[1,5]]
> Explanation: Intervals [1,4] and [4,5] are considered overlapping.

## 2.Solutions

The idea is to sort the intervals by their starting points. Then, we take the first interval and compare its end with the next intervals starts. As long as they overlap, we update the end to be the max end of the overlapping intervals. Once we find a non overlapping interval, we can add the previous "extended" interval and start over.

Sorting takes O(nlog(n)) and merging the intervals takes O(n). So, the resulting algorithm takes O(n log(n)).

I used a lambda comparator (Java 8) and a for-each loop to try to keep the code clean and simple.

我们的想法是按照起点对间隔进行排序。 然后我们采用第一个间隔并将其结束与下一个间隔开始进行比较。 只要它们重叠，我们将结束更新为重叠间隔的最大结束。 一旦找到非重叠间隔，我们就可以添加之前的“扩展”间隔并重新开始。

排序需要O(nlog(n))并且合并间隔需要O(n)。 因此，得到的算法需要时间为：O(nlog(n))。

我使用lambda比较器（Java 8）和for-each循环来尝试保持代码整洁和简单。

~~~java
public static int[][] merge(int[][] intervals) {
    if (intervals.length <= 1)
        return intervals;

    // Sort by ascending starting point
    Arrays.sort(intervals, (i1, i2) -> Integer.compare(i1[0], i2[0]));

    List<int[]> result = new ArrayList<>();
    int[] newInterval = intervals[0];
    result.add(newInterval);
    for (int[] interval : intervals) {
        // Overlapping intervals, move the end if needed
        if (interval[0] <= newInterval[1]) 
            newInterval[1] = Math.max(newInterval[1], interval[1]);
        else {// Disjoint intervals, add the new interval to the list                             
            newInterval = interval;
            result.add(newInterval);
        }
    }

    return result.toArray(new int[result.size()][]);
}
~~~

相似问题：

[252 Meeting Rooms](https://leetcode.com/problems/meeting-rooms/)

[253 Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)

[435 Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)

<center><font style="font-weight:bold">（完）</font></center>


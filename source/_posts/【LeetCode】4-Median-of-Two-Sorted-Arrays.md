---
title: 【LeetCode】4. Median of Two Sorted Arrays
categories:
  - 算法与数据结构
  - LeetCode
description: 4. 寻找两个有序数组的中位数。主题：数组、二分查找、分治。难度：困难
abbrlink: 2a84b609
date: 2019-07-31 10:27:34
tags:
keywords:
---

## 1.题目描述

There are two sorted arrays **nums1** and **nums2** of size m and n respectively.

Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

You may assume **nums1** and **nums2** cannot be both empty.

给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。

请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

你可以假设 nums1 和 nums2 不会同时为空。

**Example 1:**

```
nums1 = [1, 3]
nums2 = [2]

The median is 2.0
```

**Example 2:**

```
nums1 = [1, 2]
nums2 = [3, 4]

The median is (2 + 3)/2 = 2.5
```

## 2.Solutions

原文链接：[Share my O(log(min(m,n)) solution with explanation](https://link.zhihu.com/?target=https%3A//leetcode.com/problems/median-of-two-sorted-arrays/discuss/2481/Share-my-O(log(min(mn))

[LeetCode#4. 寻找两个有序数组的中位数](https://zhuanlan.zhihu.com/p/70654378)

~~~java
public double findMedianSortedArrays(int[] A, int[] B) {
    int m = A.length;
    int n = B.length;

    if (m > n) {
        return findMedianSortedArrays(B, A);
    }

    int i = 0, j = 0, imin = 0, imax = m, half = (m + n + 1) / 2;
    double maxLeft = 0, minRight = 0;
    while (imin <= imax) {
        i = (imin + imax) / 2;
        j = half - i;
        if (j > 0 && i < m && B[j - 1] > A[i]) {
            imin = i + 1; //i的值太小， 增加它
        } else if (i > 0 && j < n && A[i - 1] > B[j]) {
            imax = i - 1; //i的值过大， 减小它
        } else {
            //i is perfect
            if (i == 0) {
                maxLeft = (double) B[j - 1];
            } else if (j == 0) {
                maxLeft = (double) A[i - 1];
            } else {
                maxLeft = (double) Math.max(A[i - 1], B[j - 1]);
            }
            break;
        }
    }
    if ((m + n) % 2 == 1) {
        return maxLeft;
    }
    if (i == m) {
        minRight = (double) B[j];
    } else if (j == n) {
        minRight = (double) A[i];
    } else {
        minRight = (double) Math.min(A[i], B[j]);
    }

    return (double) (maxLeft + minRight) / 2;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>


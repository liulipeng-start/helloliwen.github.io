---
title: 【LeetCode】88. Merge Sorted Array
categories:
  - 算法与数据结构
  - LeetCode
description: 88. 合并两个有序数组。主题：数组、双指针。难度：容易。
abbrlink: 7897b28f
date: 2019-09-02 17:15:20
tags:
keywords:
---

## 1.题目描述

Given two sorted integer arrays *nums1* and *nums2*, merge *nums2* into *nums1* as one sorted array.

**Note:**

- The number of elements initialized in *nums1* and *nums2* are *m* and *n* respectively.
- You may assume that *nums1* has enough space (size that is greater or equal to *m* + *n*) to hold additional elements from *nums2*.

给定两个有序整数数组 nums1 和 nums2，将 nums2 合并到 nums1 中，使得 num1 成为一个有序数组。

说明:

- 初始化 nums1 和 nums2 的元素数量分别为 m 和 n。
- 你可以假设 nums1 有足够的空间（空间大小大于或等于 m + n）来保存 nums2 中的元素。

**Example:**

> Input:
> nums1 = [1,2,3,0,0,0], m = 3
> nums2 = [2,5,6],       n = 3
>
> Output: [1,2,2,3,5,6]

## 2.Solutions

~~~java
public static void merge(int[] A, int m, int[] B, int n) {
    int a=m-1;
    int b=n-1;
    int k = m+n-1;
    while(a >=0 && b>=0){
        if(A[a] > B[b])
            A[k--] = A[a--];
        else
            A[k--] = B[b--];
    }
    while(b>=0)
        A[k--] = B[b--];
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
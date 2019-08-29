---
title: 【LeetCode】75. Sort Colors
categories:
  - 算法与数据结构
  - LeetCode
description: 75. 颜色分类。主题：排序、数组、双指针。难度：中等。
abbrlink: 61bc53ad
date: 2019-08-29 21:21:35
tags:
keywords:
---

## 1.题目描述

Given an array with *n* objects colored red, white or blue, sort them **in-place** so that objects of the same color are adjacent, with the colors in the order red, white and blue.

Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.

**Note:** You are not suppose to use the library's sort function for this problem.

给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

此题中，我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

注意:不能使用代码库中的排序函数来解决这道题。

**Example:**

> Input: [2,0,2,1,1,0]
> Output: [0,0,1,1,2,2]

**Follow up:**

- A rather straight forward solution is a two-pass algorithm using counting sort.
  First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.
- Could you come up with a one-pass algorithm using only constant space?

进阶：

- 一个直观的解决方案是使用计数排序的两趟扫描算法。
  首先，迭代计算出0、1 和 2 元素的个数，然后按照0、1、2的排序，重写当前数组。
- 你能想出一个仅使用常数空间的一趟扫描算法吗？

## 2.Solutions

The idea is to sweep all 0s to the left and all 2s to the right, then all 1s are left in the middle.

It is hard to define what is a "one-pass" solution but this algorithm is bounded by O(2n), meaning that at most each element will be seen and operated twice (in the case of all 0s). You may be able to write an algorithm which goes through the list only once, but each step requires multiple operations, leading the total operations larger than O(2n).

the basic idea is to use two pointer low and high and an iterator i, every elem left low pointer is 0, elem right high pointer is 2

~~~java
public static void sortColors(int[] nums) {
    if (nums == null || nums.length < 2)
        return;

    int low = 0;
    int high = nums.length - 1;
    for (int i = low; i <= high;) {
        if (nums[i] == 0) {
            // swap nums[i] and nums[low] and i,low both ++
            swap(nums,i,low);
            i++;
            low++;
        } else if (nums[i] == 2) {
            // swap nums[i] and nums[high] and high--;
            swap(nums,i,high);
            high--;
        } else {
            i++;
        }
    }
}
public static void swap(int[] nums,int i,int j){
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
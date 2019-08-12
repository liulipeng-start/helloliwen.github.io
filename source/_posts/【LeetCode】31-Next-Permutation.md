---
title: 【LeetCode】31. Next Permutation
categories:
  - 算法与数据结构
  - LeetCode
description: 31.下一个排列顺序（全排列）。主题：数组
abbrlink: 4b8a6c10
date: 2019-07-14 21:12:42
tags:
keywords:
---

## 1.题目描述

Implement **next permutation**, which rearranges numbers into the lexicographically(字典序) next greater permutation(排列) of numbers.

If such arrangement is not possible, it must rearrange it as the lowest possible order (ie, sorted in ascending order).

The replacement must be **in-place** and use only constant(不变的) extra memory.

Here are some examples. Inputs are in the left-hand column and its corresponding outputs are in the right-hand column.

> `1,2,3` → `1,3,2`
> `3,2,1` → `1,2,3`
> `1,1,5` → `1,5,1`

实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。

如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

必须原地修改，只允许使用额外常数空间。

以下是一些例子，输入位于左侧列，其相应输出位于右侧列。

> `1,2,3` → `1,3,2`
> `3,2,1` → `1,2,3`
> `1,1,5` → `1,5,1`

## 2.相关知识

**全排列的生成算法**方法是将给定的序列中所有可能的全排列无重复无遗漏地枚举出来。此处全排列的定义是：从n个元素中取出m个元素进行排列，当n=m时这个排列被称为全排列。字典序、邻位对换法、循环左移法、循环右移法、递增进位制法、递减进位制法都是常见的全排列生成算法。

例子：对于元素集合{1，2，3}按字典序生成的全排列是

> [1, 2, 3]
> [1, 3, 2]
> [2, 1, 3]
> [2, 3, 1]
> [3, 2, 1]
> [3, 1, 2]

## 3.Solutions

According to [Wikipedia](https://en.wikipedia.org/wiki/Permutation#Generation_in_lexicographic_order), a man named Narayana Pandita presented the following simple algorithm to solve this problem in the 14th century.

1. Find the largest index `k` such that `nums[k] < nums[k + 1]`. If no such index exists, just reverse `nums` and done.
2. Find the largest index `l > k` such that `nums[k] < nums[l]`.
3. Swap `nums[k]` and `nums[l]`.
4. Reverse the sub-array `nums[k + 1:]`.

~~~java
public static void nextPermutation(int[] nums) {
    int len = nums.length, k, l;
    for (k = len - 2; k >= 0; k--) {
        if (nums[k] < nums[k + 1]) {
            break;
        }
    }
    if (k < 0) {
        reverse(nums,0,len-1);
    } else {
        for (l = len - 1; l > k; l--) {
            if (nums[l] > nums[k]) {
                break;
            }
        } 
        swap(nums, k, l);
        reverse(nums, k + 1, len-1);
    }
}
private static void reverse(int[] nums,int start,int end){
    int len = nums.length;
    if(len < 0 || start > len || end > len || start>end){
        return;
    }
    // two pointer
    for( ;start < end; start++,end--){
        int temp = nums[start];
        nums[start] = nums[end];
        nums[end] = temp;
    }
    /*for (int i = 0; i < len >> 1; i++) {
			int temp = nums[i];
			nums[i] = nums[len-i-1];
			nums[len-i-1] = temp;
		}*/
}
private static void swap(int[] nums,int i,int j){
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>

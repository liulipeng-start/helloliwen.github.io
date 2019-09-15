---
title: 【LeetCode】27. Remove Element
categories:
  - 算法与数据结构
  - LeetCode
description: 27. 移除元素。主题：数组、双指针。难度：简单。
abbrlink: f08ce37d
date: 2019-07-13 16:45:03
tags:
keywords:
---

## 1.题目描述

Given an array *nums* and a value *val*, remove all instances of that value [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) and return the new length.

Do not allocate extra space for another array, you must do this by **modifying the input array in-place** with O(1) extra memory.

The order of elements can be changed. It doesn't matter what you leave beyond the new length.

给定一个数组 nums 和一个值 val，你需要原地移除所有数值等于 val 的元素，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

**Example 1:**

> Given nums = [3,2,2,3], val = 3,
>
> Your function should return length = 2, with the first two elements of nums being 2.
>
> It doesn't matter what you leave beyond the returned length.

**Example 2:**

> Given nums = [0,1,2,2,3,0,4,2], val = 2,
>
> Your function should return length = 5, with the first five elements of nums containing 0, 1, 3, 0, and 4.
>
> Note that the order of those five elements can be arbitrary(随意).
>
> It doesn't matter what values are set beyond the returned length.

**Clarification:**

Confused why the returned value is an integer but your answer is an array?

Note that the input array is passed in by **reference**, which means modification to the input array will be known to the caller as well.

Internally you can think of this:

为什么返回数值是整数，但输出的答案是数组呢?

请注意，输入数组是以“引用”方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:

> // nums is passed in by reference. (i.e., without making a copy)
> int len = removeElement(nums, val);
>
> // any modification to nums in your function would be known by the caller.
> // using the length returned by your function, it prints the first len elements.
> for (int i = 0; i < len; i++) {
>     print(nums[i]);
> }

## 2.Solutions

　　从数组左边开始，每当找到一个目标值时，与数组右边的值交换。同时，把右指针递减；并且当当前值不是目标值时，递增左指针。一旦左指针达到右指针，我们就知道左指针后面的值都是目标值，而左指针前面的值是非目标值，返回左指针就是移除后的数组长度。

~~~java
public static int removeElement(int[] nums, int val) {
    int left = 0,right = nums.length - 1;
    while(left <= right){
        if(nums[left] == val){
            int temp = nums[left];
            nums[left] = nums[right];
            nums[right] = temp;
            right--;
        }else{
            left++;
        }
    }
    return left;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>

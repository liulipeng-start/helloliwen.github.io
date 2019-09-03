---
title: 【LeetCode】80. Remove Duplicates from Sorted Array II
categories:
  - 算法与数据结构
  - LeetCode
description: 80. 删除排序数组中的重复项 II。主题：数组、双指针。难度：中等。
abbrlink: d746852
date: 2019-08-30 23:50:38
tags:
keywords:
---

## 1.题目描述

Given a sorted array *nums*, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that duplicates appeared at most *twice* and return the new length.

Do not allocate extra space for another array, you must do this by **modifying the input array in-place** with O(1) extra memory.

给定一个排序数组，你需要在原地删除重复出现的元素，使得每个元素最多出现两次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

**Example 1:**

```
Given nums = [1,1,1,2,2,3],

Your function should return length = 5, with the first five elements of nums being 1, 1, 2, 2 and 3 respectively.

It doesn't matter what you leave beyond the returned length.
```

**Example 2:**

```
Given nums = [0,0,1,1,1,1,2,3,3],

Your function should return length = 7, with the first seven elements of nums being modified to 0, 0, 1, 1, 2, 3 and 3 respectively.

It doesn't matter what values are set beyond the returned length.
```

**Clarification:**

Confused why the returned value is an integer but your answer is an array?

Note that the input array is passed in by **reference**, which means modification to the input array will be known to the caller as well.

Internally you can think of this:

**说明:**

为什么返回数值是整数，但输出的答案是数组呢?

请注意，输入数组是以**“引用”**方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:

```
// nums 是以“引用”方式传递的。也就是说，不对实参做任何拷贝
int len = removeDuplicates(nums);

// 在函数里修改输入数组对于调用者是可见的。
// 根据你的函数返回的长度, 它会打印出数组中该长度范围内的所有元素。
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

## 2.Solutions

Question wants us to return the length of new array after removing duplicates and that we don't care about what we leave beyond new length , hence we can use i to keep track of the position and update the array.

问题要求我们在删除重复项后返回新数组的长度，并且我们不关心超出新长度的内容，因此我们可以使用i来跟踪位置并更新数组。

~~~java
public static int removeDuplicates(int[] nums) {
    int i = 0;
    for (int n : nums)
        if (i < 2 || n > nums[i - 2])
            nums[i++] = n;
    return i;
}
~~~

参考：[26. Remove Duplicates from Sorted Array](https://helloliwen.github.io/727ca1de.html)

<center><font style="font-weight:bold">（完）</font></center>
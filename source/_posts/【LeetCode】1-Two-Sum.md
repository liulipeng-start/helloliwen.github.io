---
title: 【LeetCode】1.Two Sum
categories:
  - 算法与数据结构
  - LeetCode
tags: 
description: 1.两数之和。主题：数组、Hash Table。难度：容易。
abbrlink: e5726db5
date: 2019-07-02 10:58:32
keywords:
---

　　开启 LeetCode 刷题的旅程，这将是一段漫长而又艰辛的旅程，这是一条攀登珠穆朗玛的皑皑雪山路，这是通向 One Piece 宝藏的伟大航路，这是成为火影的无比残酷的修罗场，这是打破吊丝与高富帅之间界限的崩玉。但请不要害怕，在老船长 Grandyang 博主的带领下，必将一路披荆斩棘，将各位带到成功的彼岸。不过一定要牢记的是，不要下船，不要中途放弃，要坚持，要自我修炼，不断成长！

　　这道 Two Sum 的题目作为 LeetCode 的开篇之题，乃是经典中的经典，正所谓“**平生不识 TwoSum，刷尽 LeetCode 也枉然**”，就像英语单词书的第一个单词总是 Abandon 一样，很多没有毅力坚持的人就只能记住这一个单词，所以通常情况下单词书就前几页有翻动的痕迹，后面都是崭新如初，道理不需多讲，鸡汤不必多灌，明白的人自然明白。

## 1.题目描述

Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.

You may assume that each input would have **exactly** one solution, and you may not use the *same* element twice.

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

**Example:**

> Given nums = [2, 7, 11, 15], target = 9,
>
> Because nums[0] + nums[1] = 2 + 7 = 9,
> return [0, 1].

## 2.Solutions

### 暴力求解

~~~java
//选择排序思想
public static int[] twoSum(int[] nums, int target) {
    int[] results = new int[2];
    for (int i = 0; i < nums.length; i++) {
        for (int j = i+1; j < nums.length; j++) {
            if(nums[i]+nums[j] == target){
                results[0] = i;
                results[1] = j;
                return results;
            }
        }
    }
    return results;
}
~~~

时间复杂度：O(n<sup>2</sup>)

### 算法改进

　　一般来说，我们为了提高时间的复杂度，需要用空间来换，这算是一个 trade off 吧。我们只想用线性的时间复杂度来解决问题，那么就是说只能遍历一个数字，那么另一个数字呢？我们可以事先将其存储起来，使用一个 HashMap，来建立数字和其坐标位置之间的映射。我们都知道 HashMap 是常数级的查找效率，这样，我们在遍历数组的时候，用 target 减去遍历到的数字，就是另一个需要的数字了，直接在 HashMap 中查找其是否存在即可。

~~~java
public static int[] twoSum2(int[] nums, int target) {
    int[] results = new int[2];
    Map<Integer, Integer> map = new HashMap<Integer, Integer>();
    for (int i = 0; i < nums.length; i++) {
        if(map.containsKey(target-nums[i])){
            results[0] = map.get(target-nums[i]);
            results[1] = i;
            return results;
        }
        map.put(nums[i], i);
    }
    return results;
}
~~~

Scala版：

~~~scala
def twoSum(nums: Array[Int], target: Int): Array[Int] = {
    nums.zipWithIndex.foldLeft(Map.empty[Int,Int])((m,x)=>{
        if(m.get(target - x._1)==None)
        m+(x._1 -> x._2)
        else
        return Array(m.getOrElse(target-x._1, -1), x._2)
    })
    null
}
~~~

参考：

[LeetCode All in One 题目讲解汇总(持续更新中...)](https://www.cnblogs.com/grandyang/p/4606334.html)

<center><font style="font-weight:bold">（完）</font></center>
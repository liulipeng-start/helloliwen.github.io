---
title: 【LeetCode】11. Container With Most Water
categories:
  - 算法与数据结构
  - LeetCode
description: 11.给定坐标，求坐标中围成的最大区域（装最多的水）。主题：数组、双指针
abbrlink: 2a1c4f05
date: 2019-07-09 19:08:18
tags:
keywords:
---

## 1.题目描述

　　Given *n* non-negative integers *a<sub>1</sub>*, *a<sub>2</sub>*, ..., *a<sub>n</sub>* , where each represents a point at coordinate (*i*, *a<sub>i</sub>*). *n* vertical lines are drawn such that the two endpoints of line *i* is at (*i*, *a<sub>i</sub>*) and (*i*, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.

**Note:** You may not slant（倾斜） the container and *n* is at least 2.

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g4tsxpwzq2j20m90anwep.jpg)

　　The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.

**Example:**

> Input: [1,8,6,2,5,4,8,3,7]
> Output: 49

## 2.理解

　　The O(n) solution with proof by contradiction doesn't look intuitive enough to me. Before moving on, read any [example](https://leetcode.com/problems/container-with-most-water/discuss/6100/Simple-and-clear-proofexplanation) of the algorithm first if you don't know it yet.

　　Here's another way to see what happens in a matrix（矩阵） representation:

　　Draw a matrix where the row is the first line, and the column is the second line. For example, say `n=6`.

　　In the figures below, `x` means we don't need to compute the volume for that case: (1) On the diagonal（对角线）, the two lines are overlapped（重叠）; (2) The lower left triangle area of the matrix is symmetric（对称） to the upper right area.

　　We start by computing the volume at `(1,6)`, denoted（记） by `o`. Now if the left line is shorter than the right line, then all the elements left to `(1,6)` on the first row have smaller volume, so we don't need to compute those cases (crossed by `---`).

```
  1 2 3 4 5 6
1 x ------- o
2 x x
3 x x x 
4 x x x x
5 x x x x x
6 x x x x x x
```

　　Next we move the left line and compute `(2,6)`. Now if the right line is shorter, all cases below `(2,6)` are eliminated（消除、淘汰）.

```
  1 2 3 4 5 6
1 x ------- o
2 x x       o
3 x x x     |
4 x x x x   |
5 x x x x x |
6 x x x x x x
```

　　And no matter how this `o` path goes, we end up only need to find the max value on this path, which contains `n-1` cases.

```
  1 2 3 4 5 6
1 x ------- o
2 x x - o o o
3 x x x o | |
4 x x x x | |
5 x x x x x |
6 x x x x x x
```

　　Hope this helps. I feel more comfortable seeing things this way.

## 3.Solutions

　　AKA, the general idea to find some max is to go through all cases where max value can possibly occur and keep updating the max value. The efficiency of the scan depends on the size of cases you plan to scan.

　　To increase efficiency, all we need to do is to find a smart way of scan to cut off the useless cases and meanwhile 100% guarantee the max value can be reached through the rest of cases.

　　In this problem, the smart scan way is to set <font style="color:red">two pointers（双指针）</font> initialized at both ends of the array. Every time move the smaller value pointer to inner array. Then after the two pointers meet, all possible max cases have been scanned and the max situation is 100% reached somewhere in the scan. Following is a brief prove of this.

　　Given a<sub>1</sub>,a<sub>2</sub>,a<sub>3</sub>.....a<sub>n</sub> as input array. Lets assume a<sub>10</sub> and a<sub>20</sub> are the max area situation. We need to prove that a<sub>10</sub> can be reached by left pointer and during the time left pointer stays at a<sub>10</sub>, a<sub>20</sub> can be reached by right pointer. That is to say, the core problem is to prove: **when left pointer is at a<sub>10</sub> and right pointer is at a<sub>21</sub>, the next move must be right pointer to a<sub>20</sub>**.

　　Since we are always moving the pointer with the smaller value, i.e. if a<sub>10</sub> > a<sub>21</sub>, we should move pointer at a<sub>21</sub> to a<sub>20</sub>, as we hope. Why a<sub>10</sub> >a<sub>21</sub>? Because if a<sub>21</sub>>a<sub>10</sub>, then area of a<sub>10</sub> and a<sub>20</sub> must be less than area of a<sub>10</sub> and a<sub>21</sub>. Because the area of a<sub>10</sub> and a<sub>21</sub> is at least height[a<sub>10</sub>] * (21-10) while the area of a<sub>10</sub> and a<sub>20</sub> is at most height[a<sub>10</sub>] * (20-10). So there is a contradiction（矛盾） of assumption a<sub>10</sub> and a<sub>20</sub> has the max area. So, a<sub>10</sub> must be greater than a<sub>21</sub>, then next move a<sub>21</sub> has to be move to a<sub>20</sub>. The max cases must be reached.

~~~java 
public static int maxArea(int[] height) {
	    int left = 0, right = height.length - 1;
		int maxArea = 0;

		while (left < right) {
			maxArea = Math.max(maxArea, Math.min(height[left],height[right])
                               * (right-left));
			if (height[left] < height[right])
				left++;
			else
				right--;
		}
		return maxArea;
	}
~~~

<center><font style="font-weight:bold">（完）</font></center>


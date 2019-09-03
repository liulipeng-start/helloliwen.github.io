---
title: 【LeetCode】85. Maximal Rectangle
categories:
  - 算法与数据结构
  - LeetCode
description: 85. 最大矩形。主题：栈、数组、Hash Table、动态规划。难度：困难。
abbrlink: 72e624d5
date: 2019-09-02 10:22:43
tags:
keywords:
---

## 1.题目描述

Given a 2D binary matrix filled with 0's and 1's, find the largest rectangle containing only 1's and return its area.

给定一个仅包含 0 和 1 的二维二进制矩阵，找出只包含 1 的最大矩形，并返回其面积。

**Example:**

> Input:
> [
>   ["1","0","1","0","0"],
>   ["1","0","1","1","1"],
>   ["1","1","1","1","1"],
>   ["1","0","0","1","0"]
> ]
> Output: 6

## 2.Solutions

This question is similar as [[Largest Rectangle in Histogram\]](http://oj.leetcode.com/problems/largest-rectangle-in-histogram/):

You can maintain a row length of Integer array H recorded its height of '1's, and scan and update row by row to find out the largest rectangle of each row.

For each row, if matrix[row][i] == '1'. H[i] +=1, or reset the H[i] to zero.
and accroding the algorithm of [Largest Rectangle in Histogram], to update the maximum area.

~~~java
public int maximalRectangle(char[][] matrix) {
    if (matrix==null||matrix.length==0||matrix[0].length==0)
        return 0;
    int cLen = matrix[0].length;    // column length
    int rLen = matrix.length;       // row length
    // height array 
    int[] h = new int[cLen+1];
    h[cLen]=0;
    int max = 0;

    for (int row=0;row<rLen;row++) {
        Stack<Integer> s = new Stack<Integer>();
        for (int i=0;i<cLen+1;i++) {
            if (i<cLen)
                if(matrix[row][i]=='1')
                    h[i]+=1;
            else h[i]=0;

            if (s.isEmpty()||h[s.peek()]<=h[i])
                s.push(i);
            else {
                while(!s.isEmpty()&&h[i]<h[s.peek()]){
                    int top = s.pop();
                    int area = h[top]*(s.isEmpty()?i:(i-s.peek()-1));
                    if (area>max)
                        max = area;
                }
                s.push(i);
            }
        }
    }
    return max;
}
~~~



这道题太难了，先搞清楚这些matrix分别代表什么。
`height` means from top to this position, there are how many '1'
`left` means at current position, what is the **index** of left bound of the rectangle **with** `height[j]`. 0 means at this position, no rectangle. (现在这个矩形的左边的下标)
`right` means the right bound index of this rectangle. 'n' means no rectangle.

> matrix
> 0 0 0 1 0 0 0
> 0 0 1 1 1 0 0
> 0 1 1 1 1 1 0
>
> height
> 0 0 0 1 0 0 0
> 0 0 1 2 1 0 0
> 0 1 2 3 2 1 0
>
> left
> 0 0 0 3 0 0 0
> 0 0 2 3 2 0 0
> 0 1 2 3 2 1 0
>
> right
> 7 7 7 4 7 7 7
> 7 7 5 4 5 7 7
> 7 6 5 4 5 4 7
>
> result
> 0 0 0 1 0 0 0
> 0 0 3 2 3 0 0
> 0 5 6 3 6 5 0

dp的太难了，来看下Histogram的解法吧。参考这道题：https://discuss.leetcode.com/topic/7599/o-n-stack-based-java-solution/23

比如这个matrix有n行，就把这个问题转换成n个Histogram的问题。
每个问题就是一个以这一行为底的Histogram问题，上面连续的1的个数就是Height。

~~~java
public int maximalRectangle(char[][] matrix) {
    if(matrix.length == 0) return 0;  
    int n = matrix[0].length;
    int[] heights = new int[n]; // using a array to reduce counting step of 1
    int max = 0;
    for(char[] row: matrix){
        for(int i = 0; i < n; i++){
            if(row[i] == '1'){
                heights[i] += 1;
            } else {
                heights[i] = 0;
            }
        }

        max = Math.max(max, maxArea(heights)); // go a sub problem of Histogram
    }
    return max;
}

// Exact same solution to Histogram
public int maxArea(int[] heights){
    Stack<Integer> stack = new Stack();
    int max = 0;
    for(int i = 0; i <= heights.length; i++){
        int h = (i == heights.length) ? 0 : heights[i];
        while(!stack.isEmpty() && heights[stack.peek()] > h){
            int index = stack.pop();
            int leftBound = -1;
            if(!stack.isEmpty()){
                leftBound =  stack.peek();
            }
            max = Math.max(max, heights[index] * (i - leftBound - 1));
        }
        stack.push(i);
    }
    return max;
}
~~~

<center><font style="font-weight:bold">（完）</font></center>
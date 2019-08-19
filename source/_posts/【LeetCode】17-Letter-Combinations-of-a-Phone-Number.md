---
title: 【LeetCode】17. Letter Combinations of a Phone Number
categories:
  - 算法与数据结构
  - LeetCode
description: 17. 电话号码的字母组合。主题：字符串，回溯算法。难度：中等。
abbrlink: 582f38e
date: 2019-08-15 22:39:50
tags:
keywords:
---

## 1.题目描述

Given a string containing digits from `2-9` inclusive, return all possible letter combinations that the number could represent.

A mapping of digit to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![](http://ww1.sinaimg.cn/large/75a4a8eegy1g60qw5cl7dj20dv0ckdhp.jpg)

**Example:**

> Input: "23"
> Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].

**Note:**

Although the above answer is in lexicographical order, your answer could be in any order you want.

尽管上面的答案是按字典序排列的，但是你可以任意选择答案输出的顺序。

## 2.Solutions

使用FIFO队列解决：

~~~java
public static List<String> letterCombinations(String digits) {
    LinkedList<String> queue = new LinkedList<String>();
    if(digits.isEmpty()) 
        return queue;
    String[] mapping = {"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
    queue.offer("");
    for(int i =0; i<digits.length();i++){
        int str = Character.getNumericValue(digits.charAt(i));
        while(queue.peek().length()==i){
            String rows = queue.poll();
            for(char s : mapping[str].toCharArray())
                queue.offer(rows+s);
        }
    }
    return queue;
}
~~~

　　这是一个迭代的解决方案。 对于添加的每个数字，删除并复制队列中的每个元素，并将可能的字母添加到每个元素，然后再将更新的元素添加回队列。 重复此过程，直到迭代所有数字。

　　但是没有回溯（BFS或者DFS）版本。

<center><font style="font-weight:bold">（完）</font></center>


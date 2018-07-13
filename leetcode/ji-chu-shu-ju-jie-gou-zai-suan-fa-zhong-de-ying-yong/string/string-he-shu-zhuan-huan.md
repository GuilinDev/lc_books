# String和数转换

## 67 - Add Binary

### 原题概述

Given two binary strings, return their sum \(also a binary string\).

The input strings are both **non-empty** and contains only characters `1` or `0`.

**Example 1:**

```text
Input: a = "11", b = "1"
Output: "100"
```

**Example 2:**

```text
Input: a = "1010", b = "1011"
Output: "10101"
```

### 题意和分析

将两个string形式的二进制数相加，并且保存在string中，要注意的是如何将string和int之间互相转换，并且每位相加时，会有进位的可能，会影响之后相加的结果。而且两个输入string的长度也可能会不同。新建一个string，它的长度是两条输入string中的较大的那个，并且把较短的那个输入string通过在开头加字符‘0’来补的较大的那个长度。这时候我们逐个从两个string的末尾开始取出字符，然后转为数字，相加，如果大于等于2，则标记进位标志carry，并且给新string加入一个字符‘0’。

### 代码


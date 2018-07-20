# 异或XOR操作

XOR交换两个整数的值时可以不用第三个参数。 

如a=11,b=9.

以下是二进制 

a=a^b=1011^1001=0010; 

b=b^a=1001^0010=1011; 

a=a^b=0010^1011=1001; 

这样一来a=9,b=11了。

## 136 - Single Number

## 137 - Single Number II

## 260 - Single Number III

## 268 - Missing Number

### 原题概述

Given an array containing n distinct numbers taken from `0, 1, 2, ..., n`, find the one that is missing from the array.

**Example 1:**

```text
Input: [3,0,1]
Output: 2
```

**Example 2:**

```text
Input: [9,6,4,2,3,5,7,0,1]
Output: 8
```

**Note**:  
Your algorithm should run in linear runtime complexity. Could you implement it using only constant extra space complexity?

### 题意和分析

给一个未排序的数组，里面有n个数字，范围从0到n\(本来有n+1个数字\)，找到缺失的那个数，要求线型时间复杂度和常数空间。

我们可以用等差数列的求和公司n \* \(n - 1\) / 2求得一个值Sum，然后遍历整个数组，将每个元素值从Sum里面减掉，最后剩下的数字就是缺失的数字。

这里我们还是用位操作异或XOR来做，因为0到n之间少了一个数，因为 a^b^b =a，异或两次等于原来的数，将这个少了一个数的数组和0到n之间完整的数组异或XOR一下，那么相同的数字都变为0了，最后剩下的就是缺失了的那个数字了。比如5==101 ^ 6==110 == 011；数组\[0,1,3,4\]，result初始为4，循环的值分别为4^0^0=4，4^1^1=4，4^2^3=5，5^3^4=2，最后2作为缺失的数字返回。

### 代码

```java
class Solution {
    public int missingNumber(int[] nums) {
        int result = nums.length;
        for (int i = 0; i < nums.length; i++) {
            result = result ^ i ^ nums[i]; // a^b^b = a
        }
        return result;
    }
}
```

面试中有可能给定的数组是排好序的，那么就用二分查找法来做，找出中间元素nums\[mid\]的下标mid，然后用元素值nums\[mid\]和下标值mid之间做对比，如果元素值大于下标值，则说明缺失的数字在左边，此时将right赋为mid，反之则将left赋为mid+1。


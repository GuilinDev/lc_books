# 众数

### 169 -  Majority Element

### 原题概述

Given an array of size n, find the majority element. The majority element is the element that appears **more than** `⌊ n/2 ⌋` times.

You may assume that the array is non-empty and the majority element always exist in the array.

**Example 1:**

```text
Input: [3,2,3]
Output: 3
```

**Example 2:**

```text
Input: [2,2,1,1,1,2,2]
Output: 2
```

### 题意和分析

给一个非空无序的数组，找到其中的众数，众数是多余元素总个数n/2的数，这个众数一定存在。首先看一下这道题有如下解法：

![](../../../.gitbook/assets/image%20%289%29.png)

这里我们选第6个办法 Moore's voting，大浪淘沙法，维持一个count ，初始化一个数，遍历数组，如果遇到这个数自己，就+1，如果不是自己就-1，当count = 0时，换遍历到的那个数再换一个，最后count ！= 0的数肯定是众数。

### 代码

```java
class Solution {
    public int majorityElement(int[] nums) {
        int major = nums[0];
        int count = 1;//初始化,nums[0]出现一次了

        for (int i = 1; i < nums.length; i++) {
            if (count == 0) {
                major = nums[i];
            }
            if (nums[i] != major) {
                count--;
            } else {
                count++;
            }
        }
        return major;
    }
}
```


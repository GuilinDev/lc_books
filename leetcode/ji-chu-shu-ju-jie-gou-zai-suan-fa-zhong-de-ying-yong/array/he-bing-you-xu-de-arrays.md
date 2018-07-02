# 合并有序Arrays

## 88 - Merge Sorted Array

### 原题概述

Given two sorted integer arrays _nums1_ and _nums2_, merge _nums2_ into _nums1_ as one sorted array.

**Note:**

* The number of elements initialized in _nums1_ and _nums2_ are _m_ and _n_ respectively.
* You may assume that _nums1_ has enough space \(size that is greater or equal to _m_ + _n_\) to hold additional elements from _nums2_.

**Example:**

```text
Input:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

Output: [1,2,2,3,5,6]
```

### 题意与分析

两个排序好的数组nums1和nums2，按照顺序全部元素都放到第一个数组当中，保证装得下所以不用担心nums1数组空间的问题。大致的思路就是在nums1中最后一个位置开始（通过原先两个数组的参数代表有效长度，相加得来），把大的数填在后面，这样就不会覆盖nums1前面的数字了，注意别越界就行。

Time：O\(m + n\)；Space：O\(1\)；

这道题很有可能和[Merge Two Sorted Lists](https://guilindev.gitbook.io/interview/leetcode/ji-chu-shu-ju-jie-gou-zai-suan-fa-zhong-de-ying-yong/linkedlist/he-bing-liang-ge-you-xu-lie-biao)一起问。

### 代码

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int length = m + n;

        while (n > 0) { //表示nums2里面的元素还没有加完
            //从最后一位开始检查，每次循环前移一位
            length--;
            if (m == 0 || nums1[m - 1] < nums2[n - 1]) {//m=0表示只剩nums2的元素了，直接加入就好
                n--;//从nums2最后一位开始
                nums1[length] = nums2[n];
            } else {//m != 0 &&　nums1[m - 1] > nums2[n - 1]
                m--;//从nums1最后一位开始
                nums1[length] = nums1[m];
            }
        }
        //这样写可以短一点: nums1[--length] = (m == 0 || nums[m - 1] < nums[n - 1]) ? nums2[--n] : nums1[--m];
    }
}
```


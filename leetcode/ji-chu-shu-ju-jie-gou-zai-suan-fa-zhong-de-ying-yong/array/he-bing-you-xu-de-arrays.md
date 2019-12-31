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

直观解法

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        if (nums1 == null || nums2 == null || nums1.length == 0 || nums2.length == 0) {
            return;
        }
        int index = m + n - 1;
        int index1 = m - 1;
        int index2 = n - 1;
        
        //直接比较
        while (index1 >= 0 && index2 >= 0) {
            if (nums1[index1] >= nums2[index2]) {
                nums1[index] = nums1[index1];
                index1--;
            } else {
                nums1[index] = nums2[index2];
                index2--;
            }
            index--;
        }
        //如果nums1没有加完，这段可省略
        if (index1 >= 0) {
            while (index1 >= 0) {
                nums1[index] = nums1[index1];
                index--;
                index1--;
            }
        }
        //如果nums2没有加完
        if (index2 >= 0) {
            while (index2 >= 0) {
                nums1[index] = nums2[index2];
                index--;
                index2--;
            }
        }
    }
}
```

简约解法

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        if (nums1 == null || nums2 == null || nums1.length == 0 || nums2.length == 0) {
            return;
        }
        int index = m + n - 1;
        int index1 = m - 1;
        int index2 = n - 1;
        
        while (index2 >= 0) { //nums2中还有元素
            if (index1 < 0 || nums2[index2] >= nums1[index1]) { //index1 < 0 表示nums1中的元素已经比较完了
                nums1[index] = nums2[index2];
                index2--;
            } else {
                nums1[index] = nums1[index1];
                index1--;
            }
            index--;
        }
    }
}

//这样写可以短一点: nums1[--length] = (m == 0 || nums[m - 1] < nums[n - 1]) ? nums2[--n] : nums1[--m];可读性为0
```


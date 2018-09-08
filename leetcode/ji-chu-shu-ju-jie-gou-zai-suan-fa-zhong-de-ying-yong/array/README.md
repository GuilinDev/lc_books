---
description: 包括一维二维数组的各种算法。
---

# Array

## **4 Median of Two Sorted Arrays** 

### 原题概述

There are two sorted arrays **nums1** and **nums2** of size m and n respectively.

Find the median of the two sorted arrays. The overall run time complexity should be O\(log \(m+n\)\).

You may assume **nums1** and **nums2** cannot be both empty.

**Example 1:**

```text
nums1 = [1, 3]
nums2 = [2]

The median is 2.0
```

**Example 2:**

```text
nums1 = [1, 2]
nums2 = [3, 4]

The median is (2 + 3)/2 = 2.5
```

### 题意和分析

求两个有序数组的中位数，要求复杂度O\(log \(m+n\)\)，看到这个只能是二分法了；如果将两个数组合并后再查找中位数的话为O\(m+n\)，不满足条件（不过这本身是Merge Sort的一个步骤），做法参考了下面链接的第三个解法

{% embed data="{\"url\":\"http://windliang.cc/2018/07/18/leetCode-4-Median-of-Two-Sorted-Arrays/\",\"type\":\"link\",\"title\":\"leetCode\_4\_Median\_of\_Two\_Sorted\_Arrays\",\"description\":\"题目描述（困难难度） 已知两个有序数组，找到两个数组合并后的中位数。 解法一简单粗暴，先将两个数组合并，两个有序数组的合并也是归并排序中的一部分。然后根据奇数，还是偶数，返回中位数。 代码1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950public double f\",\"icon\":{\"type\":\"icon\",\"url\":\"http://windliang.cc/images/apple-touch-icon-next.png?v=6.3.0\",\"width\":180,\"height\":180,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"http://windliang.oss-cn-beijing.aliyuncs.com/mid8.jpg\",\"width\":1038,\"height\":318,\"aspectRatio\":0.3063583815028902}}" %}

假设我们要找第 k 小数，我们可以每次循环排除掉 k / 2 个数。看下边一个例子。

假设我们要找第 7 小的数字。

![](http://windliang.oss-cn-beijing.aliyuncs.com/mid1.jpg)

我们比较两个数组的第 k / 2 个数字，如果 k 是奇数，向下取整。也就是比较第 3 个数字，上边数组中的 8 和下边数组中的 3 ，如果哪个小，就表明该数组的前 k / 2 个数字都不是第 k 小数字，所以可以去掉。也就是 1，2，3 这三个数字不可能是第 7 小的数字，我们可以把它排除掉。将 1389 和 45678910 两个数组作为新的数组进行比较。

总结下就是 A \[ 1 \]，A \[ 2 \]，A \[ 3 \]，A \[ k / 2\] … ，B\[ 1 \]，B \[ 2 \]，B \[ 3 \]，B\[ k / 2\] … ，如果 A \[ k / 2 \] &lt; B \[ k / 2 \] ，那么 A \[ 1 \]，A \[ 2 \]，A \[ 3 \]，A \[ k / 2\] 都不可能是第 k 小的数字。

A 数组中比 A \[ k / 2 \] 小的数有 k / 2 - 1 个，B 数组中，B \[ k / 2 \] 比 A \[ k / 2 \] 小，假设 B \[ k / 2 \] 前边的数字都比 A \[ k / 2 \] 小，也只有 k / 2 - 1 个，所以比 A \[ k / 2 \] 小的数字最多有 k / 2 - 1 + k / 2 - 1 = k - 2 个，所以 A \[ k / 2 \] 最多是第 k - 1 小的数。而比 A \[ k / 2 \] 小的数更不可能是第 k 小的数了，所以可以把它们排除。

橙色的部分表示已经去掉的数字。

![](http://windliang.oss-cn-beijing.aliyuncs.com/mid2.jpg)

由于我们已经排除掉了 3 个数字，就是这 3 个数字一定在最前边，所以在两个新数组中，我们只需要找第 7 - 3 = 4 小的数字就可以了，也就是 k = 4 。此时两个数组，比较第 2 个数字，3 &lt; 5，所以我们可以把小的那个数组中的 1 ，3 排除掉了。

![](http://windliang.oss-cn-beijing.aliyuncs.com/mid3.jpg)

我们又排除掉 2 个数字，所以现在找第 4 - 2 = 2 小的数字就可以了。此时比较两个数组中的第 k / 2 = 1 个数，4 = 4 ，怎么办呢？由于两个数相等，所以我们无论去掉哪个数组中的都行，因为去掉 1 个总会保留 1 个的，所以没有影响。为了统一，我们就假设 4 &gt; 4 吧，所以此时将下边的 4 去掉。

![](http://windliang.oss-cn-beijing.aliyuncs.com/mid4.jpg)

由于又去掉 1 个数字，此时我们要找第 1 小的数字，所以只需判断两个数组中第一个数字哪个小就可以了，也就是 4 。

所以第 7 小的数字是 4 。

我们每次都是取 k / 2 的数进行比较，有时候可能会遇到数组长度小于 k / 2 的时候。

![](http://windliang.oss-cn-beijing.aliyuncs.com/mid5.jpg)

此时 k / 2 等于 3 ，而上边的数组长度是 2 ，我们此时将箭头指向它的末尾就可以了。这样的话，由于 2 &lt; 3 ，所以就会导致上边的数组 1，2 都被排除。造成下边的情况。

![](http://windliang.oss-cn-beijing.aliyuncs.com/mid6.jpg)

由于 2 个元素被排除，所以此时 k = 5 ，又由于上边的数组已经空了，我们只需要返回下边的数组的第 5 个数字就可以了。

从上边可以看到，无论是找第奇数个还是第偶数个数字，对我们的算法并没有影响，而且在算法进行中，k 的值都有可能从奇数变为偶数，最终都会变为 1 或者由于一个数组空了，直接返回结果。

所以我们采用递归的思路，为了防止数组长度小于 k / 2 ，所以每次比较 min \( k / 2，len \( 数组 \) \) 对应的数字，把小的那个对应的数组的数字排除，将两个新数组进入递归，并且 k 要减去排除的数字的个数。递归出口就是当 k = 1 或者其中一个数字长度是 0 了。

### 代码

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int n = nums1.length;
        int m = nums2.length;
        int left = (n + m + 1) / 2;
        int right = (n + m + 2) / 2;

        return (getKth(nums1, 0, n - 1, nums2, 0, m - 1, left) + getKth(nums1, 0, n - 1, nums2, 0, m - 1, right)) * 0.5;
    }

    //虽然用到了递归，但是可以看到这个递归属于尾递归，所以编译器不需要不停地堆栈，所以空间复杂度为 O（1）
    private int getKth(int[] nums1, int start1, int end1, int[] nums2, int start2, int end2, int k) {
        int len1 = end1 - start1 + 1;
        int len2 = end2 - start2 + 1;

        //确保len1 的长度小于 len2，这样就能保证如果有数组空了，一定是 len1，方便控制流程
        if (len1 > len2) return getKth(nums2, start2, end2, nums1, start1, end1, k);

        if (len1 == 0) return nums2[start2 + k - 1];//如果有数组空了就直接返回nums2剩下元素中的中位数

        if (k == 1) return Math.min(nums1[start1], nums2[start2]);

        int i = start1 + Math.min(len1, k/2) - 1;
        int j = start2 + Math.min(len2, k/2) - 1;

        if (nums1[i] > nums2[j]) {
            return getKth(nums1, start1, end1, nums2, j + 1, end2, k - (j - start2 + 1));
        } else {
            return getKth(nums1, i + 1, end1, nums2, start2, end2, k - (i - start1 + 1));
        }
    }
}
```

## **11 Container With Most Water**

### 原题概述

### 题意和分析

### 代码

## **561 Array Partition I** 

### 原题概述

### 题意和分析

### 代码

## **122 Best Time to Buy and Sell Stock II** 

### 原题概述

### 题意和分析

### 代码

## **169 Majority Element** 

### 原题概述

### 题意和分析

### 代码

## **283 Move Zeroes** 

### 原题概述

### 题意和分析

### 代码

## **238 Product of Array Except Self** 

### 原题概述

### 题意和分析

### 代码

## **217 Contains Duplicate** 

### 原题概述

### 题意和分析

### 代码

## **48 Rotate Image** 

### 原题概述

### 题意和分析

### 代码

## **219 Contains Duplicate II**

## 原题概述

### 题意和分析

### 代码 


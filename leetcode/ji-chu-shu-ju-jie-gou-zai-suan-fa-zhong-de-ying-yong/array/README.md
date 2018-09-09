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

Given n non-negative integers a1, a2, ..., an , where each represents a point at coordinate \(i, ai\). n vertical lines are drawn such that the two endpoints of line i is at \(i, ai\) and \(i, 0\). Find two lines, which together with x-axis forms a container, such that the container contains the most water.

**Note:** You may not slant the container and n is at least 2.

![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg)

The above vertical lines are represented by array \[1,8,6,2,5,4,8,3,7\]. In this case, the max area of water \(blue section\) the container can contain is 49.

**Example:**

```text
Input: [1,8,6,2,5,4,8,3,7]
Output: 49
```

### 题意和分析

跟42 Trapping Rain Water类似，略简单一点，只需要用双指针指向头尾，然后往中间走计算装雨水的面积即可，移动的办法是挪动较矮的线的index以防错过最大值，计算面积的方法是找到两条线中较矮的那条，乘以两条线的距离。

### 代码

```java
class Solution {
    public int maxArea(int[] height) {
        int result = 0, left = 0, right = height.length - 1;

        while (left < right) {
            result = Math.max(result, Math.min(height[left], height[right]) * (right - left));

            //移动较矮的那条线的index
            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }
        return result;
    }
}
```

## **561 Array Partition I** 

### 原题概述

Given an array of **2n** integers, your task is to group these integers into **n** pairs of integer, say \(a1, b1\), \(a2, b2\), ..., \(an, bn\) which makes sum of min\(ai, bi\) for all i from 1 to n as large as possible.

**Example 1:**  


```text
Input: [1,4,3,2]

Output: 4
Explanation: n is 2, and the maximum sum of pairs is 4 = min(1, 2) + min(3, 4).
```

**Note:**  


1. **n** is a positive integer, which is in the range of \[1, 10000\].
2. All the integers in the array will be in the range of \[-10000, 10000\].

### 题意和分析

这道题要求把2n个元素的数组两两一对分成n个，然后找到每一对中较小的那个，加起来的和是最大的；使用greedy，将比较临近的两个数字排成一对，取其中较小值（如果把很小和很大的数字放在一起，去小的数字，那大的数字就不在结果中了）。具体做法是先排序然后隔一个数字取一下就行了。

### 代码

```java
class Solution {
    public int arrayPairSum(int[] nums) {
        Arrays.sort(nums);
        int result = 0;
        for (int i = 0; i < nums.length; i += 2) {
            result += nums[i];
        }
        return result;
    }
}
```

## **122 Best Time to Buy and Sell Stock II** 

### 原题概述

Say you have an array for which the _i_th element is the price of a given stock on day _i_.

Design an algorithm to find the maximum profit. You may complete as many transactions as you like \(i.e., buy one and sell one share of the stock multiple times\).

**Note:** You may not engage in multiple transactions at the same time \(i.e., you must sell the stock before you buy again\).

**Example 1:**

```text
Input: [7,1,5,3,6,4]
Output: 7
Explanation: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4.
             Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.
```

**Example 2:**

```text
Input: [1,2,3,4,5]
Output: 4
Explanation: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
             Note that you cannot buy on day 1, buy on day 2 and sell them later, as you are
             engaging multiple transactions at the same time. You must sell before buying again.
```

**Example 3:**

```text
Input: [7,6,4,3,1]
Output: 0
Explanation: In this case, no transaction is done, i.e. max profit = 0.
```

### 题意和分析

这道题跟121 Best Time to Buy and Sell Stock相比，可以无限购买，那就从第二天开始算，如果当前的股票价格高于前一日的话，就把差额利润算入到结果当中，如果后面的值更高，那继续把利润加入；虽然例子中说了不能卖了再买，但是如上算法的利润是一样的，所以只需遍历数组，然后把每个数和它后面的数进行比较即可。

### 代码

```java
class Solution {
    public int maxProfit(int[] prices) {
        int result = 0;
        for (int i = 0; i < prices.length - 1; i++) {//到倒数第二个数为止
            if (prices[i] < prices[i+1]) {
                result += prices[i+1] - prices[i];
            }
        }
        return result;
    }
}
```

## **169 Majority Element** 

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

这道题要求众数，首先因为出现的次数是多于n/2的次数的，所以可以排序然后最中间的数就是需要找的数；另外有个更高效的办法，所以只需要每次只需出现一个数就+1，然后出现不同的数就-1，最后剩下的数一定是需要找的数，因为众数一定存在，出现次数是大于n/2的，所以它的出现次数比别的数加起来的出现次数都多。

另外也可以用bit manipulation来做， 将数组中的元素按位来建立，表示的长度是32位，每次统计下数组中该元素位上0和1的个数，如果1多，那么我们将结果result中该位变为1，最后累加出来的res就是中位数了

### 代码

投票算法

```java
class Solution {
    public int majorityElement(int[] nums) {
        int count = 0, result = 0;
        for (int num : nums) {
            if (count == 0) {
                result = num;
                count++;
            } else if (result == num) {
                count++;
            } else {
                count--;
            }
        }
        return result;
    }
}
```

位操作

```java
class Solution {
    public int majorityElement(int[] nums) {
        int result = 0, n = nums.length;
        for (int i = 0; i < 32; i++) {
            int ones = 0, zeros = 0;
            for (int num : nums) {
                if (ones > n / 2 || zeros > n / 2) break;
                if ((num & (1 << i)) != 0) {
                    ones++;
                } else {
                    zeros++;
                }
            }
            if (ones > zeros) result |= (1 << i);
        }
        return result;
    }
}
```

## **283 Move Zeroes** 

### 原题概述

Given an array `nums`, write a function to move all `0`'s to the end of it while maintaining the relative order of the non-zero elements.

**Example:**

```text
Input: [0,1,0,3,12]
Output: [1,3,12,0,0]
```

**Note**:

1. You must do this **in-place** without making a copy of the array.
2. Minimize the total number of operations.

### 题意和分析

给一个数组，把其中的0挪到最后面去，非0元素的相对顺序不能变，不能另外开一个数组；使用两个指针，从0位置开始查，找到不为0的元素后，与另外一个指针交换值，直到末尾。

### 代码

```java
class Solution {
    public void moveZeroes(int[] nums) {
        for (int left = 0, right = 0; right < nums.length; right++) {
            if (nums[right] != 0) {//把非0的元素全部换到前面来
                int temp = nums[right];
                nums[right] = nums[left];
                nums[left] = temp;
                left++;//挪动指针从非0元素到下一位
            }
        }
    }
}
```

## **238 Product of Array Except Self** 

### 原题概述

Given an array `nums` of _n_ integers where _n_ &gt; 1,  return an array `output` such that `output[i]` is equal to the product of all the elements of `nums` except `nums[i]`.

**Example:**

```text
Input:  [1,2,3,4]
Output: [24,12,8,6]
```

**Note:** Please solve it **without division** and in O\(_n_\).

**Follow up:**  
Could you solve it with constant space complexity? \(The output array **does not** count as extra space for the purpose of space complexity analysis.\)

### 题意和分析

给一个数组，让返回一个新数组，对于每一个位置上的数是其他位置上数的乘积，并且限定了时间复杂度O\(n\)，不让用除法。对于数组中的某个元素，如果知道该数前面所有数的乘积，也知道该数后面所有数的乘积，二者相乘就是对该数的结果；根据这一点，可以先遍历数组两遍，第一次遍历算出针对第i个元素前面所有元素的乘积，第二次遍历算出针对第i个元素后面所有元素的乘积；然后第三次遍历分别相乘即可；

可以对空间进行优化，无需单独的数组来保存乘积，而是直接累积到result中，第一次遍历，将所有i元素前面的乘积的累积存入result数组中，然后从后面开始遍历，用一个临时变量back来存储后面的乘积，初始化为1，然后每次不断累积，将back的值乘入到相对应的result数组的位置上。

### 代码

用数组来保存乘积

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int len = nums.length;
        int[] front = new int[len], back = new int[len], result = new int[len];
        //初始化乘积为1
        front[0] = 1;
        back[len - 1] = 1;
        //计算i元素前面所有元素的乘积
        for (int i = 1; i < len; i++) {
            front[i] = front[i - 1] * nums[i - 1];
        }
        //计算i元素后面所有元素的乘积
        for (int i = len - 2; i >= 0; i--) {
            back[i] = back[i + 1] * nums[i + 1];
        }

        //二者相乘
        for (int i = 0; i < len; i++) {
            result[i] = front[i] * back[i];
        }
        return result;
    }
}
```

优化空间

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int len = nums.length, back = 1;
        int[] result = new int[len];

        //第一次遍历计算所有i元素前面的乘积
        result[0] = 1;//初始化为1，因为第二次遍历会乘过来
        for (int i = 1; i < len; i++) {
            result[i] = result[i - 1] * nums[i - 1];
        }

        //第二次遍历从后面开始计算，逐渐累积
        for (int i = len - 1; i >= 0; i--) {
            result[i] *= back;
            back *= nums[i];
        }

        return result;
    }
}
```

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


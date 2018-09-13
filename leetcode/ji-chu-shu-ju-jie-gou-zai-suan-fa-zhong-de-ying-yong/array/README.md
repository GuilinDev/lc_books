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

Given an array of integers, find if the array contains any duplicates.

Your function should return true if any value appears at least twice in the array, and it should return false if every element is distinct.

**Example 1:**

```text
Input: [1,2,3,1]
Output: true
```

**Example 2:**

```text
Input: [1,2,3,4]
Output: false
```

**Example 3:**

```text
Input: [1,1,1,3,3,4,3,2,4,2]
Output: true
```

### 题意和分析

判断数组是否包含有任意元素的重复元素，如果包含返回true；可以先排序然后比较相邻元素，也可以使用HashMap来检查，如果元素不存在，放入HashMap中，如果存在，返回false。要时间还是要空间。

### 代码

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int num : nums) {
            if (map.containsKey(num)) {
                return true;
            } else {
                map.put(num, 1);
            }
        }
        return false;
    }
}
```

## **48 Rotate Image** 

### 原题概述

You are given an _n_ x _n_ 2D matrix representing an image.

Rotate the image by 90 degrees \(clockwise\).

**Note:**

You have to rotate the image [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm), which means you have to modify the input 2D matrix directly. **DO NOT** allocate another 2D matrix and do the rotation.

**Example 1:**

```text
Given input matrix = 
[
  [1,2,3],
  [4,5,6],
  [7,8,9]
],

rotate the input matrix in-place such that it becomes:
[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]
```

**Example 2:**

```text
Given input matrix =
[
  [ 5, 1, 9,11],
  [ 2, 4, 8,10],
  [13, 3, 6, 7],
  [15,14,12,16]
], 

rotate the input matrix in-place such that it becomes:
[
  [15,13, 2, 5],
  [14, 3, 4, 1],
  [12, 6, 8, 9],
  [16, 7,10,11]
]
```

### 题意和分析

计算机里图片的本质是矩阵，旋转矩阵即是旋转图片，有很多方法可以旋转矩阵，我自己比较好理解的两种办法是：

1）首先对原数组取其转置矩阵（行列互换），然后把每行的数字翻转可得到结果，如下所示\(其中蓝色数字表示翻转轴\)：

1  2  3　　　 　　 1  4  7　　　　　  7  4  1

4  5  6　　--&gt;　　 2  5  8　　 --&gt;  　  8  5  2　　

7  8  9 　　　 　　3  6  9　　　　      9  6  3

2）首先以从对角线为轴翻转，然后再以x轴中线上下翻转即可得到结果：

1  2  3　　　 　　 9  6  3　　　　　  7  4  1

4  5  6　　--&gt;　　 8  5  2　　 --&gt;   　 8  5  2　　

7  8  9 　　　 　　7  4  1　　　　　  9  6  3

3）每次循环换四个数字：

1  2  3                 7  2  1                  7  4  1

4  5  6      --&gt;      4  5  6　　 --&gt;  　 8  5  2　　

7  8  9                 9  8  3　　　　　 9  6  3

### 代码

转置矩阵的办法

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        for (int i = 0; i < n; i++) {
            for (int j = i; j < n; j++) {//j = i不用重复转置
                //转换为转置矩阵transport matrix
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
        //逐行将元素翻转
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n/2; j++) {//注意这里是j < n/2，没有=
                int temp = matrix[i][j];
                matrix[i][j] = matrix[i][n - j - 1];
                matrix[i][n - j - 1] = temp;
            }
        }
    }
}
```

对角线翻转的办法

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        //以对角线为轴翻转
        for (int i = 0; i < n - 1; i++) {
            for (int j = 0; j < n - i; j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[n - 1 - j][n - 1 - i];
                matrix[n - 1 - j][n - 1 - i] = temp;
            }
        }
        //以x轴中线上下翻转
        for (int i = 0; i < n / 2; i++) {
            for (int j = 0; j < n; j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[n - 1 - i][j];
                matrix[n - 1 - i][j] = temp;
            }
        }
    }
}
```

直接旋转

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        for (int i = 0; i < n / 2; i++) {
            for (int j = i; j < n - 1 - i; j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[n - 1 - j][i];
                matrix[n - 1 - j][i] = matrix[n - 1 - i][n - 1 - j];
                matrix[n - 1 - i][n - 1 - j] = matrix[j][n - 1 - i];
                matrix[j][n - 1 - i] = temp;
            }
        }
    }
}
```

## **219 Contains Duplicate II**

### 原题概述

Given an array of integers and an integer k, find out whether there are two distinct indices i and j in the array such that **nums\[i\] = nums\[j\]** and the **absolute** difference between i and j is at most k.

**Example 1:**

```text
Input: nums = [1,2,3,1], k = 3
Output: true
```

**Example 2:**

```text
Input: nums = [1,0,1,1], k = 1
Output: true
```

**Example 3:**

```text
Input: nums = [1,2,3,1,2,3], k = 2
Output: false
```

### 题意和分析

第217 Contains Duplicate的延伸，加了个条件检查重复元素的索引是否超过k，没超过就返回true，同样用HashMap来存，k-v分别是元素的值和索引，检查重复的时候加一个检查条件即可。

### 代码

```java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(nums[i]) && i - map.get(nums[i]) <= k) {
                return true;
            }
            map.put(nums[i], i);
        }
        return false;
    }
}
```

也可以用HashSet添加值会有一个boolean的返回值的特性来做

```java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        Set<Integer> set = new HashSet<>();
        for (int i = 0; i < nums.length; i++) {
            if (i > k)set.remove(nums[i - k - 1]);//最多k个元素，超过k个元素就从头开始删除
            if (!set.add(nums[i])) return true;//加不进去了，说明距离比k小
        }
        return false;
    }
}
```

## **41 First Missing Positive**

### **原题概述**

Given an unsorted integer array, find the smallest missing positive integer.

**Example 1:**

```text
Input: [1,2,0]
Output: 3
```

**Example 2:**

```text
Input: [3,4,-1,1]
Output: 2
```

**Example 3:**

```text
Input: [7,8,9,11,12]
Output: 1
```

**Note:**

Your algorithm should run in _O_\(_n_\) time and uses constant extra space.

### **题意和分析**

给一个数组，返回第一个缺失的正数，要求线性时间复杂度O\(n\)和常量空间O\(1\)，因此一般的排序方法是不能用的，另外用空间也有要求所以利用额外空间例如HashMap和HashSet也不能用了；只能in-place来做，遍历数组，把1放到nums\[0\]处，把2放到nums\[1\]处，如果nums\[i\] &gt; 0（**负数和0不用管**），同时nums\[i\]为整数且不大于n（**因为缺失的第一个正数值最大就是数组的长度n，不可能超过**），同时nums\[i\]不等于nums\[nums\[i\] - 1\]的话（**桶排序的思想，对应的数字应该放在对应的位置上**），则交换nums\[i\]和nums\[nums\[i\] - 1\]的位置；然后再遍历一遍，遇到nums\[i\] != i + 1即为第一个缺失的正数。

### **代码**

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        if (nums == null || nums.length == 0) return 1;
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            while (nums[i] > 0 && nums[i] <= n && nums[i] != nums[nums[i] - 1]) {
                int temp = nums[nums[i] - 1];
                nums[nums[i] - 1] = nums[i];
                nums[i] = temp;
            }
        }
        for (int i = 0; i < n; i++) {
            if (nums[i] != i + 1) return i + 1;
        }

        //从1开始连续出现
        return n + 1;
    }
}
```

## **128 Longest Consecutive Sequence** 

### **原题概述**

Given an unsorted array of integers, find the length of the longest consecutive elements sequence.

Your algorithm should run in O\(_n_\) complexity.

**Example:**

```text
Input: [100, 4, 200, 1, 3, 2]
Output: 4
Explanation: The longest consecutive elements sequence is [1, 2, 3, 4]. Therefore its length is 4.
```

### **题意和分析**

没有排序的数组里面寻找最长的子序列，要求时间复杂度是O\(n\)，没有空间复杂度的要求，于是可以用一个HashSet，把数组里面所有的元素放入到set里面，然后遍历数组，对每个元素都进行移除操作，同时用两个指针prev和next求出当前元素的构成连续数列的前面和后面一个数，继续检查prev和next是否在set中存在，如果存在就继续移除，最后用next - prev - 1（因为两个指针指向的元素在set中不存在的时候才停止移除，所以有-1），对每个元素都进行这样的操作后求出连续序列最大的。

 也可以采用HashMap来做，刚开始map为空，然后遍历所有数组中的元素，如果该数字不在map中，那么分别检查前后两个数字是否在map中，如果在，则返回其哈希表中映射值，若不在，则返回0，将prev+next+1作为当前数字的映射，并更新result结果，然后更新num-left和num-right的映射值。

### **代码**

HashSet

```java
class Solution {
   public int longestConsecutive(int[] nums) {
      if (nums == null || nums.length == 0) return 0;

      HashSet<Integer> set = new HashSet<>();
      int result = 0;

      //将数组里的所有元素放到HashSet里面
      for (int num : nums) set.add(num);

      for (int num : nums) {
         if (set.remove(num)) {//Java的remove方法是有返回值的，同样add也有
            int prev = num - 1, next = num + 1;
            while (set.remove(prev)) prev--;
            while (set.remove(next)) next++;

            result = Math.max(result, next - prev - 1);
         }
      }
      return result;
   }
}
```

HashMap

```java
class Solution {
   public int longestConsecutive(int[] nums) {
      if (nums == null || nums.length == 0) return 0;

      HashMap<Integer, Integer> map = new HashMap<>();
      int result = 0;
      for (int num : nums) {
         if (!map.containsKey(num)) {
            //注意这里的prev和next是元素在HashMap中的索引值
            int prev = map.containsKey(num - 1) ? map.get(num - 1) : 0;
            int next = map.containsKey(num + 1) ? map.get(num + 1) : 0;

            int sum = prev + next + 1;
            map.put(num, sum);

            result = Math.max(result, sum);

            map.put(num - prev, sum);
            map.put(num + next, sum);
         }
      }

      return result;
   }
}
```

## **54 Spiral Matrix** 

### **原题概述**

Given a matrix of _m_ x _n_ elements \(_m_ rows, _n_ columns\), return all elements of the matrix in spiral order.

**Example 1:**

```text
Input:
[
 [ 1, 2, 3 ],
 [ 4, 5, 6 ],
 [ 7, 8, 9 ]
]
Output: [1,2,3,6,9,8,7,4,5]
```

**Example 2:**

```text
Input:
[
  [1, 2, 3, 4],
  [5, 6, 7, 8],
  [9,10,11,12]
]
Output: [1,2,3,4,8,12,11,10,9,5,6,7]
```

### **题意和分析**

要求由外层向内层螺旋打印数组，只能一行一列地打印，先往右，再往下，再往左，最后往上，用四个变量来记录打印的位置，下一轮从新的打印位置开始。

### **代码**

```java
class Solution {
   public List<Integer> spiralOrder(int[][] matrix) {

      List<Integer> result = new ArrayList<>();
      if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return result;

      int rowBegin = 0;
      int colEnd = matrix[0].length - 1;
      int colBegin = 0;
      int rowEnd = matrix.length - 1;

      while (rowBegin <= rowEnd && colBegin <= colEnd) {
         //从左向右
         for (int i = colBegin; i <= colEnd; i++) {
            result.add(matrix[rowBegin][i]);
         }
         rowBegin++;

         //从上到下
         for (int i = rowBegin; i <= rowEnd; i++) {
            result.add(matrix[i][colEnd]);
         }
         colEnd--;

         //从右到左
         if (rowBegin <= rowEnd) {//这里检查防止行已经打印完重复打印
            for (int i = colEnd; i >= colBegin; i--) {
               result.add(matrix[rowEnd][i]);
            }
         }
         rowEnd--;

         //从下到上
         if (colBegin <= colEnd) {//这里检查防止列已经打印完重复打印
            for (int i = rowEnd; i >= rowBegin; i--) {
               result.add(matrix[i][colBegin]);
            }
         }
         colBegin++;

      }
      return result;
   }
}
```

## **229 Majority Element II** 

### **原题概述**

Given an integer array of size n, find all elements that appear more than `⌊ n/3 ⌋` times.

**Note:** The algorithm should run in linear time and in O\(1\) space.

**Example 1:**

```text
Input: [3,2,3]
Output: [3]
```

**Example 2:**

```text
Input: [1,1,1,3,3,2,2,2]
Output: [1,2]
```

### **题意和分析**

跟169 Major Element相比，这道题是要求返回所出现次数超过1/3的元素，另外还规定了线性时间复杂度和常数时间，所以想利用数据结构来统计不可行；因为是出现次数大于1/3，所以众数最多是两个，最少是没有。做法是两次遍历，第一次遍历找出两个众数作为备选，第二次遍历利用投票方法确认这两个众数是否合格。

### **代码**

```java
class Solution {
   public List<Integer> majorityElement(int[] nums) {
      List<Integer> result = new ArrayList<>();
      if (nums == null || nums.length == 0) return result;
      int candidate1 = 0, candidate2 = 0, count1 = 0, count2 = 0;
      for (int num : nums) {//利用投票法则，至少有两个数的出现次数是多于其它数的
         if (num == candidate1) {
            count1++;
         } else if (num == candidate2) {
            count2++;
         } else if (count1 == 0) {
            candidate1 = num;
            count1 = 1;
         } else if (count2 == 0) {
            candidate2 = num;
            count2 = 1;
         } else {
            count1--;
            count2--;
         }
      }

      //重新计算一下两个备选众数的实际出现次数，看是否超过1/3
      count1 = 0;
      count2 = 0;
      for (int num : nums) {
         if (num == candidate1) count1++;
         else if (num == candidate2) count2++;
      }
      if (count1 > nums.length/3) result.add(candidate1);
      if (count2 > nums.length/3) result.add(candidate2);

      return result;
   }
}
```

## **228 Summary Ranges** 

### **原题概述**

### **题意和分析**

### **代码**

## **31 Next Permutation** 

### **原题概述**

### **题意和分析**

### **代码**

## **448 Find All Numbers Disappeared in an Array** 

### **原题概述**

### **题意和分析**

### **代码**

## **287 Find the Duplicate Number** 

### **原题概述**

### **题意和分析**

### **代码**

## **189 Rotate Array** 

### **原题概述**

### **题意和分析**

### **代码**

## **79 Word Search** 

### **原题概述**

### **题意和分析**

### **代码**

## **73 Set Matrix Zeroes**

### **原题概述**

### **题意和分析**

### **代码**

## **289 Game of Life** 

### **原题概述**

### **题意和分析**

### **代码**

## **695 Max Area of Island** 

### **原题概述**

### **题意和分析**

### **代码**

###  


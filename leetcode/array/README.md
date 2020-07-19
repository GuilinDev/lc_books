---
description: 包括一维二维数组的各种算法。
---

# Array

## 66 - Plus One

### 原题概述

> Given a **non-empty** array of digits representing a non-negative integer, plus one to the integer.
>
> The digits are stored such that the most significant digit is at the head of the list, and each element in the array contain a single digit.
>
> You may assume the integer does not contain any leading zero, except the number 0 itself.
>
> **Example 1:**
>
> ```text
> Input: [1,2,3]
> Output: [1,2,4]
> Explanation: The array represents the integer 123.
> ```
>
> **Example 2:**
>
> ```text
> Input: [4,3,2,1]
> Output: [4,3,2,2]
> Explanation: The array represents the integer 4321.
> ```

### 题意和分析

这道题目很简单，就是需要考虑数组中最后一位加1的时候进位的问题。

Time: O\(n\)

### 代码

```java
class Solution {
    public int[] plusOne(int[] digits) {
        int carry = 1;
        
        //如果carry==0，没有往前进位的情况，就终止循环，不再加了
        for (int i = digits.length - 1; i >= 0 && carry > 0; i--) {
            int sum = digits[i] + carry;
            digits[i] = sum % 10;
            carry = sum / 10;
        }
        
        //处理数组中最后一次加法（第0位）可能往前进位的情况
        if (carry == 0) {//没有进位了，直接返回
            return digits;
        } else { //有进位，新建一个长度多一位的array
            int[] newDigits = new int[digits.length + 1];
            newDigits[0] = 1;
            for (int j = 1; j <= digits.length; j++) {
                newDigits[j] = digits[j - 1];
            }
            return newDigits;
        }
    }
}
```



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

{% embed url="http://windliang.cc/2018/07/18/leetCode-4-Median-of-Two-Sorted-Arrays/" %}

假设我们要找第 k 小数（从1开始数），我们可以每次循环排除掉 k / 2 个数。看下边一个例子。

假设我们要找第 7 小的数字。

![](http://windliang.oss-cn-beijing.aliyuncs.com/mid1.jpg)

我们比较两个数组的第 k / 2 个数字\(如果 k 是奇数，向下取整，方便排除\)，也就是比较第 3 个数字，上边数组中的 4 和下边数组中的 3 ，如果哪个小，就表明该数组的前 k / 2 个数字都不是第 k 小数字（两个数组一个出一半，较小数和其左边的数肯定不会是二者共同考虑的中位数），所以较小数和其在数组中左边的数都“太小了”，可以去掉。也就是 1，2，3 这三个数字不可能是第 7 小的数字，我们可以把它排除掉。将 1349 和 45678910 两个数组作为新的数组进行比较。

总结， A \[ 1 \]，A \[ 2 \]，A \[ 3 \]，A \[ k / 2\] … ，B\[ 1 \]，B \[ 2 \]，B \[ 3 \]，B\[ k / 2\] … ，如果 A \[ k / 2 \] &lt; B \[ k / 2 \] ，那么 A \[ 1 \]，A \[ 2 \]，A \[ 3 \]，A \[ k / 2\] 都不可能是第 k 小的数字。

证明，现在A 数组中比 A \[ k / 2 \] 小的数有 k / 2 - 1 个，而在B 数组中，如果B \[ k / 2 \]这个数字 比 A \[ k / 2 \] 大，那么 就算B \[ k / 2 \] 前边的所有数字都比 A \[ k / 2 \] 小，也只有 k / 2 - 1 个，所以比 A \[ k / 2 \] 小的数字最多有 k / 2 - 1 + k / 2 - 1 = k - 2 个（索引从0开始），所以 A \[ k / 2 \] 最多是第 k - 1 小的数，而左边的比 A \[ k / 2 \] 小的数那就更不可能是第 k 小的数了，所以可以把从A\[k / 2\]开始向左边的所有数排除。

下图橙色的部分表示已经可以去掉的数字，

![](http://windliang.oss-cn-beijing.aliyuncs.com/mid2.jpg)

由于我们已经排除掉了 3 个数字，就是这 3 个数字一定在最前边，所以在两个新数组中，我们只需要找第 7 - 3 = 4 小的数字就可以了，也就是 k = 4 。此时两个数组，比较第k / 2 = 2 个数字，3 &lt; 5，所以我们可以把小的那个数组中的 1 ，3 排除掉了。

![](http://windliang.oss-cn-beijing.aliyuncs.com/mid3.jpg)

我们又排除掉 2 个数字，所以现在找第 \(4 - 2\) / 2 = 1 小的数字就可以了。此时比较两个数组中的第 k / 2 = 1 个数，4 = 4 ，怎么办呢？由于两个数相等，所以我们无论去掉哪个数组中的都行，因为去掉 1 个总会保留 1 个的，所以没有影响。为了统一，我们就假设 4 &gt; 4 吧，所以此时将下边的 4 去掉。

![](http://windliang.oss-cn-beijing.aliyuncs.com/mid4.jpg)

由于又去掉 1 个数字，1 / 2 = 0，此时我们要找第 0 小的数字，就是找到的比较小的数本身，所以只需判断两个数组中第一个数字哪个小就可以了，这里也就是 4 。

所以第 7 小的数字是 4 。

我们每次都是取 k / 2 的数进行比较，有时候可能会遇到数组长度小于 k / 2 的时候。

![](http://windliang.oss-cn-beijing.aliyuncs.com/mid5.jpg)

此时 k / 2 等于 3 ，而上边的数组长度是 2 ，我们此时将箭头指向它的末尾就可以了。这样的话，由于 2 &lt; 3 ，所以就会导致上边的数组 1，2 都被排除。造成下边的情况。

![](http://windliang.oss-cn-beijing.aliyuncs.com/mid6.jpg)

由于 2 个元素被排除，所以此时 k = 5 ，又由于上边的数组已经空了，我们只需要返回下边的数组的第 5 个数字就可以了，如果上面的数组的数字较大也是同样的道理，总之第k大的数字不会出现在较短的数组里面。

从上边可以看到，无论是找第奇数个还是第偶数个数字，对我们的算法并没有影响，而且在算法进行中，k 的值都有可能从奇数变为偶数，最终都会变为 1 或者由于一个数组空了，直接返回结果。

所以我们采用递归的思路，为了防止数组长度小于 k / 2 ，所以每次比较 min \( k / 2，len \( 数组 \) \) 对应的数字，把小的那个对应的数组的数字排除，将两个新数组进入递归，并且 k 要减去排除的数字的个数。递归出口就是当 k = 1 或者其中一个数字长度是 0 了。

### 代码

1. 如同上述解释的做法

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

同样的思路，另一种写法，更清楚一点

```java
class Solution {
    /**
    * 寻找一个unioned sorted array中的第k大（从1开始数）的数。因而等价于寻找并判断两个sorted array中第k/2（从1开始数）大的数。
    * 特殊化到求median，那么对于奇数来说，就是求第(m + n) / 2 + 1（地板除法，从1开始数）大的数。
    * 而对于偶数来说，就是求第(m + n) / 2大（地板除法，从1开始数）和第(m + n) / 2 + 1大（地板除法，从1开始数）的数的算术平均值。
    */
    public double findMedianSortedArrays(int[] nums1, int[] nums2) { 
        /**
        * 那么如何判断两个有序数组A,B中第k大的数呢？

        * 我们需要判断A[k/2-1]和B[k/2-1]的大小。

        * 如果A[k/2-1]==B[k/2-1]，那么这个数就是两个数组中第k大的数。

        * 如果A[k/2-1] < B[k/2-1], 那么说明A[0]到A[k/2-1]都不可能是第k大的数，所以需要舍弃这一半，继续从A[k/2]到A[A.length-1]继续找。当然，因为这里舍弃了A[0]到A[k/2-1]这k/2个数，那么第k大也就变成了，第k-k/2个大的数了。

        如果 A[k/2-1]>B[k/2-1]，就做之前对称的操作就好。
        */
        
        int m = nums1.length;
        int n = nums2.length;
        int total = m + n;
        
        if (total % 2 != 0) { // exact one or algrebic divied by two 
            return (double) findKth(nums1, 0, m - 1, nums2, 0, n - 1, total / 2 + 1);// kth, but the index is k-1
        } else {
            double x = (double) findKth(nums1, 0, m - 1, nums2, 0, n - 1, total / 2);
            double y = (double) findKth(nums1, 0, m - 1, nums2, 0, n - 1, total / 2 + 1);
            return (double)(x + y) / 2;
        }
    }
    public static int findKth(int[] nums1, int aStart, int aEnd, int[] nums2, int bStart, int bEnd, int k) {
            
            int m = aEnd - aStart + 1;
            int n = bEnd - bStart + 1;
            
            if (m > n) {
                return findKth(nums2, bStart, bEnd, nums1, aStart, aEnd, k);
            }
            
            if (m == 0) {
                return nums2[k - 1];
            }
        
            if (k == 1) {
                return Math.min(nums1[aStart], nums2[bStart]);
            }
            
            //----------------------------------------
            
            int partA = Math.min(k/2,m); // half length of total length
            int partB = k - partA; // 
            
            // Recursion to 
            if (nums1[aStart + partA - 1] < nums2[bStart + partB - 1]) {
                return findKth(nums1, aStart+partA, aEnd, nums2, bStart, bEnd, k - partA);
            } else if (nums1[aStart + partA - 1] > nums2[bStart + partB - 1]) {
                return findKth(nums1, aStart, aEnd, nums2, bStart + partB, bEnd, k - partB);
            } else {
                return nums1[aStart + partA - 1];
            }
        }
}
```

2. 使用二分搜索的办法，推荐这个办法，寻找nums1 nums2两个数组的划分，同时满足nums1中左半边的最右边的数 &lt;= nums2中右半边的最左边的数 + nums2中左半边的最右边的数 &lt;= nums1中右半边的最左边的数。

```java
class Solution {
    /**
    * 二分搜索，log(min(m, n))
    */
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        //if nums1 length is greater than switch them so that 
        // nums1 is always smaller than nums2, 方便复用代码
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays(nums2, nums1);
        }
        int x = nums1.length;
        int y = nums2.length;

        int low = 0;
        int high = x;
        while (low <= high) {
            // 把两个数组分别划分
            int partitionX = (low + high) / 2; // nums1的中间
            int partitionY = (x + y + 1) / 2 - partitionX; // nums2的中间

            //if partitionX is 0 it means nothing is there on left side. Use -INF for maxLeftX
            //if partitionX is length of input then there is nothing on right side. Use +INF for minRightX
            int maxLeftX = (partitionX == 0) ? Integer.MIN_VALUE : nums1[partitionX - 1];
            int minRightX = (partitionX == x) ? Integer.MAX_VALUE : nums1[partitionX];

            int maxLeftY = (partitionY == 0) ? Integer.MIN_VALUE : nums2[partitionY - 1];
            int minRightY = (partitionY == y) ? Integer.MAX_VALUE : nums2[partitionY];

            if (maxLeftX <= minRightY && maxLeftY <= minRightX) {
                //We have partitioned array at correct place
                // Now get max of left elements and min of right elements to get the median in case of even length combined array size
                // or get max of left for odd length combined array size.
                if ((x + y) % 2 == 0) {
                    return ((double)Math.max(maxLeftX, maxLeftY) + Math.min(minRightX, minRightY))/2;
                } else {
                    return (double)Math.max(maxLeftX, maxLeftY);
                }
            } else if (maxLeftX > minRightY) { //we are too far on right side for partitionX. Go on left side.
                high = partitionX - 1;
            } else { //we are too far on left side for partitionX. Go on right side.
                low = partitionX + 1;
            }
        }

        //Only we we can come here is if input arrays were not sorted. Throw in that scenario.
        throw new IllegalArgumentException();
    }
}
```

3. O（m + n）解法

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        /**
        * 找到两个数组中的中位数，然后
        */
        int index1 = 0; // nums1中的索引
        int index2 = 0; // nums2中的索引
        int med1 = 0;
        int med2 = 0;
        for (int i = 0; i <= (nums1.length+nums2.length) / 2; i++) {
            med1 = med2;
            if (index1 == nums1.length) { // nums1中元素遍历完
                med2 = nums2[index2];
                index2++;
            } else if (index2 == nums2.length) { // nums2中元素遍历完
                med2 = nums1[index1];
                index1++;
            } else if (nums1[index1] < nums2[index2] ) {
                med2 = nums1[index1];
                index1++;
            }  else {
                med2 = nums2[index2];
                index2++;
            }
        }

        // the median is the average of two numbers，判断一下奇偶
        if ((nums1.length + nums2.length) % 2 == 0) {
            return (double)(med1 + med2) / 2;
        }

        return (double)med2;
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
        int zeroIndex = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != 0) {
                if (i != zeroIndex) { // 优化下，相同元素不用交换
                    nums[i] = nums[i] ^ nums[zeroIndex];
                    nums[zeroIndex] = nums[i] ^ nums[zeroIndex];
                    nums[i] = nums[i] ^ nums[zeroIndex];
                }
                zeroIndex++;
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

##  **59 Spiral Matrix II** <a id="59-spiral-matrix-ii"></a>

### **原题概述** <a id="yuan-ti-gai-shu-29"></a>

Given a positive integer _n_, generate a square matrix filled with elements from 1 to _n_2 in spiral order.

**Example:**

```text
Input: 3
Output:
[
 [ 1, 2, 3 ],
 [ 8, 9, 4 ],
 [ 7, 6, 5 ]
]
```

### **题意和分析** <a id="ti-yi-he-fen-xi-29"></a>

这道题根据打印顺序来创建二维数组，和54 Spiral Matrix一样的思路。

### **代码** <a id="dai-ma-29"></a>

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int[][] result = new int[n][n];
        if (n <= 0) {
            return result;
        }
        int rowBegin = 0;
        int rowEnd = n - 1;
        int colBegin = 0;
        int colEnd = n - 1;
        int index = 1;
        while (rowBegin <= rowEnd && colBegin <= colEnd) {
            //从左到右
            for (int i = colBegin; i<= colEnd; i++) {
                result[rowBegin][i] = index;
                index++;
            }
            rowBegin++;

            //从上到下
            for (int i = rowBegin; i <= rowEnd; i++) {
                result[i][colEnd] = index;
                index++;
            }
            colEnd--;

            //从右到左
            if (colEnd >= colBegin) {
                for (int i = colEnd; i >= colBegin; i--) {
                    result[rowEnd][i] = index;
                    index++;
                }
            }
            rowEnd--;

            //从下到上
            if (rowEnd >= rowBegin) {
                for (int i = rowEnd; i >= rowBegin; i--) {
                    result[i][colBegin] = index;
                    index++;
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
            if (candidate1 == num) {
                count1++;
            } else if (candidate2 == num) { //else if 表示candidate1和candidate2不会是一个数
                count2++;
            }
        }
      if (count1 > nums.length/3) result.add(candidate1);
      if (count2 > nums.length/3) result.add(candidate2);

      return result;
   }
}
```

## **228 Summary Ranges** 

### **原题概述**

Given a sorted integer array without duplicates, return the summary of its ranges.

**Example 1:**

```text
Input:  [0,1,2,4,5,7]
Output: ["0->2","4->5","7"]
Explanation: 0,1,2 form a continuous range; 4,5 form a continuous range.
```

**Example 2:**

```text
Input:  [0,2,3,4,6,8,9]
Output: ["0","2->4","6","8->9"]
Explanation: 2,3,4 form a continuous range; 8,9 form a continuous range.
```

### **题意和分析**

这道题的思路比较简单，从左边找到右边即可，注意判断一下如果是连续的元素就用"-&gt;"，否则就是元素自己，注意下边界，在for循环中，注意每次i在循环中是一段连续数字移动到终止地方的位置，同时每次for循环i还会自增1。

### **代码**

```java
class Solution {
   public List<String> summaryRanges(int[] nums) {
      List<String> result = new ArrayList<>();
      int len = nums.length;
      for (int i = 0; i < len; i++) {
         int temp = nums[i];
         while (i + 1 < len && nums[i + 1] - nums[i] == 1) {//是连续元素
            i++;
         }
         if (nums[i] != temp) {//多于一个元素
            result.add(temp + "->" + nums[i]);
         } else {//只有当前元素自己
            result.add(nums[i] + "");
         }
      }
      return result;
   }
}
```

## **31 Next Permutation** 

### **原题概述**

Implement **next permutation**, which rearranges numbers into the lexicographically next greater permutation of numbers.

If such arrangement is not possible, it must rearrange it as the lowest possible order \(ie, sorted in ascending order\).

The replacement must be [**in-place**](http://en.wikipedia.org/wiki/In-place_algorithm) and use only constant extra memory.

Here are some examples. Inputs are in the left-hand column and its corresponding outputs are in the right-hand column.

`1,2,3` → `1,3,2`  
`3,2,1` → `1,2,3`  
`1,1,5` → `1,5,1`

### **题意和分析**

排列Arrangement是从N个不同元素中取出M个元素，当N == M时，就是全排列Permutation。如果能够知道下一个排列Next Permutation是什么，也就知道了全排列是什么。全排列的问题在算法上没有特别的，但是要理清思路，还得刻意练习才行。

假如排列是{2,3,6,5,4,1}，求下一个排列的基本步骤是这样：  
1\) 先从后往前看，找到第一个不是依次增长的数，记录下位置p。比如现在的例子就应该是3，对应的位置是p==1；现在在p位置的数字跟从后向前的数字进行比较_，_找到第一个比p位置的数大的数，位置为q，然后p，q两个调换位置，比如例子中q位置的4。把3和4调换位置后得到{2,4,6,5,3,1}。最后把p位置之后的所有数字倒序，得到{2,4,1,3,5,6}，即是要求的下一个排列；  
2\) 如果从后向前看的时候，上面的数字都是依次增长的，那么说明这是最后一个排列，下一个就是第一个，把所有数字翻转过来即可\(比如{6,5,4,3,2,1}下一个是{1,2,3,4,5,6}\)；

最坏情况需要扫描数组三次，所以时间复杂度是O\(3\*n\)=O\(n\)，空间复杂度是O\(1\)。

### **代码**

```java
class Solution {
    public void nextPermutation(int[] nums) {
        if (nums == null || nums.length == 0) {
            return;
        }
        int len = nums.length, p = len - 2, q = len - 1;
        
        // 1. 从后向前找到一个非增长的元素，等于的话也继续向前
        while (p >= 0 && nums[p] >= nums[p + 1]) {
            p--;
        }
        //全逆序的数组不会进入这个判断，全逆序p的位置为-1
        // 2. 从后向前找到第一个比p位置元素大的元素，注意这个数字肯定有，等于的话继续向前
        if (p >= 0) {
            while (nums[q] <= nums[p]) {
                q--;
            }
            swap(nums, p, q);
        }
        // 3. p位置后面的数组元素进行翻转
        reverse(nums, p + 1, len - 1);
    }
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
    private void reverse(int[] nums, int left, int right) {
        while (left < right) {
            swap(nums, left, right);
            left++;
            right--;
        }
    }
}
```

## **448 Find All Numbers Disappeared in an Array** 

### **原题概述**

Given an array of integers where 1 ≤ a\[i\] ≤ n \(n = size of array\), some elements appear twice and others appear once.

Find all the elements of \[1, n\] inclusive that do not appear in this array.

Could you do it without extra space and in O\(n\) runtime? You may assume the returned list does not count as extra space.

**Example:**

```text
Input:
[4,3,2,7,8,2,3,1]

Output:
[5,6]
```

### **题意和分析**

 给一个数组，数组元素的范围在1 ≤ a\[i\] ≤ n \(n = size of array\)，找到所有数组中缺少的数，要求O\(1\)的空间和O\(n\)的时间复杂度，所以只能在原来的数组上操作了，也不能排序了，有很多种办法可以做：

1） 第一遍循环，对于每个数字nums\[i\]，我们认为nums\[nums\[i\] - 1\]是其对应的数，如果这个对应的数是正数，将其赋值为负数，如果已经是负数，就不变；可以想象，如果某些数1 ≤ a\[i\] ≤ n 缺失了，那么它们对应的索引处的整数就不会被改变成负数，然后第二遍循环我们就找出剩余的正数，返回这些正数所对应的索引值，就是原本数组中缺少的数字；

2）第一遍循环，将nums\[i\]交换到其对应的位置索引nums\[nums\[i\]-1\]上去，比如题目给的例子例子，如果没有缺失的数你那正确的顺序应该是\[1, 2, 3, 4, 5, 6, 7, 8\]，现在是\[4, 3 ,2 ,7 ,8 ,2 ,3 ,1\]，于是把数字移动到正确的位置上去，比如第一个4（nums\[0\]）就应该和7（nums\[4-1\] = nums\[3\]）逐个交换个位置，有点选择排序的思想，最后得到的顺序是\[1, 2, 3, 4, 3, 2, 7, 8\]，最后在对应位置检查，如果nums\[i\]和i+1不等，那么将i+1存入结果中即可;

3）对于\[4, 3 ,2 ,7 ,8 ,2 ,3 ,1\]，在nums\[nums\[i\]-1\]位置累加数组长度len，\(nums\[i\]-1有可能越界，所以需要对len取余\)，最后要找出缺失的数只需要看nums\[i\]的值是否小于等于len即可，最后遍历完nums\[i\]数组为\[12, 19, 18, 15, 8, 2, 11, 9\]，我们发现有两个数字8和2小于等于len，那么就可以通过i+1来得到正确的结果5和6了;

### **代码**

正负数来标记

```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        List<Integer> result = new ArrayList();
        if (nums == null || nums.length == 0) {
            return result;
        }
        int len = nums.length;

        //先把元素对应的索引处改为负数
        for (int i = 0; i < len; i++) {
            int index = Math.abs(nums[i]) - 1;
            nums[index] = (nums[index] > 0) ? -nums[index] : nums[index];
        }

        //找到目前剩余的正数，返回索引值
        for (int i = 0; i < len; i++) {
            if (nums[i] > 0) {
                result.add(i + 1);
            }
        }
        return result;
    }
}
```

交换索引和数字

```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        List<Integer> result = new ArrayList();
        if (nums == null || nums.length == 0) {
            return result;
        }
        int len = nums.length;

        for (int i = 0; i < len; i++) {
            if (nums[i] != nums[nums[i] - 1]) {//相等则说明刚才已经交换过了
                swap (nums, i, nums[i] - 1);//将i位置的元素和nums[i] - 1处的元素进行交换，放在合适的位置
                i--;//交换后需要在当前位置停留一下检查当前元素的交换位置
            }
        }

        for (int i = 0; i < len; i++) {
            if (nums[i] != i + 1) {
                result.add(i + 1);
            }
        }
        return result;
    }
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

累加数组长度

```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        List<Integer> result = new ArrayList();
        if (nums == null || nums.length == 0) {
            return result;
        }
        int len = nums.length;

        for (int i = 0; i < len; i++) {
            nums[(nums[i] - 1) % len] += len;
        }

        for (int i = 0; i < len; i++) {
            if (nums[i] <= len) {
                result.add(i + 1);
            }
        }
        return result;
    }
}
```

## **287 Find the Duplicate Number** 

### **原题概述**

Given an array nums containing n + 1 integers where each integer is between 1 and n \(inclusive\), prove that at least one duplicate number must exist. Assume that there is only one duplicate number, find the duplicate one.

**Example 1:**

```text
Input: [1,3,4,2,2]
Output: 2
```

**Example 2:**

```text
Input: [3,1,3,4,2]
Output: 3
```

**Note:**

1. You **must not** modify the array \(assume the array is read only\).
2. You must use only constant, O\(1\) extra space.
3. Your runtime complexity should be less than _O_\(_n_2\).
4. There is only one duplicate number in the array, but it could be repeated more than once.

### **题意和分析**

n+1长度的数组，找出唯一的那个有重复的数字，可能重复多次，不能修改数组并要求常量空间和小于平方级线型时间复杂度（不能用额外空间来统计出现的次数，不能排序，也不能套两个循环来暴力破解）。

1）二分查找，利用数组的元素的值在区间\[1, n\]的特点进行搜索，首先求出中间的索引mid，然后遍历整个数组，统计所有小于等于索引mid的元素的个数，如果元素个数大于mid索引，则说明重复值在\[mid+1, n\]这些索引之间，因为“较小的数比较多”，反之，重复值应在\[1, mid-1\]之间（“较大的数比较多”），然后依次类推，直到搜索完成，此时的low就是我们要求的重复值；

2）双指针，数组元素的范围是\[1, n\]，利用数组元素和坐标的转换来形成一个闭环，利用快慢指针找到重复的值，这个做法如同142题带环链表一样，第二次快慢指针相遇的时候即为重复的元素。

```java
// [1,3,4,2,2]
/**
 * 按照index来形成带环链表，从index = 0开始，将该index所在的value作为下一个结点的index，以此来走到下一步；因为有n+1个元素，范围是1~n，所以不会越界
 (index=0)1(index=1) -> (index=1)3(index=3) -> (index=3)2(index=2) -> (index=2)4(index=4)
                                                        ^                      |
                                                        |......................|
 */
```

参考[这里](http://bookshadow.com/weblog/2015/09/28/leetcode-find-duplicate-number/)[这里](https://leetcode.com/problems/find-the-duplicate-number/discuss/72845/Java-O%28n%29-time-and-O%281%29-space-solution.-Similar-to-find-loop-in-linkedlist.)。

### **代码**

二分查找

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int low = 1; //数字从1到n，因此index/value至少为1
        int high = nums.length - 1; //数字从1到n，n+1个数字，最大值（index）为n
        while (low <= high) {
            int mid = low + (high - low) / 2;
            int count = 0;
            for (int num : nums) {
                if (num <= mid) { //如果等于mid，说明index和value在左边都是1：1，重复值依然在[1, mid-1]之间（“较大的数多一个”）
                    count++;
                }
            }
            if (count <= mid) { //同上
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return low;
    }
}
```

快慢指针

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = 0;
        int fast = 0;
        
        //快指针走两步，慢指针走一步
        while (true) {
            slow = nums[slow];
            fast = nums[nums[fast]];
            if (slow == fast) {
                break;
            }
        }
        fast = 0;
        // 快慢指针都走一步
        while (fast != slow) {
            fast = nums[fast];
            slow = nums[slow];
        }
        return slow; //return fast;
    }
}
```

## **189 Rotate Array** 

### **原题概述**

Given an array, rotate the array to the right by _k_ steps, where _k_ is non-negative.

**Example 1:**

```text
Input: [1,2,3,4,5,6,7] and k = 3
Output: [5,6,7,1,2,3,4]
Explanation:
rotate 1 steps to the right: [7,1,2,3,4,5,6]
rotate 2 steps to the right: [6,7,1,2,3,4,5]
rotate 3 steps to the right: [5,6,7,1,2,3,4]
```

**Example 2:**

```text
Input: [-1,-100,3,99] and k = 2
Output: [3,99,-1,-100]
Explanation: 
rotate 1 steps to the right: [99,-1,-100,3]
rotate 2 steps to the right: [3,99,-1,-100]
```

### **题意和分析**

翻转数组右边部分k个元素，如果可以用辅助数组，就直接  \(i+k\)%n来映射就行了，但是要求空间为常量，并且至少有三种方法可以来做：1）用多个辅助变量来记录数组元素；2） 先翻转整个数组，再翻转前k个数字，最后翻转后len - k个数字；3）不停交换两个数字，达到翻转的目的。

### **代码**

这里实现第二种解法

```java
class Solution {
    public void rotate(int[] nums, int k) {
        if (nums == null || nums.length == 0) {
            return;
        }

        k %= nums.length;//k有可能比数组长度长

        int len = nums.length;

        reverse(nums, 0, len - 1);//全部数组翻转一遍
        reverse(nums, 0, k - 1);//翻转前k个数组元素，翻转的目标范围
        reverse(nums, k, len - 1);//翻转后面len - k个元素
    }
    private void reverse(int[] nums, int left, int right) {
        while (left < right) {
            int temp = nums[left];
            nums[left] = nums[right];
            nums[right] = temp;
            left++;
            right--;
        }
    }
}
```

## **73 Set Matrix Zeroes**

### **原题概述**

Given a _m_ x _n_ matrix, if an element is 0, set its entire row and column to 0. Do it [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm).

**Example 1:**

```text
Input: 
[
  [1,1,1],
  [1,0,1],
  [1,1,1]
]
Output: 
[
  [1,0,1],
  [0,0,0],
  [1,0,1]
]
```

**Example 2:**

```text
Input: 
[
  [0,1,2,0],
  [3,4,5,2],
  [1,3,1,5]
]
Output: 
[
  [0,0,0,0],
  [0,4,5,0],
  [0,3,1,0]
]
```

**Follow up:**

* A straight forward solution using O\(_mn_\) space is probably a bad idea.
* A simple improvement uses O\(_m_ + _n_\) space, but still not the best solution.
* Could you devise a constant space solution?

### **题意和分析**

给一个矩阵，如果某个元素为0，就将该元素所在的行和列上的元素都改为0，额外空间O\(_mn_\)的解法的当然是复制一下矩阵，然后暴力按行扫，只要有0就把该行全部变成0，然后再按照列来扫，如果该列有0则把列上所有元素变成0；空间复杂度为O\(_m_ + _n_\)的做法则是，创建两个一维数组，一个用来记录哪些行有0，另一个用来记录哪些列有0，然后分别修改行和列的元素；

要求是用O\(1\)的空间，所以可以用原数组的第一行第一列来记录各行各列是否有0：

1）先扫描第一行第一列，如果有0，则将各自的flag设置为true；  
2）然后扫描除去第一行第一列的整个数组，如果有0，则将对应的第一行和第一列的数字赋0；  
3）再次遍历除去第一行第一列的整个数组，如果对应的第一行和第一列的数字有一个为0，则将当前值赋0；  
4） 最后根据第一行第一列的flag情况来更新第一行和第一列的元素；

### **代码**

```java
class Solution {
    public void setZeroes(int[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return;
        }

        //记录第一行和第一列是否有0的flag
        boolean rowZero = false, colZero = false;

        int m = matrix.length, n = matrix[0].length;

        //首先扫描第一行和第一列
        for (int i = 0; i < n; i++) {
            if (matrix[0][i] == 0) {
                rowZero = true;
                break;
            }
        }
        for (int j = 0; j < m; j++) {
            if (matrix[j][0] == 0) {
                colZero = true;
                break;
            }
        }

        //遍历除第一行和第一列的行列，把是否有0的结果分别记录在第一行和第一列
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][j] == 0) {
                    matrix[0][j] = 0;
                    matrix[i][0] = 0;
                }
            }
        }

        //遍历除第一行和第一列的行列，如果第一行和第一列有0，则将该行列均改为0
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[0][j] == 0 || matrix[i][0] == 0) {
                    matrix[i][j] = 0;
                }
            }
        }

        //最后修改第一行和第一列的元素
        if (rowZero) {
            for (int i = 0; i < n; i++) {
                matrix[0][i] = 0;
            }
        }
        if (colZero) {
            for (int i = 0; i < m; i++) {
                matrix[i][0] = 0;
            }
        }
    }
}
```

## **289 Game of Life** 

### **原题概述**

According to the [Wikipedia's article](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life): "The **Game of Life**, also known simply as **Life**, is a cellular automaton devised by the British mathematician John Horton Conway in 1970."

Given a board with m by n cells, each cell has an initial state live \(1\) or dead \(0\). Each cell interacts with its [eight neighbors](https://en.wikipedia.org/wiki/Moore_neighborhood) \(horizontal, vertical, diagonal\) using the following four rules \(taken from the above Wikipedia article\):

1. Any live cell with fewer than two live neighbors dies, as if caused by under-population.
2. Any live cell with two or three live neighbors lives on to the next generation.
3. Any live cell with more than three live neighbors dies, as if by over-population..
4. Any dead cell with exactly three live neighbors becomes a live cell, as if by reproduction.

Write a function to compute the next state \(after one update\) of the board given its current state. The next state is created by applying the above rules simultaneously to every cell in the current state, where births and deaths occur simultaneously.

**Example:**

```text
Input: 
[
  [0,1,0],
  [0,0,1],
  [1,1,1],
  [0,0,0]
]
Output: 
[
  [0,0,0],
  [1,0,1],
  [0,1,1],
  [0,1,0]
]
```

**Follow up**:

1. Could you solve it in-place? Remember that the board needs to be updated at the same time: You cannot update some cells first and then use their updated values to update other cells.
2. In this question, we represent the board using a 2D array. In principle, the board is infinite, which would cause problems when the active area encroaches the border of the array. How would you address these problems?

### **题意和分析**

参考[这里](http://www.cnblogs.com/grandyang/p/4854466.html)和[这里](https://segmentfault.com/a/1190000003819277)，用二维数组来表示细胞，1代表活细胞，0代表死细胞按照题意，每个细胞满足以下条件：

1. 如果活细胞周围八个位置的活细胞数少于两个，则该位置活细胞死亡

2. 如果活细胞周围八个位置有两个或三个活细胞，则该位置活细胞仍然存活

3. 如果活细胞周围八个位置有超过三个活细胞，则该位置活细胞死亡

4. 如果死细胞周围正好有三个活细胞，则该位置死细胞复活

要求计算给定的二维数组的下一个状态，在in-place位置更新，所以就不能新建一个相同大小的数组，只能更新原有数组，但是题目中要求所有的位置必须被同时更新，不能分批更新，但是在循环程序中我们还是一个位置一个位置更新的，那么当一个位置更新了，这个位置成为其他位置的neighbor时，我们怎么知道其未更新的状态呢，我们可以使用状态机转换：

状态0： 死细胞转为死细胞

状态1： 活细胞转为活细胞

状态2： 活细胞转为死细胞

状态3： 死细胞转为活细胞

对所有状态对2取余，那么状态0和2就变成死细胞，状态1和3就是活细胞。因此先对原数组进行逐个扫描，对于每一个位置，扫描其周围八个位置，如果遇到状态1或2，就计数器累加1，扫完8个邻居，如果少于两个活细胞或者大于三个活细胞，而且当前位置是活细胞的话，标记状态2，而如果有三个活细胞且当前是死细胞的话，标记状态3。完成一遍扫描后再对数据扫描一遍，对2取余。

### **代码**

\*\*\*\*[**https://leetcode.com/problems/game-of-life/discuss/73223/Easiest-JAVA-solution-with-explanation**](https://leetcode.com/problems/game-of-life/discuss/73223/Easiest-JAVA-solution-with-explanation)\*\*\*\*

```java
class Solution {
    final int[][] dirs = {{-1, -1}, {-1, 0}, {-1, 1}, {0, -1}, {0, 1}, {1, -1}, {1, 0}, {1, 1}}; 
    public void gameOfLife(int[][] board) {
        // live: 1
        // death: 0
        // death -> live: -1
        // live -> death: 2
        // two passes, the first pass changes all cell's state for memorization, the second pass change back
        if (board == null || board.length == 0) {
            return;
        }
        int rows = board.length;
        int cols = board[0].length;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (board[i][j] == 1) {
                    int livesNearby = calLives(board, i, j);
                    if (livesNearby < 2 || livesNearby > 3) {
                        board[i][j] = 2;
                    }
                } 
                if (board[i][j] == 0) {
                    int livesNearby = calLives(board, i, j);
                    if (livesNearby == 3) {
                        board[i][j] = -1;
                    }
                }
            }
        }
        update(board);
    }
    private int calLives(int[][] board, int row, int col) {
        int livesNearbyCell = 0;
        for (int[] dir : dirs) {
            int checkingRow = row + dir[0];
            int checkingCol = col + dir[1];
            if (checkingRow >= 0 &&
                checkingRow < board.length &&
                checkingCol >= 0 &&
                checkingCol < board[0].length &&
                (board[checkingRow][checkingCol] == 1 || board[checkingRow][checkingCol] == 2)) {
                    livesNearbyCell++;
                }
        }
        return livesNearbyCell;
    }

    private void update(int[][] board) {
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if (board[i][j] == -1) {
                    board[i][j] = 1;
                } else if (board[i][j] == 2) {
                    board[i][j] = 0;
                }
            }
        }
    }
}
```

## **695 Max Area of Island** 

### **原题概述**

Given a non-empty 2D array `grid` of 0's and 1's, an **island** is a group of `1`'s \(representing land\) connected 4-directionally \(horizontal or vertical.\) You may assume all four edges of the grid are surrounded by water.

Find the maximum area of an island in the given 2D array. \(If there is no island, the maximum area is 0.\)

**Example 1:**  


```text
[[0,0,1,0,0,0,0,1,0,0,0,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,1,1,0,1,0,0,0,0,0,0,0,0],
 [0,1,0,0,1,1,0,0,1,0,1,0,0],
 [0,1,0,0,1,1,0,0,1,1,1,0,0],
 [0,0,0,0,0,0,0,0,0,0,1,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,0,0,0,0,0,0,1,1,0,0,0,0]]
```

Given the above grid, return `6`. Note the answer is not 11, because the island must be connected 4-directionally.

**Example 2:**  


```text
[[0,0,0,0,0,0,0,0]]
```

Given the above grid, return `0`.

**Note:** The length of each dimension in the given `grid` does not exceed 50.

### **题意和分析**

典型的DFS，对每个元素，如果是1的话就前后左右调用递归递归，然后比较每个元素的最大值；如果想避免修改原本的数组，可以另外新建一个同等大小的二维数组来记录是否访问过，由于要同时记录多个元素是否被访问过，因此不能像79 Word Search那样优化空间。

### **代码**

```java
class Solution {
    public int maxAreaOfIsland(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }
        int m = grid.length, n = grid[0].length;
        int maxArea = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {//是岛屿的时候才计算
                    maxArea = Math.max(maxArea, dfs(grid, i, j));
                }
            }
        }
        return maxArea;
    }

    private int dfs(int[][] grid, int i, int j) {
        int m = grid.length, n = grid[0].length;
        if (i >= 0 && i < m && j >= 0 && j < n && grid[i][j] == 1) {
            grid[i][j] = 0;//这一步很重要，设为0是为了防止递归过程中对当前元素的重复调用
            //本身的面积+四个方向可能的面积
            return 1 + dfs(grid, i + 1, j) + dfs(grid, i - 1, j) + dfs(grid, i, j + 1) + dfs(grid, i, j - 1);
        }
        return 0;
    }
}
```

不改变原本的二维数组

```java
class Solution {
    public int maxAreaOfIsland(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }

        int m = grid.length, n = grid[0].length;
        int maxArea = 0;

        boolean[][] visited = new boolean[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                visited[i][j] = false;
            }
        }

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {//是岛屿的时候才计算
                    maxArea = Math.max(maxArea, dfs(grid, i, j, visited));
                    visited[i][j] = false;//重设刚才的元素为下一轮检查做准备
                }
            }
        }
        return maxArea;
    }

    private int dfs(int[][] grid, int i, int j, boolean[][] visited) {
        int m = grid.length, n = grid[0].length;
        if (i >= 0 && i < m && j >= 0 && j < n && grid[i][j] == 1 && !visited[i][j]) {
            visited[i][j] = true;
            //本身的面积+四个方向可能的面积
            return 1 + dfs(grid, i + 1, j, visited) + dfs(grid, i - 1, j, visited) + dfs(grid, i, j + 1, visited) + dfs(grid, i, j - 1, visited);
        }
        return 0;
    }
}
```

##  **697 Degree of an Array** 

### **原题概述**

\*\*\*\*

Given a non-empty array of non-negative integers `nums`, the **degree** of this array is defined as the maximum frequency of any one of its elements.

Your task is to find the smallest possible length of a \(contiguous\) subarray of `nums`, that has the same degree as `nums`.

**Example 1:**  


```text
Input: [1, 2, 2, 3, 1]
Output: 2
Explanation: 
The input array has a degree of 2 because both elements 1 and 2 appear twice.
Of the subarrays that have the same degree:
[1, 2, 2, 3, 1], [1, 2, 2, 3], [2, 2, 3, 1], [1, 2, 2], [2, 2, 3], [2, 2]
The shortest length is 2. So return 2.
```

**Example 2:**  


```text
Input: [1,2,2,3,1,4,2]
Output: 6
```

### **题意和分析**

给一个整数数组，规定degree是这个数组的一个或者多个出现次数最多的元素，求出degree一样的最短子数组的长度。比较直观的做法是先用一个HashMap来统计哪些元素属于degree，同时用另外一个HashMap来统计每个元素的初始出现位置和最后出现位置（第一次出现就更新起始位置，接下来只更新结束位置），这样一次遍历后就知道了数组的degree有哪些元素和每个元素的的起始终止位置，把起始和终止位置相减然后+1，就是拥有同样degree的子数组的长度了；由于degree的元素可能有多个，因此根据degree的数值需要遍历所有的degree元素找到最小的子字符串长度。

另外起始可以把两个HashMap合成一个，key是元素，value是数组分别包含三个元素 - 出现次数，起始位置和终止位置。

### **代码**

```java
class Solution {
    public int findShortestSubArray(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int degree = 0, result = Integer.MAX_VALUE;
        //存元素和出现的次数
        HashMap<Integer, int[]> map = new HashMap<>();

        for (int i = 0; i < nums.length; i++) {
            if (!map.containsKey(nums[i])) {//第一次出现，更新出现次数，和初始结束位置
                map.put(nums[i], new int[]{1, i, i});
            } else {//之前已经出现过，更新出现次数和结束位置
                int[] temp = map.get(nums[i]);
                temp[0]++;
                temp[2] = i;
            }
        }

        for (int[] value : map.values()) {
            if (value[0] > degree) {//更新degree
                degree = value[0];
                result = value[2] - value[1] + 1;//计算当前元素的起始终止位置
            } else if (value[0] == degree) {
                result = Math.min(result, value[2] - value[1] + 1);//找到最小的长度
            }
        }
        return result;
    }
}
```

## **442 Find All Duplicates in an Array** 

### **原题概述**

Given an array of integers, 1 ≤ a\[i\] ≤ n \(n = size of array\), some elements appear **twice** and others appear **once**.

Find all the elements that appear **twice** in this array.

Could you do it without extra space and in O\(n\) runtime?

**Example:**  


```text
Input:
[4,3,2,7,8,2,3,1]

Output:
[2,3]
```

### **题意和分析**

给一个数组，里面的元素1 ≤ a\[i\] ≤ n，里面有的元素出现一次有的出现两次，在常数空间和线性时间复杂度找出所有的出现两次的元素。这道题的三种解法跟 448 Find All Numbers Disappeared in an Array一模一样，这里就用正负值的办法， 对于每个nums\[i\]，我们将其对应的nums\[nums\[i\] - 1\]取相反数，如果其已经是负数了，说明之前存在过，那么说明该数出现了两次，我们将其加入结果result中即可。

### **代码**

```java
class Solution {
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> result = new ArrayList<>();
        if (nums == null || nums.length == 0) {
            return result;
        }
        for (int i = 0; i < nums.length; i++) {
            int index = Math.abs(nums[i]) - 1;//绝对值才是正确的索引值
            if (nums[index] < 0) {
                result.add(Math.abs(index+1));//将相应的索引加入到结果
            }
            nums[index] = -nums[index];
        }
        return result;
    }
}
```

## **717 1-bit and 2-bit Characters**

### **原题概述**

### **题意和分析**

说有两种特殊的字符，一种是两位字符，只能是二进制的11和10，另一种是单个位字符，只能是二进制的0。现在给了我们一个只包含0和1的数组，最后一个元素始终是0，问我们能否将其正确的分割，使得最后一个字符是单个位字符。

按照 这个解法 （[https://leetcode.com/problems/1-bit-and-2-bit-characters/discuss/108967/JAVA-check-only-the-end-of-array](https://leetcode.com/problems/1-bit-and-2-bit-characters/discuss/108967/JAVA-check-only-the-end-of-array)），只检查最后一位或者两位：

1）如果数组里面只有一位，那么返回true，因为最后一位必然是0；

2）如果数组最后两位都是0，返回true；

3）如果最后是10这样的情况，这取决于前面1的数量，如果是奇数个1，返回false，因为有个1不能配对；如果是偶数个1，返回true；

### **代码**

```java
class Solution {
    public boolean isOneBitCharacter(int[] bits) {
        int ones = 0;

        for (int i = bits.length - 2; i >= 0 && bits[i] != 0; i--) {//最后一位不用检查
            ones++;
        }
        if (ones % 2 != 0) {
            return false;
        }

        return true;
    }
}
```

## **628 Maximum Product of Three Numbers**

### **原题概述**

Given an integer array, find three numbers whose product is maximum and output the maximum product.

**Example 1:**  


```text
Input: [1,2,3]
Output: 6
```

**Example 2:**  


```text
Input: [1,2,3,4]
Output: 24
```

**Note:**  


1. The length of the given array will be in range \[3,104\] and all elements are in the range \[-1000, 1000\].
2. Multiplication of any three numbers in the input won't exceed the range of 32-bit signed integer.

### **题意和分析**

这道题是在数组中找到三个数相乘的乘积为最大，限制了条件，三个数的乘积不会超过Integer的范围，这道题需要考虑的地方就是负数，1）如果全是正数，那没什么好说的直接最大的三个数相乘即可，如果数组里面全是负数，那么三个数相乘就应该是绝对值最小的三个数，也是三个最大的数相乘；2）如果有正数有负数，那么就应该是最大的正数和最小的两个负数相乘；注意想想这道题可将0划到正数那一伙并不会影响结果，所以可以先排序在分别算出来两种情况的乘积然后比较即可；如果不想排序，那么就先循环一遍找到三个最大的数，和两个最小的数，最后比较看二选一哪种情况大，返回即可。

### **代码**

```java
class Solution {
    public int maximumProduct(int[] nums) {
        if (nums.length < 3) {
            return 0;
        }
        int max1 = Integer.MIN_VALUE, max2 = Integer.MIN_VALUE, max3 = Integer.MIN_VALUE;
        int min1 = Integer.MAX_VALUE, min2 = Integer.MAX_VALUE;
        for (int num : nums) {
            if (num > max1) {
                max3 = max2;
                max2 = max1;
                max1 = num;
            } else if (num > max2) {
                max3 = max2;
                max2 = num;
            } else if (num > max3) {
                max3 = num;
            }

            if (num < min1) {
                min2 = min1;
                min1 = num;
            } else if (num < min2) {
                min2 = num;
            }
        }
        return Math.max(max1 * max2 * max3, max1 * min1 * min2);
    }
}
```

## 1013 Partition Array Into Three Parts With Equal Sum

### 题目

Given an array `A` of integers, return `true` if and only if we can partition the array into three **non-empty** parts with equal sums.

Formally, we can partition the array if we can find indexes `i+1 < j` with `(A[0] + A[1] + ... + A[i] == A[i+1] + A[i+2] + ... + A[j-1] == A[j] + A[j-1] + ... + A[A.length - 1])`

**Example 1:**

```text
Input: A = [0,2,1,-6,6,-7,9,1,2,0,1]
Output: true
Explanation: 0 + 2 + 1 = -6 + 6 - 7 + 9 + 1 = 2 + 0 + 1
```

**Example 2:**

```text
Input: A = [0,2,1,-6,6,7,9,-1,2,0,1]
Output: false
```

**Example 3:**

```text
Input: A = [3,3,6,5,-2,2,5,1,-9,4]
Output: true
Explanation: 3 + 3 = 6 = 5 - 2 + 2 + 5 + 1 - 9 + 4
```

**Constraints:**

* `3 <= A.length <= 50000`
* `-10^4 <= A[i] <= 10^4`

### 分析

这道题要求把Array分成三部分，每部分至少一个元素，三部分的和是一样的，做法是将元素的和加起来，首先检查对3取余是否为0，然后顺序找两个断点看是否存在，如果不是顺序找也可以用双指针来做，一个道理。

### 代码

```java
class Solution {
    public boolean canThreePartsEqualSum(int[] A) {
        if (A == null || A.length < 3) {
            return false;
        }
        int len = A.length;
        int sum = 0;
        for (int a : A) {
            sum += a;
        }
        if (sum % 3 != 0) {
            return false;
        }

        // 分别检查两个断点的位置
        int index1 = 0;
        int index2 = 0;
        boolean isFirst = false;
        boolean isSecond = false;
        int part = sum / 3;
        int temp = 0;
        for (int i = 0; i < len; i++) {
            temp += A[i];
            if (temp == part) {
                temp = 0;
                index1 = i;
                isFirst = true;
                break;
            }
        }
        for (int i = index1 + 1; i < len; i++) {
            temp += A[i];
            if (temp == part) {
                temp = 0;
                index2 = i;
                isSecond = true;
                break;
            }
        }

        return isFirst && isSecond && (index2 < len - 1); //len - 1 表示最后一部分需要有值
    }
}
```

## 769 Max Chunks to Make Sorted

### 题目

Given an array `arr` that is a permutation of `[0, 1, ..., arr.length - 1]`, we split the array into some number of "chunks" \(partitions\), and individually sort each chunk.  After concatenating them, the result equals the sorted array.

What is the most number of chunks we could have made?

**Example 1:**

```text
Input: arr = [4,3,2,1,0]
Output: 1
Explanation:
Splitting into two or more chunks will not return the required result.
For example, splitting into [4, 3], [2, 1, 0] will result in [3, 4, 0, 1, 2], which isn't sorted.
```

**Example 2:**

```text
Input: arr = [1,0,2,3,4]
Output: 4
Explanation:
We can split into two chunks, such as [1, 0], [2, 3, 4].
However, splitting into [1, 0], [2], [3], [4] is the highest number of chunks possible.
```

**Note:**

* `arr` will have length in range `[1, 10]`.
* `arr[i]` will be a permutation of `[0, 1, ..., arr.length - 1]`.

### 解释

对于这个问题，要我们做的是找到一些分裂线，每个分裂线之间的数字排序后，连在一起整个数组是有序的，求出最大分裂线分出的子数组的数目。

做法是让每条分裂线左边的数字都小于这条线右边的数字。这个想法与快速排序非常相似。用一个额外的max数组来记录达到当前位置时最大的数字，将max\[i\]与当前的索引i比较，如果相等，结果+1。

遍历整个array，每一次左边的元素小于等于右边的元素，都会产生一个新的chunck，证明：

```text
原始数组:   0, 2, 1, 4, 3, 5, 7, 6
max数组:   0, 2, 2, 4, 4, 5, 7, 7
sorted后:  0, 1, 2, 3, 4, 5, 6, 7
索引:      0, 1, 2, 3, 4, 5, 6, 7
```

### 代码

O\(n\)和O\(n\)

```java
class Solution {
    public int maxChunksToSorted(int[] arr) {
        if (arr == null || arr.length == 0) {
            return 0;
        }
        int len = arr.length;
        int[] max = new int[len];
        int result = 0;
        max[0] = arr[0];
        
        for (int i = 1; i < len; i++) {
            max[i] = Math.max(max[i - 1], arr[i]);
        }
        
        for (int i = 0; i < len; i++) {
            if (max[i] == i) {
                result++;
            }
        }
        return result;
    }
}
```

优化空间，O\(n\)和O\(1\)

```java
class Solution {
    public int maxChunksToSorted(int[] arr) {
        if (arr == null || arr.length == 0) {
            return 0;
        }
        int result = 0;
        int max = 0; // 跟索引比较，所以不用Integer.MIN_VALUE
        for (int i = 0; i < arr.length; i++) {
            max = Math.max(max, arr[i]);
            if (max == i) {
                result++;
            }
        }
        return result;
    }
}
```

## 


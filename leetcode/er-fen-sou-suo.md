# Binary Search

## 704 Binary Search

### 原题

Given a **sorted** \(in ascending order\) integer array `nums` of `n` elements and a `target` value, write a function to search `target` in `nums`. If `target` exists, then return its index, otherwise return `-1`.

  
**Example 1:**

```text
Input: nums = [-1,0,3,5,9,12], target = 9
Output: 4
Explanation: 9 exists in nums and its index is 4

```

**Example 2:**

```text
Input: nums = [-1,0,3,5,9,12], target = 2
Output: -1
Explanation: 2 does not exist in nums so return -1
```

**Note:**

1. You may assume that all elements in `nums` are unique.
2. `n` will be in the range `[1, 10000]`.
3. The value of each element in `nums` will be in the range `[-9999, 9999]`.

### 题意

模板要清晰，注意一个元素的情况，澄清题意。

### 代码

```java
class Solution {
    public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return -1;
        }
        int low = 0, high = nums.length - 1;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] < target) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return -1;
    }
}
```

递归

```java
class Solution {
    public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return -1;
        }
        return search (nums, target, 0, nums.length - 1);
    }
    private int search(int[] nums, int target, int low, int high) {
        if (low > high) {
            return -1;
        }
        int mid = low + (high - low) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            return search(nums, target, mid + 1, high);
        } else {
            return search(nums, target, low, mid - 1);
        }
    }
}
```

## 50 Power\(x, n\)

### 原题概述

Implement [pow\(_x_, _n_\)](http://www.cplusplus.com/reference/valarray/pow/), which calculates _x_ raised to the power _n_ \(xn\).

**Example 1:**

```text
Input: 2.00000, 10
Output: 1024.00000
```

**Example 2:**

```text
Input: 2.10000, 3
Output: 9.26100
```

**Example 3:**

```text
Input: 2.00000, -2
Output: 0.25000
Explanation: 2-2 = 1/22 = 1/4 = 0.25
```

**Note:**

* -100.0 &lt; _x_ &lt; 100.0
* _n_ is a 32-bit signed integer, within the range \[−231, 231 − 1\]

### 题意和分析

利用递归，每次对n进行折半处理，n最终会变成0，任何数的0次方都是1，这时候回溯，如果n是偶数，直接将上次递归的道德值算个平方在返回；如果是奇数，还需要乘上x的值，比如2^50 = 2^25 \* 2^25，而2^51 = 2 ^25 \* 2^ 25 \* 2。

### 代码

```java
class Solution {
    public double myPow(double x, int n) {
        if (n == 0) { // 基线条件
            return 1;
        }
        double half = myPow(x, n / 2); // 先递归到基线条件

        if (n % 2 == 0) { // 递归过程中，n若是为偶数，那x为正数或负数都没关系
            return half * half;
        } else if (n > 0) { // n为奇数，且大于0
            return half * half * x;
        } else { // n为奇数，且小于0
            return half * half / x;
        }
    }
}
```

半截尾递归的做法

```java
class Solution {
    public double myPow(double x, int n) {
        if (n == 0) {
            return 1;
        }
        // 最大的负整数-2147483648，转换成-n（正数）后会越界，所以单独处理,这里也把n变成long
        if (n == Integer.MIN_VALUE) {
            x *= x;
            n /= 2;
        }
        if (n < 0) {
            n = -n;
            x = 1 / x;
        }
        return (n % 2 == 0) ? myPow(x * x, n / 2) : x * myPow(x * x, n / 2);
    }
}
```

## 69 Sqrt\(x\)

### 原题概述

Implement `int sqrt(int x)`.

Compute and return the square root of _x_, where _x_ is guaranteed to be a non-negative integer.

Since the return type is an integer, the decimal digits are truncated and only the integer part of the result is returned.

**Example 1:**

```text
Input: 4
Output: 2
```

**Example 2:**

```text
Input: 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since 
             the decimal part is truncated, 2 is returned.
```

### 题意和分析

求平方根，保证输入是正数，向下返回值。

1）用二分搜索来在\[0,x\]之间找平方根，找到一个不大于目标值的数；

2）使用牛顿迭代法，这个主要是逼近法求方程的根（[http://www.cnblogs.com/AnnieKim/archive/2013/04/18/3028607.html](http://www.cnblogs.com/AnnieKim/archive/2013/04/18/3028607.html)），这里是求x^2的解，因此方程f\(x\) = x^2 - n， 相当于求解f\(x\)=0的解，如图所示

![](../.gitbook/assets/image%20%288%29.png)

首先取x0，如果x0不是解，做一个经过\(x0,f\(x0\)\)这个点的切线，与x轴的交点为x1。

同样的道理，如果x1不是解，做一个经过\(x1,f\(x1\)\)这个点的切线，与x轴的交点为x2。

以此类推。

以这样的方式得到的xi会无限趋近于f\(x\)=0的解。

判断xi是否是f\(x\)=0的解有两种方法：

一是直接计算f\(xi\)的值判断是否为0，二是判断前后两个解xi和xi-1是否无限接近。

经过\(xi, f\(xi\)\)这个点的切线方程为f\(x\) = f\(xi\) + f’\(xi\)\(x - xi\)，其中f'\(x\)为f\(x\)的导数，本题中为2x。令切线方程等于0，即可求出xi+1=xi - f\(xi\) / f'\(xi\)。

继续化简，xi+1=xi - \(xi2 - n\) / \(2xi\) = xi - xi / 2 + n / \(2xi\) = xi / 2 + n / 2xi = \(xi + n/xi\) / 2。

### 代码

二分法搜索合适的数，模板写法

```java
class Solution {
    public int mySqrt(int x) {
        if (x <= 1) {
            return x;
        }
        int left = 0, right = x;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            int temp = x / mid;
            if (temp >= mid) { // 等于时还不能停，因为是地板除法，temp*mid可能比x小（有更大的备选），所以要挪动left
                left = mid;
            } else {
                right = mid;
            }
        }

        if (x / right >= right) { // 先检查right是否符合，因为right更大
            return right;
        }
        return left;
    }
}
```

牛顿迭代法

```java
class Solution {
    public int mySqrt(int x) {
        if (x == 0) {
            return 0;
        }
        double last = 0;
        double result = 1;
        while (result != last) {
            last = result;
            result = (result + x / result) / 2;
        }
        return (int)result;
    }
}

```

## 34 Find First and Last Position of Element in Sorted Array

### 原题概述

Given an array of integers `nums` sorted in ascending order, find the starting and ending position of a given `target` value.

Your algorithm's runtime complexity must be in the order of _O_\(log _n_\).

If the target is not found in the array, return `[-1, -1]`.

**Example 1:**

```text
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]
```

**Example 2:**

```text
Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]
```

### 题意和分析

这个要求时间复杂度log\(n\)，那一定就是二分查找了，毕竟线性查找没有技术含量，具体做法是分别做两次二分查找，第一次找到target的左边界，第二次找到target的右边界，二分查找根据自己熟悉的写法，需要注意越界的问题。

### 代码

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int[] result = {-1, -1};
        if (nums == null || nums.length == 0) {
            return result;
        }
        
        int left = 0;
        int right = nums.length - 1;
        int startPoint = -1;
        int endPoint = -1;
        
        // 找到target的左边界left
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] >= target) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        if (left <= nums.length - 1 && nums[left] == target) {//left有可能超过right到右边
            startPoint = left;
        } 
        if (startPoint == -1) {
            return result;
        }
        
        //找到target的右边界right
        left = 0;
        right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] <= target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        if (right >= 0 && nums[right] == target) {//right有可能越过left到左边
            endPoint = right;
        } 
        
        result[0] = startPoint;
        result[1] = endPoint;
        
        return result;
    }
}
```

## 278 First Bad Version

### 原题

You are a product manager and currently leading a team to develop a new product. Unfortunately, the latest version of your product fails the quality check. Since each version is developed based on the previous version, all the versions after a bad version are also bad.

Suppose you have `n` versions `[1, 2, ..., n]` and you want to find out the first bad one, which causes all the following ones to be bad.

You are given an API `bool isBadVersion(version)` which will return whether `version` is bad. Implement a function to find the first bad version. You should minimize the number of calls to the API.

**Example:**

```text
Given n = 5, and version = 4 is the first bad version.

call isBadVersion(3) -> false
call isBadVersion(5) -> true
call isBadVersion(4) -> true

Then 4 is the first bad version. 
```

### 题意

这道题就是二分查找的应用，根据自己理解来写，注意边界（所有都是False，所有都是true）就没问题了。

### 代码

```java
/* The isBadVersion API is defined in the parent class VersionControl.
      boolean isBadVersion(int version); */

public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int left = 1;
        int right = n;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (isBadVersion(mid) == true) {
                right = mid - 1;
            } else {
                if (isBadVersion(mid + 1) == true) {
                    return mid + 1;
                } 
                left = mid + 1;
            }
        }
        return 1; //return left; //false positive
    }
}
```

```java
/* The isBadVersion API is defined in the parent class VersionControl.
      boolean isBadVersion(int version); */

public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int left = 1;
        int right = n;

        while (left < right) {
            int mid = left + (right - left) / 2;
            if (isBadVersion(mid)) {
                right = mid; // 可能当前这个就是结果，所以mid - 1，不要越界就行
            } else {
                left = mid + 1;
            }
        }
        return left; // 最后跳出while循环的时候left会 >= right
    }
}
```

## 1011 Capacity To Ship Packages Within D Days

### 题目

A conveyor belt has packages that must be shipped from one port to another within `D` days.

The `i`-th package on the conveyor belt has a weight of `weights[i]`.  Each day, we load the ship with packages on the conveyor belt \(in the order given by `weights`\). We may not load more weight than the maximum weight capacity of the ship.

Return the least weight capacity of the ship that will result in all the packages on the conveyor belt being shipped within `D` days.

**Example 1:**

```text
Input: weights = [1,2,3,4,5,6,7,8,9,10], D = 5
Output: 15
Explanation: 
A ship capacity of 15 is the minimum to ship all the packages in 5 days like this:
1st day: 1, 2, 3, 4, 5
2nd day: 6, 7
3rd day: 8
4th day: 9
5th day: 10

Note that the cargo must be shipped in the order given, so using a ship of capacity 14 and splitting the packages into parts like (2, 3, 4, 5), (1, 6, 7), (8), (9), (10) is not allowed. 
```

**Example 2:**

```text
Input: weights = [3,2,2,4,1,4], D = 3
Output: 6
Explanation: 
A ship capacity of 6 is the minimum to ship all the packages in 3 days like this:
1st day: 3, 2
2nd day: 2, 4
3rd day: 1, 4
```

**Example 3:**

```text
Input: weights = [1,2,3,1,1], D = 4
Output: 3
Explanation: 
1st day: 1
2nd day: 2
3rd day: 3
4th day: 1, 1
```

**Constraints:**

* `1 <= D <= weights.length <= 50000`
* `1 <= weights[i] <= 500`

### 分析

题目很绕，首先比较直观的方法是可以合并数组，以第一个例子，重量列表 \[1,2,3,4,5,6,7,8,9,10\] 和天数 D = 5为例，目标就是要生成一个新列表，其中有 5 个元素，每个元素代表每天搬运的总重量，新列表中最大值即船舶最小运载能力。任务就变成了将重量列表中的元素合并，直至其长度与天数一致。

现在重量列表有 10 个元素，最大值 10 假设为每天搬运上限的话，可以合并前 4 个元素求和得到 10 ，这样列表就变成了 7 个元素，即合并出一个 7 天完成搬运任务的方案。但仍达不到我们 5 天的目标，继续合并，如图：

![](../.gitbook/assets/image%20%28114%29.png)

最终合并出的 5 个元素，代表 要求5 天完成任务的情况下每天运载的重量，最小的船舶运载能力即其最大值 15。根据这个思路可以写出一个很麻烦的代码，分析的时候可用，代码不用掌握。

这道题可以用二分法来解，想到这个办法需要比较扎实的算法理解，首先二分法的左边界left = max\(weights\)，表示起码要能装下一个货物；右边界right = sum\(weights\)，表示最大能装下所有货物。现在的问题就是找到中间的一个最小的capacity，恰好能让所有货物在D天内发出。

从mid = \(left + right\) / 2这个中点的capacity开始查是否合适，如果当前货物（累加）大于mid，就将所需的货柜+1并重置当前货物，这样遍历完一次所有货物后得到一个need表示以mid为capacity，所需的总天数。如果need &gt; D，说明当前货柜太小了，需要left = mid + 1，反之则是货柜太大了，需要right == mid，等于可以继续往左看一下，最后返回left。

### 代码

```java
class Solution {
    public int shipWithinDays(int[] weights, int D) {
        int left = 0, right = 0;
        for (int weight : weights) { // 找到左右边界
            left = Math.max(left, weight);
            right += weight;
        }
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            int need = 1;
            int curLoad = 0;
            
            for (int weight : weights) {
                if (curLoad + weight > mid) { // 达到上限了，需要一个新的一天新货柜
                    need++;
                    curLoad = 0;
                }
                curLoad += weight;
            }
            
            if (need > D) { // 需要的天数太多，需要扩容
                left = mid + 1;
            } else {
                right = mid; //如果等于mid，还可以继续往左边走看看能否更小
            }
        }
        return left;
    }
}
```

## 378 Kth Smallest Element in a Sorted Matrix

### 原题

Given a n x n matrix where each of the rows and columns are sorted in ascending order, find the kth smallest element in the matrix.

Note that it is the kth smallest element in the sorted order, not the kth distinct element.

**Example:**

```text
matrix = [
   [ 1,  5,  9],
   [10, 11, 13],
   [12, 13, 15]
],
k = 8,

return 13.
```

**Note:**  
You may assume k is always valid, 1 ≤ k ≤ n2.

### 分析

寻找第k小的元素，这个数是第k小，不是第k个distinct小（比它更小的数可能有重复）。

暴力的思路无非就是将数组中的元素都放在数组或List中，然后排序，找到第k小的即可，改进以下可以用最大堆或最小堆（将每列最小的元素放进堆中，然后查找每一列下面的元素加入堆，每次取出堆中的元素都是最小的）。

以上这些没有应用到原来的matirx是行和列都是排序的特征。比较容易想到二分法，现在的规律是：每个元素右下角的肯定比自己大，左上角的肯定比自己小，但是左下角和右上角的无法确定。进行数值的二分法，最小值为左上角，最大值为右下角，根据这两个数，求出中间值，然后从左上角开始，数行和列有多少个数比中间值小，计数，如果这个数达到count，就把最大数往中间值挪动，否则把最小值往中间值移动，最后返回左边界。

### 代码

```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int rows = matrix.length;
        int cols = matrix[0].length;
        
        int count = 0;
        int min = matrix[0][0];
        int max = matrix[rows - 1][cols - 1];
        
        while (min < max) {
            int mid = min + (max - min) / 2;
            
            // 计算当前行和列有多少个小于mid的数
            for (int i = 0; i < rows && matrix[i][0] <= mid; i++) { // 当前行的第一个元素小于mid，当前行可查
                for (int j = 0; j < cols; j++) {
                    if (matrix[i][j] <= mid) {
                        count++;
                    }
                }
            }
            if (count >= k) {
                max = mid;
            } else {
                min = mid + 1;
            }
            
            // 移动上边界或下边界，重新统计
            count = 0;
        }
        return min;
    }
}
```

## 702 Search in a Sorted Array of Unknown Size

### 原题

Given an integer array sorted in ascending order, write a function to search `target` in `nums`.  If `target` exists, then return its index, otherwise return `-1`. **However, the array size is unknown to you**. You may only access the array using an `ArrayReader` interface, where `ArrayReader.get(k)` returns the element of the array at index `k` \(0-indexed\).

You may assume all integers in the array are less than `10000`, and if you access the array out of bounds, `ArrayReader.get` will return `2147483647`.

**Example 1:**

```text
Input: array = [-1,0,3,5,9,12], target = 9
Output: 4
Explanation: 9 exists in nums and its index is 4
```

**Example 2:**

```text
Input: array = [-1,0,3,5,9,12], target = 2
Output: -1
Explanation: 2 does not exist in nums so return -1
```

**Constraints:**

* You may assume that all elements in the array are unique.
* The value of each element in the array will be in the range `[-9999, 9999]`.
* The length of the array will be in the range `[1, 10^4]`.

### 分析

倍增找到右边界，然后二分。

### 代码

```java
/**
 * // This is ArrayReader's API interface.
 * // You should not implement it, or speculate about its implementation
 * interface ArrayReader {
 *     public int get(int index) {}
 * }
 */

class Solution {
    public int search(ArrayReader reader, int target) {
        long left = 0, right = 1;
        while (reader.get((int)right) < target) {
            right *= 2;
        }
        while (left + 1 < right) {
            long mid = left + (right - left) / 2;
            if (reader.get((int)mid) == target) {
                return (int)mid;
            } else if (reader.get((int)mid) < target) {
                left = mid;
            } else {
                right = mid;
            }
        }
        if (reader.get((int)left) == target) return (int)left;
        
        return reader.get((int)right) == target ? (int)right : -1;
    }
}
```


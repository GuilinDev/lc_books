---
description: subarray一系列的题，大部分用prefix sum比较好。
---

# 子数组Subarray

## 53 - Maximum Subarray

### 原题概述

Given an integer array `nums`, find the contiguous subarray \(containing at least one number\) which has the largest sum and return its sum.

**Example:**

```text
Input: [-2,1,-3,4,-1,2,1,-5,4],
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
```

**Follow up:**

If you have figured out the O\(_n_\) solution, try coding another solution using the divide and conquer approach, which is more subtle.

### 题意和分析

给一个数组，找到一个子数组，子数组的元素加起来的和最大，并返回这个和。这道题用O\(n\)的办法就是从左扫到右，将临时的tempSum初始化为Integer.MIN\_VALUE，当遇到一个元素时，判断这个元素和之前sum的大小，如果加入当前元素更大，就改变tempSum的值；如果当前元素的值更大，之前的tempSum也不用保留了，一直到末尾。

这道题也可以用DP中的[Kadane’s Algorithm](http://theoryofprogramming.com/2016/10/21/dynamic-programming-kadanes-algorithm/)来进行优化；既然是DP，首先解决子问题的公式\(即状态方程\)，如果想象子问题是这样的公式：maxSubArray\(int nums\[\], int i, int j\)，代表从i位置到j位置是最到的maxSubArray，那么我们的目标就是要分解maxSubArray\(nums, 0, nums.length - 1\)成子问题，然而再往下确是没办法再分了；然后换一种思维，把子问题的公式当作maxSubArray\(int nums\[\], int i\)，表示从位置0到位置i的maxSubArray：

> maxSubArray\(nums, i\) = maxSubArray\(nums, i - 1\) &gt; 0 ? maxSubArray\(nums, i - 1\) : 0 + nums\[i\];

DP擅长优化指数级别的算法为多项式的复杂度，对于本身已是多项式则优化不明显，Time：O\(n\)；Space：O\(1\)。

### 代码

O\(n\)顺序从左扫描到右的做法

```java
class Solution {
    public int maxSubArray(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        long tempSum = Integer.MIN_VALUE, result = Integer.MIN_VALUE;
        for (int num : nums) {
            tempSum = Math.max(num, tempSum + num);//因为只用知道最大的sum是多少就行了，不用知道subarray的起始和终止位置，对于当前的元素来说，只有加入计算或者不加入计算两个选择，如果当前元素num加上之前的tempSum的和比当前的num还要小的话，之前的tempSum就可以舍弃掉了，反之则保留。
            result = Math.max(result, tempSum);//比较已有的result和新得到的sum看谁大
        }
        return (int)result;
    }
}
```

另外一种做法，如果nums\[i\]是负数，那么它不可能代表最优序列的起点，因为任何包含nums\[i\]的作为起点的子序列都可以通过使用nums\[i+1\]作为起点得到改进（少加nums\[i\]这一个负数，所以sum肯定更大）。类似的，任何负的子序列也不可能是最优子序列的前缀（原理相同），联机算法，复杂度一样。

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int max_so_far = nums[0]; //目前为止的最大值，初始化为第一个元素，[-1,-2,-3,-4]这样的全负数子序列就可以考虑在内了
        int max_ending_here = 0;//以当前数为终点的子序列的最大值
        for(int i = 0; i < nums.length; i++) {
            max_ending_here = max_ending_here + nums[i];
            if(max_so_far < max_ending_here) {
                max_so_far = max_ending_here;
            }
            if(max_ending_here < 0) {//当前元素为重点的子序列的sum小于0的时候，下一个元素重新开始(反正前面都是负的了，不要了)
                max_ending_here = 0;
            }
        }
        return max_so_far;
    }
}
```

DP的做法，其实上面的做法也是DP优化空间后的做法，下面是DP原生用数组来存储中间状态

```java
class Solution {
    public int maxSubArray(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int len = nums.length;
        long[] dp = new long[len]; //dp[i]就是以num[i]结尾的最大子序列
        dp[0] = nums[0];
        long result = dp[0]; // 一个元素的情况
        for (int i = 1; i < len; i++) {
            dp[i] = Math.max(nums[i], dp[i - 1] + nums[i]);
            result = Math.max(result, dp[i]);
        }
        return (int)result;
    }
}
```

## 152 - Maximum Product Subarray

### 原题概述

Given an integer array `nums`, find the contiguous subarray within an array \(containing at least one number\) which has the largest product.

**Example 1:**

```text
Input: [2,3,-2,4]
Output: 6
Explanation: [2,3] has the largest product 6.
```

**Example 2:**

```text
Input: [-2,0,-1]
Output: 0
Explanation: The result cannot be 2, because [-2,-1] is not a subarray.
```

### 题意和分析

这题和上一题53 - Maximum Subarray非常类似，一个是求最大和，而这个是求最大乘积。可以用大致类似的思路解，只是求乘积比求和要复杂些，因为多了不少corner cases。当然那这题出得还算仁慈，因为这题其实有两个地方简化了： 

1）注意这里的数组是整型的，如果含有浮点数，就有可能出现0-1之间类似0.25这样的小数，所以即使是全都是正数，也可以越乘越小。如果数组里的数字全为正数还好说，因为可以用求对数的方式把求乘积转化为求和，从而转换为之前的Maximum Subarray。但是因为这题有负数和0的存在，所以求对数的方法行不通。

2）这题的测试用例里数组元素的绝对值都非常的小，而实际中如果真的连乘起来，最后数值越界很容易发生的。如果考虑这一点，要么估计得用类似Java里BigInteger类这样的东西去避免越界。

所以如同上面一样，在遍历过程中，不断更新以当前元素为终点的subarray的乘积极大值（下面简称极大值）和最大值。本质上无非就是要做出一个二选一：要么继续把当前遍历的元素算上，扩展当前的subarray，要么就重新开始一个subarray。此外，如何更新最大值？由于有整数，负数和0的存在，需要分为三种情况，并且还需要维护一个极小值。为了方便连乘操作，因为都是整数，这里规定维护的最大乘积必须大于等于1，即不能等于0。另外需要注意，在没有遍历到负数之前，极小值这里其实和极大值是一样大的（不考虑为0的情况），也可以是正数。

综合考虑如下：

1）如果当前元素为正数，那么极大值只可能扩大，所以应该继续扩展当前subarray：

此种情况简单，极大值应该更新为原极大值乘以当前元素，极小值更新为原极小值乘以当前元素。全局最大值跟极大值比较。

2）如果当前元素为负数，那么极大值可能会变小，所以不清楚应该继续扩展当前subarray还是新起一个subarray：

对于极大值的更新：如果扩展当前subarray，极大值为原极小值乘以当前元素；如果另外新起一个subarray，由于当前元素为负数，所以直接舍弃，根据规定，设为初始值1。由于这里原极小值不一定为负数，所以前者和后者之间比较没有绝对的谁大。

对于极小值的更新：如果扩展当前subarray，极小值为原极大值乘以当前元素；如果另外新起一个subarray，极小值为当前元素。不过由于之前极大值肯定大于1，所以_当前元素乘以原先的极大值_肯定比_当前元素本身_大，所以极小值更新为原极大值乘以当前元素。

最应该小心的地方是更新全局最大值：这里的全局最大值不能和极大值比较，而应该和极小值乘以当前元素值比较，即扩展当前subarray的选择比较。因为如果极大值此时为1，则并不是靠实实在在存在的，以当前元素结尾的subarray获得，而是靠舍弃当前元素，寄希望于之后“可能”出现的新subarray。举个例子，如果数组为{-2}，或者{-1, 0, -2}，那么无论如何是不会出现最大值为1这种情况的，因为负数的后面没有出现过正数。

3）如果当前元素为0，那么包括一个0会使得极大值成为0，而按照操作规定，这里的极大值应该大于等于1，所以应该舍弃当前元素，新起一个subarray。

对于极大值和极小值，由于新起一个subarray，全部还原为1。

对于全局最大值的更新，这里和2）类似。由于极大值的获取是寄希望于之后“可能”出现的新subarray，所以更新全局最大值的时候不能和此时的极大值1进行比较，而应该和实实在在的0比较。

DP解法，思路跟53题的做法一样，使用滚动数组，只是corner cases更多，获得dp\[i\]的值只需要dp\[i-1\]的值，所以是不需要保存整个DP表的。这样一来，DP可以用滚动数组进行优化空间。简单的写法其实就是设一对prevMin/prevMax表示上一个值，以及还有一对curMin/curMax表示当前值。

Time：O\(n\)；Space：O\(1\)。

### 代码

滚动数组

```java
class Solution {
    public int maxProduct(int[] nums) {
        int maxProduct = nums[0], temp = 0;
        for (int i = 1, max = maxProduct, min = maxProduct; i < nums.length; i++) {
            if (nums[i] < 0) {
                temp = min;
                min = max;
                max = temp;
            }
            max = Math.max(nums[i], max * nums[i]);
            min = Math.min(nums[i], min * nums[i]);
            maxProduct = Math.max(maxProduct, max);
        }
        return maxProduct;
    }
}
```

DP的解法

```java
class Solution {
    public int maxProduct(int[] nums) {
        int max = nums[0];
        int prevMin = nums[0], prevMax = nums[0];
        int curMin, curMax;
        for (int i = 1; i < nums.length; i++) {
            curMin = Math.min(Math.min(prevMax * nums[i], prevMin * nums[i]), nums[i]);
            curMax = Math.max(Math.max(prevMax * nums[i], prevMin * nums[i]), nums[i]);
            prevMin = curMin;
            prevMax = curMax;
            max = Math.max(curMax, max);
        }
        return max;
    }
}
```

## 560 - Subarray Sum Equals K

### 原题概述

Given an array of integers and an integer **k**, you need to find the total number of continuous subarrays whose sum equals to **k**.

**Example 1:**  


```text
Input:nums = [1,1,1], k = 2
Output: 2
```

**Note:**  


1. The length of the array is in range \[1, 20,000\].
2. The range of numbers in the array is \[-1000, 1000\] and the range of the integer **k** is \[-1e7, 1e7\].

### 题意和分析

1\) 这道题是找到所有的子序列的个数，这些子序列的元素的和等于给定的一个target。暴力解法子数组左右两个边界遍历O\(n^2\)，嵌套加上求子数组的和O\(n\)，总共O\(n^3\)，不行。

2\) 如果使用前缀和，可以先计算所有前缀的sum，可以降到两层循环，前缀和就是相当于固定了左边界，枚举右边界，或者固定右边界，枚举左边界，降了一维，O\(n^2\)。

3\) **前缀和+哈希表优化做法**：题目只关心子数组的个数，不关心具体的子数组长什么样子。定义preSum\[i\]为\[0, i\]之间元素的和，preSum\[i\]可以由preSum\[i - 1\]得到，即，preSum\[i\] = preSum\[i - 1\] + nums\[i\]：

![](../../.gitbook/assets/image%20%2858%29.png)

preSum\[i\] - preSum\[j\] = nums\[j + 1\] + nums\[j + 2\] + ...+ nums\[i - 1\] + nums\[i\] ?= k，简单移项转换成：

preSum\[j\] == preSum\[i\] - k

所以，统计有多少个preSum\[i\] - k等于preSum\[j\]这个条件的个数是我们需要的，于是把preSum\[i\] - k作为key，把前面计算的preSum\[j\]前面本身出现的次数作为value，如果再次出现\(preSum\[j\] == preSum\[i\] - k\)，说明等式成立，\[i, j\]这个区间出现了和为k的子数组，结果+1。 

对于一开始的情况，下标 0 之前没有元素，可以认为前缀和为 0，个数为 1 个，因此 `preSumFreq.put(0, 1);`，这一点是必要且合理的。举例说明：

Array = {3,4,7,2,-3,1,4,2}，k= 7，如果遇到二者相减（sum - k）等于7，或者sum本身等于7或者7的倍数，subarray的count均+1，（注意黑体字）

* 循环初始map 为 {{0,1}}， preSum = 0， result = 0；{0,1}为初始，preSum为0开始
* 循环第一个遇到3，map 为 {{0, 1}, {3, 1}}；preSum = 3；preSum - k = -4；result = 0；
* 循环第二个遇到4，map 为 {{0,1}, {3,1}, {**7,1**}}；preSum = 7；preSum - k = 0，0作为key之前出现过， 结果+1，result = 1；
* 循环第三个遇到7，map 为 {{0,1}, {3,1}, {**7, 1**}, {**14, 1**}}；preSum = 14；preSum - k = 7，7作为key之前出现过，结果+1， result = 2；
* 循环第四个遇到2，map 为 {{0,1}, {3,1}, {7,1}, {14,1}, {16,1}}；preSum = 16；preSum - k = 9，result = 2；
* 循环第五个遇到-3，map 为 {{0,1}, {3,1}, {7,1}, {14,1}, {16,1}, {13,1}}；preSum = 13；preSum - k = 6， result = 2；
* 循环第六个遇到1，map 为 {{0,1}, {3,1}, {7,1}, {14,**2**}, {16,1}, {13,1}}；preSum = 14；preSum - k = 7，7作为key之前出现过，结果+1， result = 3；
* 循环第七个遇到4，map 为 {{0,1}, {3,1}, {7,1}, {14,2}, {16,1}, {13,1}, {18,1}}；preSum = 18； preSum - k = 11；result = 3；
* 循环第八个遇到2，map - {{0,1}, {3,1}, {7,1}, {14,2}, {16,1}, **{13,1}**, {18,1}, {**20,1**}}，preSum = 20；preSum - k = 13，13作为key之前出现过，结果+1，result = 4；
* 循环结束

如此，通过preSum\[j\] - preSum\[i\] = k的数目，计算出结果。Time：O\(n\)；Space：O\(n\)。

### 代码

只用前缀和，O\(n^2\)

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int len = nums.length;
        int result = 0;
        for (int right = 0; right < len; right++) {
            int preSum = 0; // 每次遍历每个sum记录以固定右边界的前缀和
            for (int left = right; left >= 0; left--) {
                preSum += nums[left];
                if (preSum == k) {
                    result++;
                }
            }
        }
        return result;
    }
}
```

前缀和 + HashMap优化，O\(n\)的解法

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int preSum = 0;
        int result = 0;
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, 1); // 初始化，表示达到target-t时的sum有一个
        
        for (int num : nums) {
            preSum += num;
            if (map.containsKey(preSum - k)) {
                result += map.get(preSum - k);
            }
            map.put(preSum, map.getOrDefault(preSum, 0) + 1);
        }
        return result;
    }
}
```


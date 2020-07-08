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

DP解法，思路跟53题的做法一样，使用滚动数组，只是corner cases更多，获得dp\[i\]的值只需要dp\[i-1\]的值，所以是不需要保存整个DP表的。这样一来，DP可以用滚动数组进行优化。简单的写法其实就是设一对prevMin/prevMax表示上一个值，以及还有一对curMin/curMax表示当前值。

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

这道题是找到所有的子序列的个数，这些子序列的元素的和等于给定的一个target。暴力解法就是两层循环，所有的子序列都计算一遍，找到所有sum\[i, j\] = k的子序列，O\(n^2\)。

我们需要找到sum\[i, j\]，如果我们知道sum\[0, i-1\]和sum\[0, j\]，这样一减就知道sum\[i, j\]是否等于k，换句话说，sum\[j\] - sum\[i\]的话，nums\[i, j\]之间数字的和就是k，比如sum\[j\]跟sum\[i\]一样，那么nums\[i, j\]这段加起来就是0。result += map.get\(sum - k\)这句比较难懂，这个意思是如果sum - k多次等于一个值，那么前面每一个nums\[i\]位置到这里的subarray都算是一个可计入记过的subarray，相当于是需要记得之前有多少个相同的值。

做法就是遍历这个数组，计算current的sum并且把所有的sum都存到一个HashMap里面。举例说明：

Array = {3,4,7,2,-3,1,4,2}，k= 7，如果遇到二者相减（sum - k）等于7，或者sum本身等于7或者7的倍数，subarray的count均+1，（注意黑体字）

* 循环初始map - {{0,1}}， sum - 0， result - 0；
* 第一此循环遇到3，map - {{0, 1}, {3, 1}}；sum - 3；result - 0；sum - k = -4；
* 第二次循环遇到4，map - {{0,1}, {3,1}, {**7,1**}}；sum - 7；result - 1；sum - k = 0；
* 第三次循环遇到7，map - {{0,1}, {3,1}, {**7, 1**}, {**14, 1**}}；sum - 14；result - 2；sum - k = 7；
* 第四次循环遇到2，map - {{0,1}, {3,1}, {7,1}, {14,1}, {16,1}}；sum - 16；result - 2；sum - k = 9；
* 第五次循环遇到-3，map - {{0,1}, {3,1}, {7,1}, {14,1}, {16,1}, {13,1}}；sum - 13；result - 2；sum - k = 6；
* 第六次循环遇到1，map - {{0,1}, {3,1}, {7,1}, {14,**2**}, {16,1}, {13,1}}；sum - 14；result - 3；sum - k = 7；
* 第七次循环遇到4，map - {{0,1}, {3,1}, {7,1}, {14,2}, {16,1}, {13,1}, {18,1}}；sum - 18；result - 3； sum - k = 11；
* 第八次循环遇到2，map - {{0,1}, {3,1}, {7,1}, {14,2}, {16,1}, **{13,1}**, {18,1}, {**20,1**}}，sum - 20；result - 4；sum - k = 13；
* 循环结束

  

Time：O\(n\)；Space：O\(n\)。

### 代码

暴力解法O\(n^2\)

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int count = 0;
        int[] sum = new int[nums.length + 1];
        sum[0] = 0;
        for (int i = 1; i <= nums.length; i++) {
            sum[i] = sum[i - 1] + nums[i - 1];
        }
        for (int start = 0; start < nums.length; start++) {
            for (int end = start + 1; end <= nums.length; end++) {
                if (sum[end] - sum[start] == k)
                    count++;
            }
        }
        return count;
    }
}
```

优化成O\(n\)的解法

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int sum = 0, result = 0;
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);//把以0为key的pair放进去，从sum为0开始

        for (int i = 0; i < nums.length; i++) {
            sum += nums[i];//在之前sum的基础上，遇到当前的元素就更新sum，然后检查hashmap看之前出现过没有
            if (map.containsKey(sum - k)) {
                result += map.get(sum - k);//记得之前有几个这样的值
            }
            map.put(sum, map.getOrDefault(sum, 0) + 1);
        }
        return result;
    }
}
```


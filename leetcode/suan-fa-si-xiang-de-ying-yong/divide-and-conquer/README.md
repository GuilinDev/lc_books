---
description: >-
  面试中的动态规划大致分为单向递归（首或者尾）， O（n^2）或者O(n^3) 距离递归,  O(m*n)递归，有限定条件的NP (背包）。 
  至于贪心和带状态的dp(走道铺砖）一类的dp，在面试中几乎不会遇到， 因为很难临时造出一道这样的题目， 面试官一般也没这个能力和时间来思考题目是不是严谨。
---

# Dynamic Programming

![](../../../.gitbook/assets/image%20%2832%29.png)

## 121 - Best Time to Buy and Sell Stock

### 原题概述

Say you have an array for which the _i_th element is the price of a given stock on day _i_.

If you were only permitted to complete at most one transaction \(i.e., buy one and sell one share of the stock\), design an algorithm to find the maximum profit.

Note that you cannot sell a stock before you buy one.

**Example 1:**

```text
Input: [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
             Not 7-1 = 6, as selling price needs to be larger than buying price.
```

**Example 2:**

```text
Input: [7,6,4,3,1]
Output: 0
Explanation: In this case, no transaction is done, i.e. max profit = 0.
```

### 题意和分析

卖需要在买之后，一遍循环过去后先对比之前的buy的最小值和当前值得到当前最小buy，然后当前的price减去当前最小的buy得到利润，保留最大的利润值。这大概是最简单的动态规划的思想了。

### 代码

```java
class Solution {
    public int maxProfit(int[] prices) {
        int minBuy = Integer.MAX_VALUE, maxProfit = 0;
        for (int price : prices) {
            minBuy = Math.min(minBuy, price);//找到子问题中最小的buy的价格
            maxProfit = Math.max(maxProfit, price - minBuy);//找到最小buy价格下最大的利润
        }

        return maxProfit;
    }
}
```

## 70 - Climb Stairs

### 原题概述

You are climbing a stair case. It takes _n_ steps to reach to the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

**Note:** Given _n_ will be a positive integer.

**Example 1:**

```text
Input: 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps
```

**Example 2:**

```text
Input: 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```

### 题意和分析

爬梯子，每次爬一步或者两步，总共有多少种爬法。虽然是简单题，但是一道极佳的说明动态规划的题目，想法类似于斐波那契数列。

### 代码

以空间换时间，空间复杂度为O\(n\)

```java
class Solution {
    public int climbStairs(int n) {
        if (n <= 0) {
            return 0;
        }
        if (n == 1) {
            return 1;
        }
        int[] stair = new int[n];

        //two base cases
        stair[0] = 1;
        stair[1] = 2;

        //以空间换时间，到每个阶梯i的时候都记忆一下有多少种爬法
        for (int i = 2; i < n; i++) {
            stair[i] = stair[i-1] + stair[i-2];
        }
        return stair[n-1];//注意这里是返回stair[n-1]，这是最后一步台阶
    }
}
```

优化一下空间，只需存最后两步即可

```java
class Solution {
    public int climbStairs(int n) {
        if (n <= 0) {
            return 0;
        }

        //前两步的基础值
        if (n == 1) {
            return 1;
        }
        if (n == 2) {
            return 2;
        }

        //O(1)的空间
        int a = 1, b = 2, result = 0;

        //只需记住n-1和n-2最后两步的和
        for (int i = 2; i < n; i++) {
            result = a + b;//i-1和i-2所有路径和总和
            a = b;//i-2的总和
            b = result;//i-1的总和
        }
        return result;
    }
}
```

## 192 - House Robber

### 原题概述

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security system connected and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight **without alerting the police**.

**Example 1:**

```text
Input: [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
             Total amount you can rob = 1 + 3 = 4.
```

**Example 2:**

```text
Input: [2,7,9,3,1]
Output: 12
Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
             Total amount you can rob = 2 + 9 + 1 = 12.
```

### 题意和分析

相当于数组中不相邻的数相加如何使其和最大，依然是DP来求极值，维护一个一维数组dp，其中dp\[i\]表示到数组的i位置时之前的不相邻数的最大和，递推公式为dp\[i\] = max\(nums\[i\] + dp\[i-2\], dp\[i-1\]\)，初始化dp\[0\]为nums\[0\]，dp\[1\]为之前的nums\[0\]和nums\[1\]中的较大值。

### 代码

```java
class Solution {
    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) return 0;

        int len = nums.length;
        if (len == 1) return nums[0];

        int[] dp = new int[len];
        dp[0] = nums[0];
        dp[1] = Math.max(nums[0], nums[1]);

        for (int i = 2; i < len; i++) {
            dp[i] = Math.max(dp[i-1], nums[i] + dp[i-2]);//当前数+不相邻子问题的和，与相邻子问题的和，相比较看哪个大
        }
        return dp[len-1];
    }
}
```

另外的思路是维护两个变量a和b，按奇偶来更新这两个值，保证a和b这两个最大值的备选值均为不相邻数组成，最后判断a和b哪个大就是最后的最大值。

```java
class Solution {
    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) return 0;

        int len = nums.length;
        if (len == 1) return nums[0];

        int a = 0, b = 0;
        for (int i = 0; i < len; i++) {
            if (i % 2 != 0) {//奇数
                a = Math.max(a + nums[i], b);//注意是不相邻数的和，相比较
            } else {//偶数
                b = Math.max(b + nums[i], a);
            }
        }
        return Math.max(a, b);
    }
}
```

## 338 - Counting Bits

### 原题概述

Given a non negative integer number **num**. For every numbers **i** in the range **0 ≤ i ≤ num** calculate the number of 1's in their binary representation and return them as an array.

**Example 1:**

```text
Input: 2
Output: [0,1,1]
```

**Example 2:**

```text
Input: 5
Output: [0,1,1,2,1,2]
```

**Follow up:**

* It is very easy to come up with a solution with run time **O\(n\*sizeof\(integer\)\)**. But can you do it in linear time **O\(n\)** /possibly in a single pass?
* Space complexity should be **O\(n\)**.
* Can you do it like a boss? Do it without using any builtin function like **\_\_builtin\_popcount** in c++ or in any other language.

### 题意和分析

给一个整数n，找出0到n间数字二进制表达式中1的个数，给了很多限制条件，就是不让一位一位去算，而是找规律。看了discussion才知道dp是怎样来的：

首先给一个例子：

Index： 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15

相应的1的个数：0 1 1 2 1 2 2 3 1 2 2 3 2 3 3 4

四个一组看，很容易看出来重叠子问题了

dp\[0\] = 0;

dp\[1\] = dp\[0\] + 1;

dp\[2\] = dp\[0\] + 1;

dp\[3\] = dp\[1\] +1;

dp\[4\] = dp\[0\] + 1;

dp\[5\] = dp\[1\] + 1;

dp\[6\] = dp\[2\] + 1;//为了找寻规律，这里就不是dp\[6\] = dp\[1\] + 1了

dp\[7\] = dp\[3\] + 1;

dp\[8\] = dp\[0\] + 1;  
...

从上述表达式可以得出递归式：

dp\[1\] = dp\[1-1\] + 1;

dp\[2\] = dp\[2-2\] + 1;

dp\[3\] = dp\[3-2\] +1;

dp\[4\] = dp\[4-4\] + 1;

dp\[5\] = dp\[5-4\] + 1;

dp\[6\] = dp\[6-4\] + 1;

dp\[7\] = dp\[7-4\] + 1;

dp\[8\] = dp\[8-8\] + 1;  
...

dp\[index\] = dp\[index - offset\] + 1， 从index = 1开始，其中，如果当前索引i是offset的两倍，那offset自增加倍。

### 代码

```java
class Solution {
    public int[] countBits(int num) {
        int[] result = new int[num+1];
        int offset = 1;
        for (int index = 1; index <= num; index++) {//规律从1开始
            if (offset*2 == index) {
                offset *= 2;
            }
            result[index] = result[index - offset] + 1;//重叠子问题
        }
        return result;
    }
}
```

## 139 - Word Break

### 原题概述

Given a **non-empty** string _s_ and a dictionary _wordDict_ containing a list of **non-empty** words, determine if _s_ can be segmented into a space-separated sequence of one or more dictionary words.

**Note:**

* The same word in the dictionary may be reused multiple times in the segmentation.
* You may assume the dictionary does not contain duplicate words.

**Example 1:**

```text
Input: s = "leetcode", wordDict = ["leet", "code"]
Output: true
Explanation: Return true because "leetcode" can be segmented as "leet code".
```

**Example 2:**

```text
Input: s = "applepenapple", wordDict = ["apple", "pen"]
Output: true
Explanation: Return true because "applepenapple" can be segmented as "apple pen apple".
             Note that you are allowed to reuse a dictionary word.
```

**Example 3:**

```text
Input: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
Output: false
```

### 题意和分析

{% embed data="{\"url\":\"https://blog.csdn.net/linhuanmars/article/details/22358863\",\"type\":\"link\",\"title\":\"Word Break -- LeetCode - CSDN博客\",\"description\":\"原题链接: http://oj.leetcode.com/problems/word-break/  这道题仍然是动态规划的题目，我们总结一下动态规划题目的基本思路。首先我们要决定要存储什么历史信息以及用什么数据结构来存储信息。然后是最重要的递推式，就是如从存储的历史信息中得到当前步的结果。最后我们需要考虑的就是起始条件的值。 接下来我们套用上面的思路来解这道题。首先我们要存储的历史信息res\",\"icon\":{\"type\":\"icon\",\"url\":\"https://csdnimg.cn/public/favicon.ico\",\"aspectRatio\":0}}" %}

> 动态规划题目的基本思路：首先我们要决定要存储什么历史信息以及用什么数据结构来存储信息，然后是最重要的递推式，就是如何从存储的历史信息中得到当前步的结果，最后我们需要考虑的就是起始条件的值。

### 代码

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        if (s == null || s.length() == 0) {
            return true;
        }
        boolean[] res = new boolean[s.length() + 1];
        res[0] = true;//初始条件
        for (int i = 0; i < s.length(); i++) {
            StringBuilder sb = new StringBuilder(s.substring(0, i + 1));//取0到i的子字符串
            for (int j = 0; j <= i; j++) {
                if (res[j] && wordDict.contains(sb.toString())) {
                    res[i+1] = true;
                    break;
                }
                sb.deleteCharAt(0);//？
            }
        }
        return res[s.length()];
    }
}
```

## 72 - Edit Distance

### 原题概述

Given two words _word1_ and _word2_, find the minimum number of operations required to convert _word1_ to _word2_.

You have the following 3 operations permitted on a word:

1. Insert a character
2. Delete a character
3. Replace a character

**Example 1:**

```text
Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation: 
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')
```

**Example 2:**

```text
Input: word1 = "intention", word2 = "execution"
Output: 5
Explanation: 
intention -> inention (remove 't')
inention -> enention (replace 'i' with 'e')
enention -> exention (replace 'n' with 'x')
exention -> exection (replace 'n' with 'c')
exection -> execution (insert 'u')
```

### 题意和分析

从一个work变成另外一个word最少需要多少次操作，操作可以是添加一个字符，删除一个字符和替换一个字符。 维护一个二维的数组dp，其中dp\[i\]\[j\]表示从word1的前i个字符转换到word2的前j个字符所需要的步骤（dp\[len1\]\[len2\]自然就是最后需要得到的结果），初始化的话需要给第一行和第一列赋值， 因为第一行和第一列对应的总有一个字符串是空串，于是转换步骤完全是另一个字符串的长度。所以难点还是在于找出递推式，先举个例子来看，比如word1是“bbc"，word2是”abcd“，得到的dp数组如下

```text
  Ø a b c d
Ø 0 1 2 3 4
b 1 1 1 2 3
b 2 2 1 2 3
c 3 3 2 1 2
```

可以发现，当word1\[i\] == word2\[j\]时，dp\[i\]\[j\] = dp\[i-1\]\[j-1\]，当word1\[i\] != word2\[j\]时，dp\[i\]\[j\]是其左，左上，正上方三个值当中的最小值+1，因此，递推式就是

dp\[i\]\[j\] =      /    dp\[i - 1\]\[j - 1\]                                      if word1\[i - 1\] == word2\[j - 1\]

                  \    min\(dp\[i - 1\]\[j - 1\], min\(dp\[i - 1\]\[j\], dp\[i\]\[j - 1\]\)\) + 1            else

### 代码

```java
class Solution {
    public int minDistance(String word1, String word2) {
        int len1 = word1.length(), len2 = word2.length();

        int[][] dp = new int[len1+1][len2+1];//最开始对应了一个空字符串

        char[] wordArr1 = word1.toCharArray(), wordArr2 = word2.toCharArray();

        //为第一行和第一列赋值
        for (int i = 0; i <= len1; i++) dp[i][0] = i;
        for (int i = 0; i <= len2; i++) dp[0][i] = i;

        //第一行和第一列已经有值，所以从1开始
        for (int i = 1; i <= len1; i++) {
            for (int j = 1; j <= len2; j++) {
                if (wordArr1[i-1] == wordArr2[j-1]) {
                    dp[i][j] = dp[i-1][j-1];
                } else {
                    dp[i][j] = Math.min(Math.min(dp[i-1][j-1], dp[i-1][j]), dp[i][j-1]) + 1;
                }
            }
        }
        return dp[len1][len2];
    }
}
```




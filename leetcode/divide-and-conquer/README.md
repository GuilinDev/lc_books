---
description: >-
  面试中的动态规划大致分为单向递归（首或者尾）， O（n^2）或者O(n^3) 距离递归,  O(m*n)递归，有限定条件的NP (背包）。 
  至于贪心和带状态的dp(走道铺砖）一类的dp，在面试中几乎不会遇到， 因为很难临时造出一道这样的题目， 面试官一般也没这个能力和时间来思考题目是不是严谨。
---

# Dynamic Programming

![](../../.gitbook/assets/image%20%2841%29.png)

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

## 62 - Unique Paths

### 原题概述

A robot is located at the top-left corner of a _m_ x _n_ grid \(marked 'Start' in the diagram below\).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid \(marked 'Finish' in the diagram below\).

How many possible unique paths are there?

![](https://leetcode.com/static/images/problemset/robot_maze.png)  
Above is a 7 x 3 grid. How many possible unique paths are there?

**Note:** _m_ and _n_ will be at most 100.

**Example 1:**

```text
Input: m = 3, n = 2
Output: 3
Explanation:
From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
1. Right -> Right -> Down
2. Right -> Down -> Right
3. Down -> Right -> Right
```

**Example 2:**

```text
Input: m = 7, n = 3
Output: 28
```

### 题意和分析

要想从start 点到 finish 点，那么，必然经过 h1 或 h2 点，如下图所示：

![enter image description here](http://images.gitbook.cn/aeb1d490-6ddd-11e8-9ab5-a93e1135abc8)

所以，问题转化为：求 start 点到 h1 点，或到 h2 点的路径中的较小者，这相当于将问题域变小了 1，递归下去，直到问题域变为 1 个点。

使用额外的一个二维数组dp，其中dp\[i\]\[j\]表示到当前位置不同的走法的个数，然后可以得到递推式为: dp\[i\]\[j\] = dp\[i - 1\]\[j\] + dp\[i\]\[j - 1\]，另外对于空间复杂度， 每次只需要用到上一行当前列，以及前一列当前行的信息，只需要用一个一维数组存上一行的信息即可，然后扫过来依次更替掉上一行对应列的信息即可，一行一行的刷新，因此可以简化为使用一维数组dp。 时间复杂度是O\(m\*n\)，空间复杂度O\(n\)。

### 代码

```java
class Solution {
    public int uniquePaths(int m, int n) {
        if (m < 0 || n < 0) {
            return 0;
        }
        int[] dp = new int[n];
        dp[0] = 1;//只记录列
        for (int i = 0; i < m; i++) {//行从第0行开始
            for (int j = 1; j < n; j++) {//列从第1列开始
                dp[j] += dp[j - 1];
            }
        }
        return dp[n - 1];
    }
}
```

这道题也可以用纯粹的数学的方法来解，求出组合数， 从左上到右下相当于机器人总共走了m + n - 2步，其中m - 1步向下走，n - 1步向右走，那么总共不同的方法个数就相当于在步数里面m - 1和n - 1中较小的那个数的取法，时间复杂度O\(min\(m, n\)，空间复杂度O\(1\)。

```java
class Solution {
    public int uniquePaths(int m, int n) {
        if (m < 0 || n < 0) {
            return 0;
        }
        double dom = 1, dedom = 1;

        //找出m和n中的较小值
        int small = m < n ? m - 1 : n - 1;
        int big = m < n ? n - 1 : m - 1;
        //组合公式求出分子和分母
        for (int i = 1; i <= small; i++) {
            dedom *= i;
            dom *= small + big + 1 - i;
        }
        return (int)(dom / dedom);
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
        for (int index = 1; index <= num; index++) {
            if (offset*2 == index) {
                offset *= 2;
            }
            result[index] = result[index - offset] + 1;//重叠子问题
        }
        return result;
    }
}
```

### 

```java
class Solution {
    public int[] countBits(int num) {
        int[] results = new int[num + 1];//从0到num
        Arrays.fill(results, 0); //results[0] = 0;
        for (int i = 1; i <= num; i++) {
            results[i] = results[i & i - 1] + 1;//dp递推式，下一个元素是上一个元素+1（下一个元素和上一个元素的关系是相差一个二进制的1）
        }
        return results;
    }
}
```

## 139 - Word Break

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

{% embed url="https://blog.csdn.net/linhuanmars/article/details/22358863" %}

> 动态规划题目的基本思路：首先我们要决定要存储什么历史信息以及用什么数据结构来存储信息，然后是最重要的递推式，就是如何从存储的历史信息中得到当前步的结果，最后我们需要考虑的就是起始条件的值。

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        if (s.isEmpty()) {
            return true;
        }
        int len = s.length();
        boolean[] results = new boolean[len + 1];
        results[0] = true;
        for (int i = 0; i < s.length(); i++) {
            for (int j = 0; j <= i; j++) {
                String str = s.substring(j, i + 1);
                if (results[j] && wordDict.contains(str)) {
                    results[i + 1] = true;
                    break;//
                }
            }
        }
        return results[len];
    }
}
```

代码

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

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        if (s.isEmpty()) {
            return true;
        }
        int len = s.length();
        boolean[] results = new boolean[len + 1];
        results[0] = true;
        for (int i = 0; i < s.length(); i++) {
            for (int j = 0; j <= i; j++) {
                String str = s.substring(j, i + 1);
                if (results[j] && wordDict.contains(str)) {
                    results[i + 1] = true;
                    break;//
                }
            }
        }
        return results[len];
    }
}
```

## 140 Word Break II

### 原题概述

Given a **non-empty** string _s_ and a dictionary _wordDict_ containing a list of **non-empty** words, add spaces in _s_ to construct a sentence where each word is a valid dictionary word. Return all such possible sentences.

**Note:**

* The same word in the dictionary may be reused multiple times in the segmentation.
* You may assume the dictionary does not contain duplicate words.

**Example 1:**

```text
Input:
s = "catsanddog"
wordDict = ["cat", "cats", "and", "sand", "dog"]
Output:
[
  "cats and dog",
  "cat sand dog"
]
```

**Example 2:**

```text
Input:
s = "pineapplepenapple"
wordDict = ["apple", "pen", "applepen", "pine", "pineapple"]
Output:
[
  "pine apple pen apple",
  "pineapple pen apple",
  "pine applepen apple"
]
Explanation: Note that you are allowed to reuse a dictionary word.
```

**Example 3:**

```text
Input:
s = "catsandog"
wordDict = ["cats", "dog", "sand", "and", "cat"]
Output:
[]
```

### 题意和分析

第139题只要求判断能否拆分成字典中的单词，这道题则要求返回所有的情况，既然是列举所有情况，一般使用递归来做，以第一个case做例子，单词c开始那就在字典里找到cat和cats，如果是cat剩下的就是sanddog，然后再根据s来找，但“ 如果不用记忆数组做减少重复计算的优化，那么递归方法跟brute force没什么区别”， 所以如果当s变成 "sanddog"的时候，那么此时我们知道其可以拆分成sand和dog，当某个时候如果又遇到了这个 "sanddog"的时候，就不用重新计算了， 因此要将这个中间结果保存起来，也就是同时保存s和其所有的拆分的字符串，这可以使用一个HashMap来建立二者之间的映射，那么在递归函数中，首先检测当前s是否已经有映射，有的话直接返回即可。 题目中说了给定的s为non-empty，但是递归函数处理时s是会变空的，这时候是否直接返回空集？是的返回一个空字符串，为啥要这么做呢？题目中的Output，发现单词之间是有空格，而最后一个单词后面没有空格，所以这个空字符串就起到了标记当前单词是最后一个，那么最后一个单词就不要再加空格了。接着往下看，遍历wordDict数组，如果某个单词是s字符串中的开头单词的话，对后面部分调用递归函数，将结果保存到rem中，然后遍历里面的所有字符串，和当前的单词拼接起来，这里就用到了我们前面说的trick。for循环结束后，记得返回结果res之前建立其和s之间的映射，方便下次使用。

### 代码

```java
class Solution {
    public List<String> wordBreak(String s, List<String> wordDict) {
        Map<String, LinkedList<String>> map = new HashMap<String, LinkedList<String>>();
        return wordBreakHelper(s, wordDict, map);
    }
    private List<String> wordBreakHelper(String s, List<String> wordDict, Map<String, LinkedList<String>> map) {
        if (map.containsKey(s)) {//记忆化的DFS
            return map.get(s);
        }
        LinkedList<String> result = new LinkedList<String>();
        if (s.length() == 0) {
            result.add("");//s递归完后最后返回空串
            return result;
        }
        for (String word : wordDict) {
            if (s.startsWith(word)) {
                List<String> subList = wordBreakHelper(s.substring(word.length()), wordDict, map);
                for (String sub : subList) {
                    result.add(word + (sub.isEmpty() ? "" : " ") + sub);//检查是否是返回一组结果的最后一个单词
                }
            }
        }
        map.put(s, result);//将目前“子串”s和对应result做映射，方便搜索
        return result;
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

## 279 - Perfect Squares

### 原题概述

Given a positive integer n, find the least number of perfect square numbers \(for example, `1, 4, 9, 16, ...`\) which sum to n.

**Example 1:**

```text
Input: n = 12
Output: 3 
Explanation: 12 = 4 + 4 + 4.
```

**Example 2:**

```text
Input: n = 13
Output: 2
Explanation: 13 = 4 + 9.
```

### 题意和分析

给一个正整数，求最少由几个完全平方数组成，考察[四平方和定理](https://zh.wikipedia.org/wiki/%E5%9B%9B%E5%B9%B3%E6%96%B9%E5%92%8C%E5%AE%9A%E7%90%86)（没听说过）。

如果是DP的办法，其中一个办法是建立一个一维数组， dp\[i\]表示组成i这个数字需要的最少的的平方数，DP方程为dp\[i\] = min\(dp\[i - x^2\] + 1 for all x\)，因为x有很多种选择，需要选一个最小的；初始化第一个值为0，在循环里计算选择x和不选择x，谁的次数少。

每次增加一个dp数组的长度，里面那个for循环一次循环结束就算好下一个数由几个完全平方数组成，直到增加到第n+1个，返回即可；

DP的效率不是很高， 一个更加高效的办法是，根据四平方和定理，任意一个正整数均可表示为4个整数的平方和（4个，3个，2个或者1个），首先将数字化简一下，由于一个数如果含有因子4，那么我们可以把4都去掉，并不影响结果，比如2和8,3和12等等，返回的结果是相同的。还有一个可以化简的地方就是，如果一个数除以8余7的话，那么这个数肯定是由4个完全平方数组成。经过两次化简，一个很大的数有可能就会变得很小了，大大减少了运算时间，接下来将化简后的数拆为两个平方数之和，如果拆成功了那么就会返回1或2，因为其中一个平方数可能为0. \(由于输入的n是正整数，所以不存在两个平方数均为0的情况\)，然后看a和b是否为正整数，都为正整数的话返回2，只有一个是正整数的话就返回1。

### 代码

DP - 1，比较直观的做法，运行速度比较慢，因为这里Math.sqrt\(\)的复杂度是Quisilinear O\(logn\)，时间复杂度O\(n^2\*logn\)，可以查看Leetcode 69 - sqrt\(x\)

```java
class Solution {
    public int numSquares(int n) {
        int[] dp = new int[n + 1];
        Arrays.fill(dp, n);//dp[i]表示i这个数所需的最小平方数,最大不会超过n个
        dp[0] = 0; //0不会被任何正整数的平方数构成
        
        for (int i = 1; i <= n; i++) {
            for (int x = 1; x <= Math.sqrt(i); x++) {
                dp[i] = Math.min(dp[i], dp[i - x * x] + 1); //选x这个数和不选x这个数看谁少
            }
        }
        return dp[n];
    }
}
```

 DP - 2，只改了下sqrt为乘法，时间复杂度O\(n^2\)，leetcode上运行速度快了很多

```java
class Solution {
    public int numSquares(int n) {
        int[] dp = new int[n + 1];
        Arrays.fill(dp, n);//dp[i]表示i这个数所需的最小平方数,最大不会超过n个
        dp[0] = 0; //0不会被任何正整数的平方数构成
        
        for (int i = 1; i <= n; i++) {
            for (int x = 1; x * x <= i; x++) {
                dp[i] = Math.min(dp[i], dp[i - x * x] + 1); //选x这个数和不选x这个数看谁少
            }
        }
        return dp[n];
    }
}
```

四平方和的方法，时间复杂度O\(n\)，最快

```java
class Solution {
    public int numSquares(int n) {
        //两步化简
        while (n % 4 == 0) n /= 4;
        if (n % 8 == 7) return 4;

        int a = -1, b = (int)Math.sqrt(n);
        n -= b * b;
        b += b + 1;
        while (a <= b) {
            if (n < 0) {
                n += b -= 2;
            } else if (n > 0) {
                n -= a += 2;
            } else {
                return a < 0 ? 1 : 2;
            }
        }
        return 3;
    }
}
```

## 312 - Burst Balloons

### 原题概述

Given `n` balloons, indexed from `0` to `n-1`. Each balloon is painted with a number on it represented by array `nums`. You are asked to burst all the balloons. If the you burst balloon `i` you will get `nums[left] * nums[i] * nums[right]` coins. Here `left` and `right` are adjacent indices of `i`. After the burst, the `left` and `right` then becomes adjacent.

Find the maximum coins you can collect by bursting the balloons wisely.

**Note:**

* You may imagine `nums[-1] = nums[n] = 1`. They are not real therefore you can not burst them.
* 0 ≤ `n` ≤ 500, 0 ≤ `nums[i]` ≤ 100

**Example:**

```text
Input: [3,1,5,8]
Output: 167 
Explanation: nums = [3,1,5,8] --> [3,5,8] -->   [3,8]   -->  [8]  --> []
             coins =  3*1*5      +  3*5*8    +  1*3*8      + 1*8*1   = 167
```

### 题意和分析

 一个气球对应一个数字，每次打爆一个气球，得到的金币数是被打爆的气球的数字和两边的气球上的数字相乘，如果旁边没有气球了，则按1算，以此类推，求能得到的最多金币数。又是求极值，那自然用DP，维护一个二维动态数组dp，其中dp\[i\]\[j\]表示区间\[i,j\]中的打爆所有气球能得到的最多金币。题目中说明了边界情况，当气球周围没有气球的时候，旁边的数字按1算，这样可以在区间\[i,j\]两边各填充一个1，递归式：

```text
dp[i][j] = max(dp[i][j], nums[i - 1]*nums[k]*nums[j + 1] + dp[i][k - 1] + dp[k + 1][j]) 
其中i ≤ k ≤ j
```

因为每次气球打爆后就不计算了，所以只是更新了dp这个二维数组的右上三角区域（左下三角区域为空），我们最终要返回的值存在dp\[0\]\[n-1\]中，其中n是两端添加1之前数组nums的个数。

更加详细的解释 [https://leetcode.com/problems/burst-balloons/discuss/76228/Share-some-analysis-and-explanations](https://leetcode.com/problems/burst-balloons/discuss/76228/Share-some-analysis-and-explanations)

### 代码

```java
class Solution {
    public int maxCoins(int[] nums) {
        int[] newNums = new int[nums.length + 2];
        int n = 1;
        for (int x : nums) {
            if (x > 0) {
                newNums[n] = x;
                n++;
            }
        }
        newNums[0] = newNums[n++] = 1;//两边各加一个1，注意这里末尾的元素赋值为1后再自增一下

        int[][] dp = new int[n][n];
        for (int k = 2; k < n; k++) {
            for (int left = 0; left < n - k; left++) {
                int right = left + k;
                for (int i = left + 1; i < right; i++) {
                    dp[left][right] = Math.max(dp[left][right],
                            newNums[left]*newNums[i]*newNums[right] + dp[left][i] + dp[i][right]);
                }
            }
        }
        return dp[0][n-1];
    }
}
```

## 120 - Triangle 

### 原题概述

Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.

For example, given the following triangle

```text
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```

The minimum path sum from top to bottom is `11` \(i.e., **2** + **3** + **5** + **1** = 11\).

**Note:**

Bonus point if you are able to do this using only _O_\(_n_\) extra space, where _n_ is the total number of rows in the triangle.

### 题意和分析

从三角形的第二行开始，triangle\[i\]\[j\] = min\(triangle\[i - 1\]\[j - 1\], triangle\[i - 1\]\[j\]\)，然后两边的数字直接赋值上一行中找到的最小值，bonus说最好空间复杂度为_O_\(_n_\)，因此在原来的triangle数组上改动；

一种更好的办法是自底向上，逐个遍历triagnle的最后一行，对于每个数字，将它与它之后的元素比较，选择较小的+triangle数组上面一行相邻位置的元素作为新的元素，一层一层往上扫描，最小的数字冒泡到了前面，返回最前面的元素。

### 代码

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int size = triangle.size();
        for (int i = size - 2; i >= 0; i--) {//size-1是最后一行
            for (int j = 0; j <= i; j++) {//对当前层的数字逐个遍历
                int self = triangle.get(i).get(j);
                int result = Math.min(triangle.get(i+1).get(j) + self, triangle.get(i+1).get(j+1)+self);
                triangle.get(i).set(j, result);
            }
        }
        return triangle.get(0).get(0);//最小的path sum在最前面
    }
}
```

## 62 - Unique Paths

### 原题概述

### 题意和分析

### 代码

## **64 Minimum Path Sum** 

### **原题概述**

Given a _m_ x _n_ grid filled with non-negative numbers, find a path from top left to bottom right which _minimizes_ the sum of all numbers along its path.

**Note:** You can only move either down or right at any point in time.

**Example:**

```text
Input:
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
Output: 7
Explanation: Because the path 1→3→1→1→1 minimizes the sum.
```

### **题意和分析**

给一个二维数组，从左上到右下找一条经过的元素加起来最小的path，返回所有元素加起来的和。全局最优，用DP， dp\[i\]\[j\] = grid\[i\]\[j\] + min\(dp\[i - 1\]\[j\]\)，所有路径经过的元素之和等于当前元素的值加上上一个可到达的元素的总和最小值。

### **代码**

```java
class Solution {
    public int minPathSum(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }
        int m = grid.length, n = grid[0].length;
        int[][] dp = new int[m][n];

        //初始化第一个值
        dp[0][0] = grid[0][0];
        //初始化第一行和第一列
        for (int i = 1; i < m; i++) {
            dp[i][0] = grid[i][0] + dp[i-1][0];//当前值+上一步的最小值
        }
        for (int j = 1; j < n; j++) {
            dp[0][j] = grid[0][j] + dp[0][j-1];
        }

        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = grid[i][j] + Math.min(dp[i-1][j], dp[i][j-1]);
            }
        }
        return dp[m-1][n-1];
    }
}
```

## **123 Best Time to Buy and Sell Stock III** 

### **原题概述**

Say you have an array for which the _i_th element is the price of a given stock on day _i_.

Design an algorithm to find the maximum profit. You may complete at most _two_ transactions.

**Note:** You may not engage in multiple transactions at the same time \(i.e., you must sell the stock before you buy again\).

**Example 1:**

```text
Input: [3,3,5,0,0,3,1,4]
Output: 6
Explanation: Buy on day 4 (price = 0) and sell on day 6 (price = 3), profit = 3-0 = 3.
             Then buy on day 7 (price = 1) and sell on day 8 (price = 4), profit = 4-1 = 3.
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

### **题意和分析**

跟前面两道题相比，这道题是要求只交易两次，找到最大利润。

这个解法很清晰明确，这里就不造轮子了，[https://blog.csdn.net/linhuanmars/article/details/23236995](https://blog.csdn.net/linhuanmars/article/details/23236995)

> 这里我们先解释最多可以进行k次交易的算法，然后最多进行两次我们只需要把k取成2即可。我们还是使用“局部最优和全局最优解法”。我们维护两种量，一个是当前到达第i天，最多进行j次交易的情况下，最好的利润是多少（global\[i\]\[j\]）；另一个是当前到达第i天，最多可进行j次交易，并且最后一次交易在当天卖出的的情况下，最好的利润是多少（local\[i\]\[j\]）。下面我们来看递推式，全局的比较简单， 
>
> global\[i\]\[j\]=max\(local\[i\]\[j\],global\[i-1\]\[j\]\)， 
>
> 也就是去当前局部最好的，和过往全局最好的中大的那个（因为最后一次交易如果包含当前天一定在局部最好的里面，否则一定在过往全局最优的里面）。对于局部变量的维护，递推式是 
>
> local\[i\]\[j\]=max\(global\[i-1\]\[j-1\]+max\(diff,0\), local\[i-1\]\[j\]+diff\)，
>
> 也就是看两个量，第一个是全局到i-1天进行j-1次交易，然后加上今天的交易，如果今天是赚钱的话（也就是前面只要j-1次交易，最后一次交易取当前天）；第二个量则是取local第i-1天j次交易，然后加上今天的差值（这里因为local\[i-1\]\[j\]比如包含第i-1天卖出的交易，所以现在变成第i天卖出，并不会增加交易次数，而且这里无论diff是不是大于0都一定要加上，因为否则就不满足local\[i\]\[j\]必须在最后一天卖出的条件了）。 
>
> 上面的算法中对于天数需要一次扫描，而每次要对交易次数进行递推式求解，所以时间复杂度是O\(n\*k\)，如果是最多进行两次交易，那么复杂度还是O\(n\)。空间上只需要维护当天数据皆可以，所以是O\(k\)，当k=2，则是O\(1\)。

### **代码**

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length == 0) {
            return 0;
        }
        int[] local = new int[3];
        int[] global = new int[3];

        for (int i = 0; i < prices.length - 1; i++) {
            int diff = prices[i+1] - prices[i];
            for (int j = 2; j >=1; j--) {
                local[j] = Math.max(global[j-1] + (diff > 0 ? diff : 0), local[j] + diff);
                global[j] = Math.max(local[j], global[j]);
            }
        }
        return global[2];
    }
}
```

## 647 Palindromic Substrings

### 原题概述

 Given a string, your task is to count how many palindromic substrings in this string.

 The substrings with different start indexes or end indexes are counted as different substrings even they consist of same characters.

**Example 1:**  


```text
Input: "abc"
Output: 3
Explanation: Three palindromic strings: "a", "b", "c".
```

**Example 2:**  


```text
Input: "aaa"
Output: 6
Explanation: Six palindromic strings: "a", "a", "a", "aa", "aa", "aaa".
```

**Note:**  


1. The input string length won't exceed 1000.

### 题意和分析

要求返回所有子回文子串的数量，可以按照奇偶来计算；也可以利用DP的办法（[https://leetcode.com/problems/palindromic-substrings/discuss/105707/Java-DP-solution-based-on-longest-palindromic-substring](https://leetcode.com/problems/palindromic-substrings/discuss/105707/Java-DP-solution-based-on-longest-palindromic-substring)）

### 代码

直接计算

```java
class Solution {
    int count = 0;
    public int countSubstrings(String s) {
        if (s.isEmpty()) {
            return 0;
        }

        for (int i = 0; i < s.length(); i++) {
            countSubstrings(s, i, i);//以当前字符自己为中心，奇数
            countSubstrings(s, i, i + 1);//以当前和后面的字符为中心，偶数
        }
        return count;
    }

    private void countSubstrings(String s, int left, int right) {
        while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            count++;
            left--;
            right++;
        }
    }
}
```

DP



## 91 Decode Ways

### 原题概述

A message containing letters from `A-Z` is being encoded to numbers using the following mapping:

```text
'A' -> 1
'B' -> 2
...
'Z' -> 26
```

Given a **non-empty** string containing only digits, determine the total number of ways to decode it.

**Example 1:**

```text
Input: "12"
Output: 2
Explanation: It could be decoded as "AB" (1 2) or "L" (12).
```

**Example 2:**

```text
Input: "226"
Output: 3
Explanation: It could be decoded as "BZ" (2 26), "VF" (22 6), or "BBF" (2 2 6).
```

### 题意和分析

这道题用DP来做，是对DP的一种直接实现，空间复杂度可以是O\(n\)或者O\(1\)，两种方法都要掌握。

### 代码

时间O\(n\)，空间O\(n\)

```java
class Solution {
    public int numDecodings(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }
        int len = s.length();
        int[] dp = new int[len + 1];//空的时候返回为0
        dp[0] = 1;//至少有一种
        dp[1] = s.charAt(0) != '0' ? 1 : 0;//判断头字母是否为0，不等于0就为1

        for (int i = 2; i <= len; i++) {
            //可能是一个数或者两个数
            int first = Integer.valueOf(s.substring(i - 1, i));
            int second = Integer.valueOf(s.substring(i - 2, i));
            if (first >= 1 && first <= 9) {//取一个数，只要不是0，那肯定可以组成一个字符
                dp[i] += dp[i - 1];
            }
            if (second >= 10 && second <= 26) {//取两个数
                dp[i] += dp[i - 2];
            }
        }
        return dp[len];
    }
}
```

时间O\(n\)，空间O\(1\)，只需保存上一种方法的dp\[i - 1\]和dp\[i - 2\]两个数即可，因此只用两个变量就行了。

```java
class Solution {
    public int numDecodings(String s) {
        if (s == null || s.length() == 0 || s.charAt(0) == '0') {
            return 0;
        }

        int c1 = 1;
        int c2 = 1;
        for (int i = 1; i < s.length(); i++) {
            if (s.charAt(i) == '0') {
                c1 = 0;
            }
            if (s.charAt(i - 1) == '1' || (s.charAt(i - 1) == '2' && s.charAt(i) <= '6')) {//如果第一位是2，那第二位自然不能大于6
                c1 = c1 + c2;
                c2 = c1 - c2;
            } else {
                c2 = c1;
            }
        }
        return c1;
    }
}
```

## 300 Longest Increasing Subsequence

### 原题概述

Given an unsorted array of integers, find the length of longest increasing subsequence.

**Example:**

```text
Input: [10,9,2,5,3,7,101,18]
Output: 4 
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4. 
```

**Note:**

* There may be more than one LIS combination, it is only necessary for you to return the length.
* Your algorithm should run in O\(n2\) complexity.

**Follow up:** Could you improve it to O\(n log n\) time complexity?

### 题意和分析

这是经典题，如果采取暴力法来解复杂度得是排列组合的阶乘级。求最优解一般可以用动态规划，这是动态规划中的区间型题目，首先状态定义，dp\[i\]表示数组中第i个元素为止该位置的最长子序列长度（从0开始），那么dp\[i+1\]就是在i后面多一个元素的情况下，如果nums\[i\] &lt; nums\[i+1\] \(表示在i+1这个位置上，最长子序列的长度有可能会有更长的情况了\)，那么这时候检查dp\[i+1\]是否小于dp\[i\]+1（在i+1这个位置上，最长子序列的长度出现更长的情况了，这时候更新i+1位置dp\[i+1\]的值），否则不动，最后找出各个位置最大的子序列的数值，时间复杂度，用递推，外面套一层循环表示每个元素都计算下，里面套一个循环表示在外层元素的基础上，根据递推公式和之前保存的值，计算最长子序列，所以为O\(n^2\)。

这道题还要求优化到O\(nlogn\)，这时候需要用到二分查找，（参考[https://leetcode.com/problems/longest-increasing-subsequence/discuss/74824/JavaPython-Binary-search-O\(nlogn\)-time-with-explanation](https://leetcode.com/problems/longest-increasing-subsequence/discuss/74824/JavaPython-Binary-search-O%28nlogn%29-time-with-explanation)），这是一个

### 代码

DP O\(n^2）

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int len = nums.length;
        if (len == 1) {
            return 1;
        }
        int[] regionLongest = new int[len];//regionLongest[k]表示到第k+1个元素的最长升序长度；regionLongest[0]表示初始状态（0个元素的时候，依然为1）, regionLongest[1]表示第0个元素的最长长度，regionLongest[len]表示最后一个元素的最长长度
        Arrays.fill(regionLongest, 1);//每个位置初始值为1，起码包含自己为1
        
        for (int i = 1; i <= len - 1; i++) {//从第二个元素开始，让其之前有元素
            for (int j = 0; j < i; j++) {//i之前的状态
                if (nums[j] < nums[i]) {
                    if (regionLongest[j] + 1 > regionLongest[i]) {//DP中每个位置存储的值由上一个状态+1组成，因为nums[j] < nums[j]这个条件成立才可能来判断是否要+1（升序多一个），要不然regionLongest[i]就保持不变
                        regionLongest[i] = regionLongest[j] + 1;
                    }
                }
            }
        }
        
        //找到数组中所有位置的最大升序的数字
        int globalLongest = 0;
        for (int longest : regionLongest) {
            globalLongest = Math.max(globalLongest, longest);
        }
        return globalLongest;
    }
}
```

二分查找 O\(nlogn\)



## 322 Coin Change

### 原题

You are given coins of different denominations and a total amount of money amount. Write a function to compute the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return `-1`.

**Example 1:**

```text
Input: coins = [1, 2, 5], amount = 11
Output: 3 
Explanation: 11 = 5 + 5 + 1
```

**Example 2:**

```text
Input: coins = [2], amount = 3
Output: -1
```

**Note**:  
You may assume that you have an infinite number of each kind of coin.

### 分析

一维DP，top down和bottom up两种方法，需要特别掌握bottom up递推方法和相应的空间优化。

DP方程和递归树



![](../../.gitbook/assets/image%20%2856%29.png)

![](../../.gitbook/assets/image%20%2857%29.png)

### 由此得到的DP方程

```text
dp[i]= min(dp[i], dp[i − coins[j]] + 1)
```

### 代码

topdown

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] memo = new int[amount + 1]; // memo[i]表示记录凑齐i块钱需要的coin数目
        Arrays.fill(memo, -100); // 随便来个负数做初始化，-1是找不到硬币组合需要返回的值，所以这里用-1之外任何负数代表初始化没有组合
        memo[0] = 0; // 0块钱需要0个硬币

        return coinChange(coins, amount, memo);
    }
    private int coinChange(int[] coins, int amount, int[] memo) {
        if (amount < 0) { // not valid
            return -1;
        }
        if (amount == 0) { // 已完成
            return 0;
        }
        if (memo[amount] != -100) { //剪枝，之前已经计算过
            return memo[amount];
        }
        
        for (int coin : coins) {// 对比选择当前硬币coins[i]或者不选择
            if (coin > amount) {
                continue;
            }
            int option = coinChange(coins, amount - coin, memo);
            if (option == -1) { // 递归返回的是not valid，直接跳过本次组合
                continue;
            }
            
            if (memo[amount] == -100 || option + 1 < memo[amount]) {
                memo[amount] = option + 1;
            }
        }
        if (memo[amount] == -100) {
            memo[amount] = -1;
        }
        return memo[amount];
    }
}
```

bottom up

```java
class Solution {
   public int coinChange(int[] coins, int amount) {
       int[] dp = new int[amount + 1];
       for (int i = 1; i <= amount; i++) {
            dp[i] = amount + 1;
        }
       // 直接套方程
        for (int i = 1; i <= amount; i++) {
            for (int coin : coins) {
                if (coin <= i) {
                    dp[i] = Math.min(dp[i], dp[i - coin] + 1); // 选择当前coin或者不选择
                }
            }
        }
        return dp[amount] > amount ? -1 : dp[amount];
    }

}
```




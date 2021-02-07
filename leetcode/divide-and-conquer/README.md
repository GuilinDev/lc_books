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

DP

```java
class Solution {
    // dp[i][j][k]: i - day, j - tranaction times, k - whether have stocks on hand
    public int maxProfit(int[] prices) {
        int maxProfix = 0;
        if (prices == null || prices.length == 0) {
            return maxProfix;
        }
        int len = prices.length;
        int[] dp = new int[len];
        dp[0] = 0; // profit为0，尚未开始买卖
        int minPrice = prices[0];
        
        for (int i = 1; i < len; i++) {
            dp[i] = Math.max(dp[i - 1], prices[i] - minPrice);
            minPrice = Math.min(minPrice, prices[i]);
        }
        return dp[len - 1];
    }
}
```

状态压缩

```java
class Solution {
    public int maxProfit(int[] prices) {
        int minPriceStock = Integer.MAX_VALUE, maxProfit = 0;
        for (int price : prices) {
            minPriceStock = Math.min(minPriceStock, price);//找到子问题中最小的buy的价格
            maxProfit = Math.max(maxProfit, price - minPriceStock);//找到最小buy价格下最大的利润
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

使用额外的一个二维数组dp，其中dp\[i\]\[j\]表示到当前位置不同的走法的个数，然后可以得到递推式为: dp\[i\]\[j\] = dp\[i - 1\]\[j\] + dp\[i\]\[j - 1\]。

考虑状态压缩， 每次只需要用到上一行当前列，以及前一列当前行的信息，只需要用一个一维数组存上一行的信息即可，然后扫过来依次更替掉上一行对应列的信息即可，一行一行的刷新，因此可以简化为使用一维数组dp。 时间复杂度是O\(m\*n\)，空间复杂度O\(n\)。

### 代码

动态规划

```java
class Solution {
    //dp[m][n] = dp[m - 1][n] + dp[m][n - 1] 
    public int uniquePaths(int m, int n) {
        if (m < 0 || n < 0) {
            return 0;
        }
        int[][] dp = new int[m][n];
        
        
        // 第一行和第一列的初始化，只有1种走法
        for (int k = 0; k < m; k++) {
            dp[k][0] = 1;
        }
        for (int k = 0; k < n; k++) {
            dp[0][k] = 1;
        }
        
        // 对非第一行和非第一列进行填表
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        
        return dp[m - 1][n - 1];
    }
}
```

考虑状态压缩，滚动列

```java
class Solution {
    //dp[m][n] = dp[m - 1][n] + dp[m][n - 1] 
    public int uniquePaths(int m, int n) {
        if (m < 0 || n < 0) {
            return 0;
        }
        int[] dp = new int[n];
        
        dp[0] = 1; // 只记录列，下面的里面循环中每次一列的最顶上初始化为1
        
        for (int i = 0; i < m; i++) {
            for (int j = 1; j < n; j++) { // 第一列已经初始化
                // dp[j] += dp[j - 1]；
                // 这里的dp[j]相当于从左边复制路径数复制过来
                // 而dp[j - 1]相当于把上面的路径数加过来
                dp[j] += dp[j - 1]; 
            }
        }
        
        return dp[n - 1]; //返回最后一列的最后一个
    }
}
```

可以想象，考虑状态压缩的情况下，滚动行也是同样的道理

```java
class Solution {
    //dp[m][n] = dp[m - 1][n] + dp[m][n - 1] 
    public int uniquePaths(int m, int n) {
        if (m < 0 || n < 0) {
            return 0;
        }
        int[] dp = new int[m];
        
        dp[0] = 1;  //这里就是每次行的最左边在每轮里循环中为1
                
        for (int j = 0; j < n; j++) {
            for (int i = 1; i < m; i++) {
                dp[i] += dp[i - 1]; // 滚动行往下
            }
        }
        
        return dp[m - 1]; // 返回最后一行的最后一个
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

## 63 Unique Path II

### 题目

A robot is located at the top-left corner of a _m_ x _n_ grid \(marked 'Start' in the diagram below\).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid \(marked 'Finish' in the diagram below\).

Now consider if some obstacles are added to the grids. How many unique paths would there be?

![](https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png)

An obstacle and empty space is marked as `1` and `0` respectively in the grid.

**Note:** _m_ and _n_ will be at most 100.

**Example 1:**

```text
Input:
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
Output: 2
Explanation:
There is one obstacle in the middle of the 3x3 grid above.
There are two ways to reach the bottom-right corner:
1. Right -> Right -> Down -> Down
2. Down -> Down -> Right -> Right
```

### 分析

这道题是给定的二维数组里面可能会有障碍物，用1表示，依然是62题DP的做法和考虑状态压缩，只是在dp过程或者滚动数组过程中，需要检查一下当前路径是否有障碍物，如果有，将当前状态（格子）设为0，表示当前状态格子对右边和下边的格子不做贡献。

### 代码

正常DP

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid == null || obstacleGrid.length == 0 || obstacleGrid[0].length == 0) {
            return 0;
        }
        
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;
        
        int[][] dp = new int[m][n];
        
        // 初始化
        for (int k = 0; k < m; k++) {
            // 当前格子为障碍物，或者上边的状态为0，堵路了，当前状态为0
            if (obstacleGrid[k][0] == 1 || (k > 0 && dp[k - 1][0] == 0)) {
                dp[k][0] = 0;
            } else {
                dp[k][0] = 1;
            }
        }        
        for (int k = 0; k < n; k++) {
            // 当前格子为障碍物，或者左边的状态为0，堵路了，当前状态为0
            if (obstacleGrid[0][k] == 1 || (k > 0 && dp[0][k - 1] == 0)) {
                dp[0][k] = 0;
            } else {
                dp[0][k] = 1;
            }
        }
        
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (obstacleGrid[i][j] == 1) { // 障碍物
                    dp[i][j] = 0; //当前状态贡献为0
                } else { // 正常找路
                    dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
                }
            }
        }
        return dp[m - 1][n - 1];
    }
}
```

把初始化写到一个循环里面，上面分开写比较好懂

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid == null || obstacleGrid.length == 0 || obstacleGrid[0].length == 0) {
            return 0;
        }    
        
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;
        
        int[][] dp = new int[m][n];
        
        // 这里如果先分开对第一行和第一列进行初始化不太好写，因为有可能存在某一行/列全是障碍的情况，
        // 这时候应该一条路都没有，如果先初始化第一行或第一列，需要检查所在行或列是否都为障碍
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (obstacleGrid[i][j] == 1) { // 障碍物
                    dp[i][j] = 0; //当前状态贡献为0
                } else { // 正常找路
                    if (i == 0 && j == 0) { //第一个格子进行初始化
                        dp[i][j] = 1;
                    } else if (i == 0) { // 第一行
                        if (dp[i][j - 1] == 0) { // 上一个是障碍
                            dp[i][j] = 0;
                        } else {
                            dp[i][j] = 1;
                        }
                    } else if (j == 0) { // 第一列
                        if (dp[i - 1][j] == 0) {
                            dp[i][j] = 0;
                        } else {
                            dp[i][j] = 1;
                        }
                    } else {
                        dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
                    }
                }
            }
        }
        return dp[m - 1][n - 1];
    }
}
```

DP，反过来从右下到左上写也是一样，锻炼下逆向思维

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid == null || obstacleGrid.length == 0 || obstacleGrid[0].length == 0) {
            return 0;
        }       
        
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;
        
        int[][] dp = new int[m][n];
        
        // 这里如果先对第一行和第一列进行初始化不太好写，因为有可能存在某一行/列全是障碍的情况，
        // 这时候应该一条路都没有，如果先初始化第一行或第一列，需要检查所在行或列是否都为障碍
        
        for (int i = m - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                if (obstacleGrid[i][j] == 1) {//有障碍物，注意这个条件要先检查
                    dp[i][j] = 0;
                } else {
                    if (i == m - 1 && j == n - 1) {//在右下角的终点处，初始化为一条路
                        dp[i][j] = 1;
                    } else if (i == m - 1) {//初始化一列
                        if (dp[i][j + 1] == 0) {//最后一列如果下面的路被堵住了，上面的格子均为0条路径
                            dp[i][j] = 0;
                        } else {
                            dp[i][j] = 1;
                        }
                    } else if(j == n - 1) {//初始化一行
                        if (dp[i + 1][j] == 0) {//最后一行如果右面的路被堵住了，左面的格子均为0条路径
                            dp[i][j] = 0;
                        } else {
                            dp[i][j] = 1;
                        }
                    } else{
                        dp[i][j] = dp[i + 1][j] + dp[i][j + 1];
                    }
                }
            }
        }
        return dp[0][0];
    }
}
```

DP + 状态压缩，滚动行比滚动列较好维护一些

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid == null || obstacleGrid.length == 0 || obstacleGrid[0].length == 0) {
            return 0;
        }
        
        int cols = obstacleGrid[0].length; //计算一行中的列数
        
        int[] dp = new int[cols]; // hold一行的数组，该一维数组每次向下滚动一行
        dp[0] = 1; // 初始化第一行为1，下面的检查中可能会改变为0
        
        for (int[] oneRow : obstacleGrid) {
            for (int j = 0; j < cols; j++) { // 这里从j=0第一列开始检查
                if (oneRow[j] == 1) {//当前行的障碍物
                    dp[j] = 0;
                } else if (j > 0) {//这里第一行已经初始化，不要再检查
                    dp[j] += dp[j - 1]; //状态压缩前上面的值已经滚动到当前行，这时候只需加上左边值即可
                }
            }
        }
        return dp[cols - 1]; //最后一行的最后一个元素
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

### 题目

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

### 分析

1\) DP

再次复习一下动态规划的思考和思路，其实DP源自于一个很自然的想法，就拿这道题来说，假如需要判断"onetwothreefour"这一个字符串能不能满足条件，很自然的想法就是： 如果"onetwothree"这一段可以拆分，再加上four如果也可以，那就行了； 或者 如果"onetwothre"这一段可以拆分，再加上efour如果也可以，那也行了； 这其实已经抓住了动态规划的最核心的东西了，换成式子来表达，就是

```java
dp["onetwothreefour"] = dp["onetwothree"这一段] && 判断一下"four"
dp["onetwothreefour"] = dp["onetwothre"这一段] && 判断一下"efour"
```

这道题就搞定了。

动态规划的基本操作： 

1. 定义dp数组 - dp\[i\] 表示 s 前 i 个字符组成的字符串 s\[0..i-1\] 是否能被拆分成若干个字典中出现的单词
2. 初始化 - dp\[0\]=true 表示空串且合法
3. 转化公式 - **dp\[i\]=dp\[j\] && check\(s\[j..i−1\]\)**，其中check\(s\[j..i−1\]\) 表示子串 s\[j..i-1\]是否出现在字典中
4. 搞定！ 

DP的思路雷同，但每道题的实现有差别，比如这道题的遍历顺序稍微有点窍门，就是：对于检查一个字符串是否出现在给定的字符串列表里一般可以考虑哈希表（hashmap或hashset）来快速判断，同时也可以做一些简单的剪枝，正着枚举和倒着枚举都可以。

2\) Trie

### 代码

DP不剪枝，好理解，时间O\(n^2\)，空间O\(n\)

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        Set<String> wordSet = new HashSet<>(wordDict); // set的contains方法效率比list的contains方法效率高        
        int len = s.length();
        boolean[] dp = new boolean[len + 1]; // 多一个0个字符的字符串情况
        dp[0] = true; // 初始化
        
        for (int i = 1; i <= len; i++) {
            for (int j = i; j >= 0; j--) { //这里从0开始也可以，但反着查比较符合自然想法
                if (dp[j] && wordSet.contains(s.substring(j, i))) {
                    dp[i] = true;
                    break; // 当前为true必须跳出
                }
            }
        }
        return dp[len];
    }
}

```

DP + 剪枝，当超过字典中最大字符串的长度时，就不再检查是否存在了，复杂度一样

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        Set<String> wordSet = new HashSet<>(wordDict); // set的contains方法效率比list的contains方法效率高        
        int len = s.length();
        boolean[] dp = new boolean[len + 1]; // 多一个0个字符的字符串情况
        dp[0] = true; // 初始化
        
        // 找到wordDict中最大长度的单词
        int maxWordLen = 0;
        for (String word : wordDict) {
            maxWordLen = Math.max(maxWordLen, word.length());
        }
        
        for (int i = 1; i <= len; i++) {
            /**
            第二个循环，这里j在i左边，从i位置开始，这里需要计算的是[i - maxlength, i]这个区间里有没有满足dp[j] && wordSet.contains(s.substring(j,i)条件的情况,
            这里j不能从0开始往右直到i，否则计算的是[0, i - maxlength]这个区间，和要求的是反着的。
            仔细思考一下：相当于j + maxWordLen >= i，所有从i开始向左比较好枚举
            */
            for (int j = i; j >= 0 && j >= i - maxWordLen; j--) { 
                if (dp[j] && wordSet.contains(s.substring(j, i))) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[len];
    }
}
```

Trie + 记忆化，需要构建Trie，较麻烦，不要求

```java
class Solution {
    class Trie {
        class Node {
            boolean present;
            HashMap<Character, Node> next;

            Node() {
                next = new HashMap<>();
            }
        }

        private Node root;

        Trie() {
            root = new Node();
        }

        public void insert(String word) {
            Node p = root;
            for (int i = 0; i < word.length(); i++) {
                char c = word.charAt(i);
                if (!p.next.containsKey(c)) {
                    p.next.put(c, new Node());
                }
                p = p.next.get(c);
                if (i == word.length()-1) {
                    p.present = true;
                }
            }
        }

        public boolean contains(String word) {
            Node p = root;
            for (int i = 0; i < word.length(); i++) {
                char c = word.charAt(i);
                if (!p.next.containsKey(c)) return false;
                p = p.next.get(c);
                if (i == word.length()-1) {
                    if (p.present) return true;
                }
            }
            return false;
        }

        public boolean startsWith(String prefix) {
            Node p = root;
            for (int i = 0; i < prefix.length(); i++) {
                char c = prefix.charAt(i);
                if (!p.next.containsKey(c)) return false;
                p = p.next.get(c);
            }
            return true;
        }
    }

    public boolean wordBreak(String s, List<String> wordDict) {
        Trie trie = new Trie();
        for (String word : wordDict) {
            trie.insert(word);
        }
        HashMap<String, Boolean> memo = new HashMap<>();
        return canBreak(s, trie, memo);
    }

    private boolean canBreak(String s, Trie trie, HashMap<String, Boolean> memo) {
        if (memo.containsKey(s)) return memo.get(s);
        for (int i = 1; i <= s.length(); i++) {
            if (trie.startsWith(s.substring(0, i))) {
                if (trie.contains(s.substring(0, i))) {
                    if (canBreak(s.substring(i, s.length()), trie, memo)) {
                        memo.put(s, true);
                        return true;
                    }
                }
                if (i == s.length()) {
                    if (!trie.contains(s)) {
                        memo.put(s, false);
                        return false;
                    }
                }
            }
            else {
                memo.put(s, false);
                return false;
            }
        }
        memo.put(s, true);
        return true;
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

        //为第一行和第一列赋值，0个word1和word2比较，以及word1和0个word2比较
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

这样看更方便一点：

```java
[
[2],
[3,4],
[6,5,7],
[4,1,8,3]
]

相邻结点：与(i, j) 点相邻的结点为 (i + 1, j) 和 (i + 1, j + 1)。

```

定义 f\(i, j\) 为 \(i, j\) 点到底边的最小路径和，则易知递归求解式为:

f\(i, j\) = triangle\[i\]\[j\] + min\(f\(i + 1, j\), f\(i + 1, j + 1\)\) 

所以，朴素的递归解法如下：

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        return  dfs(triangle, 0, 0); //将三角形和元素的x，y坐标传入
    }

    private int dfs(List<List<Integer>> triangle, int i, int j) {
        if (i == triangle.size()) {
            return 0;
        }
        return triangle.get(i).get(j) + Math.min(dfs(triangle, i + 1, j), dfs(triangle, i + 1, j + 1));
    }
}
```

1\) 朴素的递归解法自然是可以做记忆化的自顶向下，可以同样大小的数据结构来做记忆化搜索。

时间复杂度：O\(N^2\)。

空间复杂度：O\(N^2\)。

2\) DP，dp\[i\]\[j\] 表示从点 \(i, j\) 到底边的最小路径和，所以最后返回dp\[0\]\[0\]。

状态转移方程：dp\[i\]\[j\] = triangle\[i\]\[j\] + min\(dp\[i + 1\]\[j\],dp\[i + 1\]\[j + 1\]\)

时间复杂度：O\(N^2\)。

空间复杂度：O\(N^2\)。

3\) 考虑状态压缩，上述实际递推中，计算 dp\[i\]\[j\] 时，只用到了下一行的 dp\[i + 1\]\[j\] 和 dp\[i + 1\]\[j + 1\]。 因此 dp 数组不需要定义 N 行，只要定义 1 行就可以。 所以稍微修改一下上述代码，将 i 所在的维度去掉（如下），就可以将 O\(N^2\) 的空间复杂度优化成 O\(N\)。

4\) 这道题从最底下开始算也可以，逐个遍历triagnle的最后一行，对于每个数字，将它与它之后的元素比较，选择较小的+triangle数组上面一行相邻位置的元素作为新的元素，一层一层往上扫描，最小的数字冒泡到了前面，返回最前面的元素。从三角形的第二行开始，状态方程为triangle\[i\]\[j\] = min\(triangle\[i - 1\]\[j - 1\], triangle\[i - 1\]\[j\]\)，然后两边的数字直接赋值上一行中找到的最小值。

bonus说最好空间复杂度为_O_\(_n_\)，其实还可以在原来的triangle数组上改动。

### 代码

自顶向下递归

```java
class Solution {
    Integer[][] memo;
    public int minimumTotal(List<List<Integer>> triangle) {
        memo = new Integer[triangle.size()][triangle.size()];
        return  dfs(triangle, 0, 0);
    }

    private int dfs(List<List<Integer>> triangle, int i, int j) {
        if (i == triangle.size()) {
            return 0;
        }
        if (memo[i][j] != null) {
            return memo[i][j];
        }
        return memo[i][j] = triangle.get(i).get(j) + Math.min(dfs(triangle, i + 1, j), dfs(triangle, i + 1, j + 1));
    }
}
```

自底向上DP

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int size = triangle.size();
        // dp[i][j] 表示从点 (i, j) 到底边的最小路径和。
        int[][] dp = new int[size + 1][size + 1];
        
        // 从三角形的最后一行开始递推。
        for (int i = size - 1; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
                dp[i][j] = triangle.get(i).get(j) + Math.min(dp[i + 1][j + 1], dp[i + 1][j]);
            }
        }
        return dp[0][0];
    }
}
```

考虑状态压缩

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int size = triangle.size();
        int[] dp = new int[size + 1];
        for (int i = size - 1; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
                dp[j] = triangle.get(i).get(j) + Math.min(dp[j], dp[j + 1]);
            }
        }
        return dp[0];
    }
}
```

另一种状态转移方程，triangle\[i\]\[j\] = min\(triangle\[i - 1\]\[j - 1\], triangle\[i - 1\]\[j\]\)，并在原数据结构上修改

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int size = triangle.size();
        for (int i = size - 2; i >= 0; i--) {//size-1是最后一行
            for (int j = 0; j <= i; j++) {//对当前层的数字逐个遍历
                int self = triangle.get(i).get(j);
                int result = Math.min(triangle.get(i+1).get(j) + self, triangle.get(i+1).get(j+1) + self);
                triangle.get(i).set(j, result);
            }
        }
        return triangle.get(0).get(0);//最小的path sum在最前面
    }
}
```

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

确定状态机的办法

```java
class Solution {
     public int maxProfit(int[] prices) {
        if (prices.length == 0) {
            return 0;
        }
        long[][][] dp = new long[prices.length][3][2];
        dp[0][1][0] = Integer.MIN_VALUE;
        dp[0][1][1] = -prices[0];
        dp[0][2][0] = Integer.MIN_VALUE;
        dp[0][2][1] = Integer.MIN_VALUE;
        for (int i = 1; i < prices.length; i++) {
            dp[i][1][0] =
                    Math.max(dp[i - 1][1][0], dp[i - 1][1][1] + prices[i]);
            dp[i][1][1] =
                    Math.max(dp[i-1][1][1], -prices[i]);
            dp[i][2][0] =
                    Math.max(dp[i-1][2][0], dp[i-1][2][1] + prices[i]);
            dp[i][2][1] =
                    Math.max(dp[i-1][2][1], dp[i-1][1][0] - prices[i]);
        }
        int res = (int) Math.max(dp[prices.length - 1][2][0], dp[prices.length - 1][1][0]);
        return res < 0 ? 0 : res;
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

这道题要掌握DP，是对DP的一种直接实现，空间复杂度可以是O\(n\)或者O\(1\)，两种方法都要掌握。

此题问题可转化为长度n的字符串s一共有多少种解码方法。那么其子问题是长度为n - 1的字符串s1有多少种解码方法。一直到长度为1的字符串有多少种解码方法（长度为1时是边界情况，只要首字符不为0，就算一种方法）。从左到右和从右到左都可以。

 定义：**dp\[i\]：从索引0向右到i（或从len - 1向左到i）的字符串s的子串所能够解码的总数**。

### 代码

普通递归的方式，只考虑当前这一个字符，或者考虑当前字符和后一个字符（两个字符）的情况，存在着很多重复计算，时间O\(2^n\)，空间O\(n\)

```java
class Solution {
    public int numDecodings(String s) {
        if (s.isEmpty()) { 
            return 0;
        }
        char[] chs = s.toCharArray();
        return decode(chs, 0);
    }
    private int decode(char[] chs, int index) {
        int len = chs.length;
        if (index >= len) { // 已经处理到了最后一个字符，只有一种解法
            return 1;
        }
        if (chs[index] == '0') { // leading zero和单独一个zero都不能解码
            return 0;
        }
        
        /**
        只考虑当前一个字符的情况，直接利用后面的解法
        比如考虑226，假设26的解法有m种，在前面多加一个2后，相当于在解码的前面再多加一个字符'B'
        解码结果只是长得不一样，解码的结果依然是m种
        */
        int count = decode(chs, index + 1); 
        
        /**
        考虑两个字符的情况，第一个字符如果是‘1’或‘2’，需要<=26
        当前解法m种，index + 2的解法为k种，所以总共m + k种，需要累加        
        */
        if (index < len -1 && (chs[index] == '1' || (chs[index] == '2' && chs[index + 1] <= '6'))) {
            count += decode(chs, index + 2);
        }
        
        return count;
    }
}

```

在上面递归的解法上，每次计算出值准备return的时候，做一个缓存，进行记忆化搜索，剪枝后的效率不错，时间O\(n\)，空间O\(n\)

```java
class Solution {
    public int numDecodings(String s) {
        if (s.isEmpty()) {
            return 0;
        }
        char[] chs = s.toCharArray();
        int[] cache = new int[chs.length];
        Arrays.fill(cache, -1);
        return decode(chs, 0, cache);
    }
    private int decode(char[] chs, int index, int[] cache) { 
        int len = chs.length;
        if (index >= len) { //这里超界不用缓存
            return 1;
        }
        
        if (cache[index] != -1) {
            // 0. cache，已经算过，剪枝
            return cache[index];
        }
        
        if (chs[index] == '0') {
            // 缓存处1
            cache[index] = 0;
            return 0;
        }

        
        int count = decode(chs, index + 1, cache);

        if (index < len -1 && (chs[index] == '1' || (chs[index] == '2' && chs[index + 1] <= '6'))) {
            count += decode(chs, index + 2, cache);
        }

        // 缓存处2
        cache[index] = count;

        return count;
    }
}
```

DP解法，时间O\(n\)，空间O\(n\)，为了和上面递归解法一致，就从右到左解法，DP其实就是迭代版的记忆化

```java
class Solution {
    public int numDecodings(String s) {
        if (s.isEmpty()) {
            return 0;
        }
        int len = s.length();
        int[] dp = new int[len + 1];
        
        dp[len] = 1; // 从右到左比较好迭代，最后一个字符一种解码法
        for (int i = len - 1; i >= 0; i--) {
            if (s.charAt(i) == '0') {
                dp[i] = 0;
            } else {
                // 只考虑一个字符
                dp[i] = dp[i + 1]; 
                
                // 考虑两个字符
                if (i < len - 1 && (s.charAt(i) == '1' ||(s.charAt(i) == '2' && s.charAt(i + 1) <= '6'))) {
                    dp[i] += dp[i + 2];
                }
            }
        }
        return dp[0];
    }
}
```

DP + 状态压缩，时间O\(n\)，空间O\(1\)，只需保存上一种方法的dp\[i - 1\]和dp\[i - 2\]两个数即可，状态压缩，只用两个变量就行。

```java
class Solution {
    public int numDecodings(String s) {
        if (s.isEmpty()) {
            return 0;
        }
        int len = s.length();
        
        //用两个变量来替代普通DP解法的dp[i]和dp[i + 1]
        int iValue = 1, iValue1 = 0;
        
        for (int i = len - 1; i >= 0; i--) {
            int curr;
            if (s.charAt(i) == '0') { // 一个字符
                curr  = 0;
            } else { // 两个字符
                curr = iValue;
                if (i < len - 1 && (s.charAt(i) == '1' ||(s.charAt(i) == '2' && s.charAt(i + 1) <= '6'))) {
                    curr += iValue1;
                }
            }
            // 滚动更新
            iValue1 = iValue;
            iValue = curr;
        }
        return iValue; 
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

这是经典题，如果采取暴力法来解复杂度得是排列组合的阶乘级。求最优解一般可以用动态规划，这是动态规划中的区间型题目，

首先状态定义，dp\[i\]表示数组中第i个元素为止该位置的最长子序列长度（从0开始），那么dp\[i+1\]就是在i后面多一个元素的情况下，如果nums\[i\] &lt; nums\[i+1\] \(表示在i+1这个位置上，最长子序列的长度有可能会有更长的情况了\)，那么这时候检查dp\[i+1\]是否小于dp\[i\]+1（表示在i+1这个位置上，最长子序列的长度出现更长的情况了，这时候更新i+1位置dp\[i+1\]的值），否则不动，最后找出各个位置最大的子序列的数值，时间复杂度，用递推，外面套一层循环表示每个元素都计算下，里面套一个循环表示在外层元素的基础上，根据递推公式和之前保存的值，计算最长子序列，所以为O\(n^2\)。

#### 1\) DP

![](../../.gitbook/assets/image%20%28103%29.png)

1. **定义状态** 由于一个子序列一定会以一个数结尾，于是将状态定义成：dp\[i\] 表示以 nums\[i\] 结尾的「上升子序列」的长度。注意：这个定义中 nums\[i\] 必须被选取，且必须是这个子序列的最后一个元素。
2. **考虑状态转移方程** 

   * 遍历到 `nums[i]` 时，需要把下标 `i` 之前的所有的数都看一遍；
   *  只要 `nums[i]` 严格大于在它位置之前的某个数，那么 `nums[i]` 就可以接在这个数后面形成一个更长的上升子序列；
   *  `dp[i]` 就等于下标 `i` 之前**严格**小于 `nums[i]` 的状态值的最大者 +1， 也就是下标 `i` 之前严格小于 `nums[i]` 的所有状态值中的最大者 + 1。

3. **初始化** -  `dp[i] = 1`，1 个字符显然是长度为 1 的上升子序列。
4. **输出** 
   * 这里要注意，不能返回最后一个状态值；
   *  根据定义，最后一个状态值只是以 `nums[len - 1]` 结尾的「上升子序列」的长度；
   *  状态数组 `dp` 的最大值才是最后要输出的值。
5. **考虑状态压缩**
   * 遍历到一个新数的时候，之前所有的状态值都需要保留，因此无法压缩。



#### 2\) [同时用到贪心算法和二分查找](https://leetcode-cn.com/problems/longest-increasing-subsequence/solution/dong-tai-gui-hua-er-fen-cha-zhao-tan-xin-suan-fa-p/)

这道题还要求优化到O\(nlogn\)，这时候需要用到二分查找。

*  依然是着眼于一个上升子序列的**结尾的元素；**
*  **如果已经得到的上升子序列的结尾的数越小，遍历的时候后面接上一个数，就会有更大的可能性构成一个更长的上升子序列\(贪心法\)**；
*  既然结尾越小越好，可以记录**在长度固定的情况下，结尾最小的那个元素的数值**，这样定义也是为了方便得到「状态转移方程」。

 为了与上面解法的状态区分，这里将状态数组命名为 `tail`。

1. **定义新状态（特别重要）**
   *  **`tail[i]` 表示长度为 `i + 1` 的**所有**上升子序列的结尾的最小值。**
   * tail\[0\] 表示长度为 1的所有上升子序列中，结尾最小的那个元素的数值，以题目中的示例为例 \[10, 9, 2, 5, 3, 7, 101, 18\] 中，容易发现长度为 2 的所有上升子序列中，结尾最小的是子序列 \[2, 3\] ，因此 tail\[1\] = 3
   *  下标索引和长度有一个 `1` 的偏差；
2. **思考状态转移方程**

   *  从直觉上看，数组 `tail` 也是一个严格上升数组

   证明：反证法\(略\)，算法步骤：

   1.  设置一个数组 `tail`，初始时为空；
   2. 在遍历数组 nums 的过程中，每来一个新数 num，如果这个数**严格大于\(不包括等于\)**有序数组 tail 的最后一个元素，就把 num 放在有序数组 tail 的后面，否则进入第 3 点；
   3.  在有序数组 `tail` 中查找第 1 个等于大于 `num` 的那个数，试图让它变小；

      *  如果有序数组 `tail` 中存在**等于** `num` 的元素，什么都不做，因为以 `num` 结尾的最短的「上升子序列」已经存在；
      *  如果有序数组 `tail` 中存在**大于** `num` 的元素，找到第 1 个，让它变小，这样我们就找到了一个**结尾更小**的**相同长度**的上升子序列。

      说明：再看一下数组 tail\[i\] 的定义：长度为 i + 1 的所有最长上升子序列的结尾的最小值。因此，在遍历的过程中，我们试图让一个大的值变小是合理的。这一步可以认为是「贪心算法」，总是做出在当前看来最好的选择，当前「最好的选择」是：当前只让让第 1 个严格大于 nums\[i\] 的数变小，变成 nums\[i\]，这一步操作是“无后效性”的。由于是在有序数组中的操作，因此可以使用「二分查找算法」。

   4. 遍历新的数 num ，先尝试上述第 2 点，第 2 点行不通则执行第 3 点，直到遍历完整个数组 nums，最终有序数组 tail 的长度，就是所求的“最长上升子序列”的长度。

3. **初始化** -  `dp[0] = nums[0]`，在只有 1 个元素的情况下，它当然是长度为 1 并且结尾最小的元素。
4. **输出** - 数组 tail 的长度，上文其实也已经说了，还是依据定义，tail\[i\] 表示长度固定为 i + 1 的所有「上升子序列」的结尾元素中最小的那个，长度最多就是数组 tail 的长度。
5. **考虑状态压缩** - 同样无法压缩

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
                    //DP中每个位置存储的值由上一个状态+1组成，因为nums[j] < nums[j]这个条件成立才可能来判断是否要+1（升序多一个），要不然regionLongest[i]就保持不变
                    regionLongest[i] = Math.max(regionLongest[i], regionLongest[j] + 1);
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

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int len = nums.length;
        if (len <= 1) {
            return len;
        }

        // tail 数组的定义：长度为 i + 1 的上升子序列的末尾最小是几
        int[] tail = new int[len];
        // 遍历第 1 个数，直接放在有序数组 tail 的开头
        tail[0] = nums[0];
        // end 表示有序数组 tail 的最后一个已经赋值元素的索引
        int end = 0;

        for (int i = 1; i < len; i++) {
            // 【逻辑 1】比 tail 数组实际有效的末尾的那个元素还大
            if (nums[i] > tail[end]) {
                // 直接添加在那个元素的后面，所以 end 先加 1
                end++;
                tail[end] = nums[i];
            } else {
                // 使用二分查找法，在有序数组 tail 中
                // 找到第 1 个大于等于 nums[i] 的元素，尝试让那个元素更小
                int left = 0;
                int right = end;
                while (left < right) {
                    // 选左中位数不是偶然，而是有原因的，原因请见 LeetCode 第 35 题题解
                    // int mid = left + (right - left) / 2;
                    int mid = left + ((right - left) >>> 1);
                    if (tail[mid] < nums[i]) {
                        // 中位数肯定不是要找的数，把它写在分支的前面
                        left = mid + 1;
                    } else {
                        right = mid;
                    }
                }
                // 走到这里是因为 【逻辑 1】 的反面，因此一定能找到第 1 个大于等于 nums[i] 的元素
                // 因此，无需再单独判断
                tail[left] = nums[i];
            }
            // 调试方法
            // printArray(nums[i], tail);
        }
        // 此时 end 是有序数组 tail 最后一个元素的索引
        // 题目要求返回的是长度，因此 +1 后返回
        end++;
        return end;
    }
}
```

## 674 Longest Continuous Increasing Subsequence

### 题目

Given an unsorted array of integers, find the length of longest `continuous` increasing subsequence \(subarray\).

**Example 1:**  


```text
Input: [1,3,5,4,7]
Output: 3
Explanation: The longest continuous increasing subsequence is [1,3,5], its length is 3. 
Even though [1,3,5,7] is also an increasing subsequence, it's not a continuous one where 5 and 7 are separated by 4. 
```

**Example 2:**  


```text
Input: [2,2,2,2,2]
Output: 1
Explanation: The longest continuous increasing subsequence is [2], its length is 1. 
```

**Note:** Length of the array will not exceed 10,000.

### 分析

这道题可以按直观做，但拿来练习DP非常不错。

1） 状态定义 - dp\[i\] - 到i位置时最长的上升子序列 

2） 初始化 dp\[0\] = 1, dp\[i\]初始化都是1 

3） 转移方程 dp\[i\] = dp\[i - 1\] + 1 if nums\[i\] &gt; nums\[i - 1\] 

                           = 1 if nums\[i\] &lt;= nums\[i - 1\]

4） 考虑状态压缩，可以

### 代码

DP

```java
class Solution {
    // dp[i] - 到i位置时最长的上升子序列
    // dp[0] = 1, 所以dp[i]初始化都是1
    // dp[i] = dp[i - 1] + 1 if nums[i] > nums[i - 1]
    //       = 1 if nums[i] <= nums[i - 1]
    public int findLengthOfLCIS(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int len = nums.length;
        int[] dp = new int[len];
        Arrays.fill(dp, 1);
        
        for (int i = 1; i < len; i++) {
            if (nums[i] > nums[i - 1]) {
                dp[i] = dp[i - 1] + 1;
            }
        }
        
        //遍历每个位置，找到最大值，这步可以在前面的for循环中完成
        int result = 0;
        for (int m : dp) {
            result = Math.max(result, m);
        }
        
        return result;
    }
}
```

状态压缩

```java
class Solution {
    public int findLengthOfLCIS(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int len = nums.length;
        int maxL = 1; // 最大长度
        int prev = 1; // 前一个状态
        int curr = 1; // 当前状态
        
        for (int i = 1; i < len; i++) {
            if (nums[i] > nums[i - 1]) {
                curr = prev + 1;
                maxL = Math.max(maxL, curr);
                prev = curr;
            } else {// 重新开始计算
                curr = 1;
                prev = 1; 
            }
        }
        return maxL;
    }
}
```

直观做法，类似状态压缩

```java
class Solution {
    public int findLengthOfLCIS(int[] nums) {
        int res = 0, count = 0;
        for (int i = 0; i < nums.length; i++) {
            if (i == 0 || nums[i-1] < nums[i]) {//不是第一位，找到前面和现在的关系
                count++;
                res = Math.max(res, count);
            } else {
                count = 1;
            }
        }
        return res;
    }
}
```

## 1143 Longest Common Subsequence

### 题目

Given two strings `text1` and `text2`, return the length of their longest common subsequence.

A _subsequence_ of a string is a new string generated from the original string with some characters\(can be none\) deleted without changing the relative order of the remaining characters. \(eg, "ace" is a subsequence of "abcde" while "aec" is not\). A _common subsequence_ of two strings is a subsequence that is common to both strings.

If there is no common subsequence, return 0.

**Example 1:**

```text
Input: text1 = "abcde", text2 = "ace" 
Output: 3  
Explanation: The longest common subsequence is "ace" and its length is 3.
```

**Example 2:**

```text
Input: text1 = "abc", text2 = "abc"
Output: 3
Explanation: The longest common subsequence is "abc" and its length is 3.
```

**Example 3:**

```text
Input: text1 = "abc", text2 = "def"
Output: 0
Explanation: There is no such common subsequence, so the result is 0.
```

**Constraints:**

* `1 <= text1.length <= 1000`
* `1 <= text2.length <= 1000`
* The input strings consist of lowercase English characters only.

### 分析

这个是典型的二维DP，可以看作以下的DP表格：

![](../../.gitbook/assets/image%20%28115%29.png)

1） 状态定义，为了方便理解此表，暂时认为索引是从 1 开始的，待会的代码中只要稍作调整即可。其中，dp\[i\]\[j\] 的含义是：对于 s1\[1..i\] 和 s2\[1..j\]，它们的 LCS 长度是 dp\[i\]\[j\]。

比如上图的例子，d\[2\]\[4\] 的含义就是：对于 "ac" 和 "babc"，它们的 LCS 长度是 2。最终想得到的答案应该是 dp\[3\]\[6\]。

2） 初始化base case，专门让索引为 0 的行和列表示空串，dp\[0\]\[..\] 和 dp\[..\]\[0\] 都应该初始化为 0，这就是 base case。比如说，按照刚才 dp 数组的定义，dp\[0\]\[3\]=0 的含义是：对于字符串 "" 和 "bab"，其 LCS 的长度为 0。因为有一个字符串是空串，它们的最长公共子序列的长度显然应该是 0。

注：二维DP表中，初始化的两条边在数组创建后本身就为0。

3）这是动态规划最难的一步，不过这种字符串问题的套路都差不多。状态转移说简单些就是做选择，比如说这个问题，是求 s1 和 s2 的最长公共子序列，不妨称这个子序列为 lcs。那么对于 s1 和 s2 中的每个字符，有什么选择？很简单，两种选择，要么在 lcs 中，要么不在。

![](../../.gitbook/assets/image%20%28117%29.png)

这个「在」和「不在」就是选择，关键是，应该如何选择呢？这个需要动点脑筋：如果某个字符应该在 lcs 中，那么这个字符肯定同时存在于 s1 和 s2 中，因为 lcs 是最长公共子序列嘛。所以本题的思路是这样：

* 用两个指针 i 和 j 从后往前遍历 s1 和 s2，如果 s1\[i\]==s2\[j\]，那么这个字符一定在 lcs 中；
* 否则的话，s1\[i\] 和 s2\[j\] 这两个字符至少有一个不在 lcs 中，需要丢弃一个

 对于第一种情况，找到一个 `lcs` 中的字符，同时将 `i` `j` 向前移动一位，并给 `lcs` 的长度加一；对于后者，则尝试两种情况，取更大的结果。相应代码（以从后向前为例，这道题从前往后也可以的）：

```java
for i in range(1, m + 1):
    for j in range(1, n + 1):
        if str1[i - 1] == str2[j - 1]:
            # 找到一个 lcs 中的字符
            dp[i][j] = 1 + dp[i-1][j-1]
        else:
            dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
return dp[-1][-1]

```

 对于 `s1[i]` 和 `s2[j]` 不相等的情况，**至少有一个**字符不在 `lcs` 中，会不会两个字符都不在呢？比如下面这种情况：

![](../../.gitbook/assets/image%20%28116%29.png)

相应转移方程的代码为：

```java
if str1[i - 1] == str2[j - 1]:
    # ...
else: # 考虑有三种情况
    dp[i][j] = max(dp[i-1][j], 
                   dp[i][j-1],
                   dp[i-1][j-1])

```

 其实这样改也能得到正确答案，只是多此一举，因为 `dp[i-1][j-1]` 永远是三者中最小的，max 根本不可能取到它。

4）考虑状态压缩，两个字符串各占一维，无法状态压缩。

### 代码

```java
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        int len1 = text1.length();
        int len2 = text2.length();
        int[][] dp = new int[len1 + 1][len2 + 1]; // 多一位是包含0个字符和0个字符的情况，初始两条为0
        
        for (int i = 1; i <= len1; i++) {
            for (int j = 1; j <= len2; j++) {
                if (text1.charAt(i - 1) == text2.charAt(j - 1)) { // index为0的第一个字符，为dp数组中的第2个（从1开始），dp数组中的第一个元素对应空字符串
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    // 谁能让lcs更大，就听谁的，dp[i - 1][j - 1]不用考虑
                    dp[i][j] = Math.max(dp[i][j - 1], dp[i - 1][j]);
                }
            }
        }
        return dp[len1][len2];
    }
}
```

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

这道题是完全背包问题的变种，采用动态规划维护一个二维数组dp，dp\[i\]\[j\]表示从第1个元素到第i个元素累计总金额为j时的最少硬币数量，递推公式为：

```text
dp[i]= min(dp[i], dp[i − coins[j]] + 1)
```

意思是当零钱为 i 元需要的最少硬币数 = min \(啥也不干 or 第i元 - 硬币中所有出现的可能小于i元的硬币的出现次数 + 1\)。

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
            dp[i] = amount + 1; //初始化每个位置，不能到达，dp[0]为凑满0元，可以达到
        }
       // 转移方程
        for (int i = 1; i <= amount; i++) {
            for (int coin : coins) {
                if (coin <= i) {
                    dp[i] = Math.min(dp[i], dp[i - coin] + 1); // 选择当前coin或者不选择
                }
            }
        }
        if (dp[amount] > amount) { // 当前amount没有组合的硬币
            return -1;
        }
       return dp[amount];
    }
}
```

## 329 Longest Increasing Path in a Matrix

### 题目

Given an integer matrix, find the length of the longest increasing path.

From each cell, you can either move to four directions: left, right, up or down. You may NOT move diagonally or move outside of the boundary \(i.e. wrap-around is not allowed\).

**Example 1:**

```text
Input: nums = 
[
  [9,9,4],
  [6,6,8],
  [2,1,1]
] 
Output: 4 
Explanation: The longest increasing path is [1, 2, 6, 9].
```

**Example 2:**

```text
Input: nums = 
[
  [3,4,5],
  [3,2,6],
  [2,2,1]
] 
Output: 4 
Explanation: The longest increasing path is [3, 4, 5, 6]. Moving diagonally is not allowed.
```

### 分析

从任何单元格开始的最长递增路径，可以对全部单元格进行深度优先搜索。每个单元格可以看作图 G 中的一个定点：

> 寻找有向图 G 中的最长路径。

暴力解法：对每一个定点进行DFS搜索，看哪个最长，其实是斐波那契数列朴素递归的高级版本，时间复杂度 ：O\(2^m+n \) - 其中m和n分别是矩阵的长宽。

所以有两个优化方向，自顶向下的递归和自底向上的递推，递归就是DFS的基础上进行剪枝，利用记忆化搜索的方式；递推就是动态规划。

1）自顶向下，用一个集合来避免一次深度优先搜索中的重复访问。在搜索过程中，如果未计算过单元格的结果，再计算计算并将其缓存；否则，直接从缓存中获取。

**时间复杂度** : O\(mn\)。 每个顶点/单元格均计算一次，且只被计算一次。每条边也均计算一次并只计算一次。总时间复杂度是 O\(V+E\)。V是顶点总数，E 是边总数。本问题中，O\(V\) = O\(mn\)，O\(E\) = O\(4V\) = O\(mn\)。

**空间复杂度** : O\(mn\)。缓存决定了空间复杂度。

2）自底向上，定义从单元格 \(i, j\) 开始的最长递增路径为函数f\(i,j\)，那么状态转移方程：

`f(i,j) = max{f(x,y)∣(x,y) is a neighbor of(i,j) and matrix[x][y]>matrix[i][j]}+1`

现在还需要初始化，也就是想要让动态规划有效，如果问题 B 依赖于问题 A 的结果，就必须确保问题 A 比问题 B先计算。比如斐波那契数列，F\(0\)=1,F\(1\)=1,F\(n\)=F\(n−1\)+F\(n−2\)，自然顺序就是正确的计算顺序，被依赖者总会先被计算。这种依赖顺序的术语是“拓扑顺序”或“拓扑排序”：

> 对有向无环图的拓扑排序是顶点的一个线性排序，使得对于任何有向边 \(u, v\)\(u,v\)，顶点 uu 都在 顶点 vv 的前面。

对于当前问题中，拓扑顺序并不简单自然。没有类似背包问题中矩阵的值，无法知道两个邻居 A 和 B 的依赖关系。作为预处理，必须显式执行拓扑排序。之后，可以按照存储的拓扑顺序使用状态转移函数动态地解决问题。

有多种实现拓扑排序的方法。这里使用的是一种被称为“剥洋葱”的方法。其思路是在一个有向无环图中，会有一些不依赖于其他顶点的顶点\(入度为0\)，称为“叶子”。我们将这些叶子放在一个列表中（它们之间的内部排序不重要），然后将它们从图中移除。移除之后，会产生新的“叶子”。重复以上过程，就像一层一层一层地拨开洋葱的心。最后，列表中就会存储有效的拓扑排序。

在本问题中，因为想要求出在整个图中最长的路径，也就是“洋葱”的层总数。因此，我们可以在“剥离”的期间计算层数，在不调用动态规划的情况下返回计数。

**时间复杂度** : O\(mn\)。拓扑排序的时间复杂度为 O\(V+E\) = O\(mn\)。V 是顶点总数，E 是边总数。本问题中，O\(V\) = O\(mn\)，O\(E\) = O\(4V\) = O\(mn\)。

**空间复杂度** : O\(mn\)。我们需要存储出度和每层的叶子。

### 代码

记忆化搜索

```java
class Solution {
    private static final int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}}; // 四个方向
    private int m, n;

    public int longestIncreasingPath(int[][] matrix) {
        if (matrix.length == 0) {
            return 0;
        }
        m = matrix.length; 
        n = matrix[0].length;
        int[][] cache = new int[m][n];
        int result = 0;
        
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                result = Math.max(result, dfs(matrix, i, j, cache));
            }
        }
        
        return result;
    }

    private int dfs(int[][] matrix, int i, int j, int[][] cache) {
        if (cache[i][j] != 0) { // 剪枝
            return cache[i][j];
        }
        for (int[] d : dirs) {
            int x = i + d[0], y = j + d[1];
            if (0 <= x && x < m && 0 <= y && y < n && matrix[x][y] > matrix[i][j]) {
                cache[i][j] = Math.max(cache[i][j], dfs(matrix, x, y, cache));
            }
        }
        cache[i][j]++; //需要加上本身
        return cache[i][j]; 
    }
}
```

基于拓扑排序的DP

```java
class Solution {
    private static final int[][] dir = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
    private int m, n;
    public int longestIncreasingPath(int[][] grid) {
        int m = grid.length;
        if (m == 0) {
            return 0;
        }
        int n = grid[0].length;
        
        // padding the matrix with zero as boundaries
        // assuming all positive integer, otherwise use INT_MIN as boundaries
        int[][] matrix = new int[m + 2][n + 2];
        
        for (int i = 0; i < m; ++i)
            System.arraycopy(grid[i], 0, matrix[i + 1], 1, n);

        // 计算出度
        int[][] outdegree = new int[m + 2][n + 2];
        for (int i = 1; i <= m; ++i)
            for (int j = 1; j <= n; ++j)
                for (int[] d: dir)
                    if (matrix[i][j] < matrix[i + d[0]][j + d[1]])
                        outdegree[i][j]++;

        // 找到出度为0的“叶子”节点
        n += 2;
        m += 2;
        List<int[]> leaves = new ArrayList<>();
        for (int i = 1; i < m - 1; ++i) {
            for (int j = 1; j < n - 1; ++j) {
                if (outdegree[i][j] == 0) {
                    leaves.add(new int[]{i, j});
                }
            }
        }

        // remove leaves level by level in topological order
        int height = 0;
        while (!leaves.isEmpty()) {
            height++;
            List<int[]> newLeaves = new ArrayList<>();
            for (int[] node : leaves) {
                for (int[] d:dir) {
                    int x = node[0] + d[0];
                    int y = node[1] + d[1];
                    if (matrix[node[0]][node[1]] > matrix[x][y]) {
                        if (--outdegree[x][y] == 0) {
                            newLeaves.add(new int[]{x, y});
                        }
                    }
                }
            }
            leaves = newLeaves;
        }
        return height;
    }
}

```

## 198 - House Robber

### 题目

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security system connected and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight **without alerting the police**.

**Example 1:**

```text
Input: nums = [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
             Total amount you can rob = 1 + 3 = 4.
```

**Example 2:**

```text
Input: nums = [2,7,9,3,1]
Output: 12
Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
             Total amount you can rob = 2 + 9 + 1 = 12.
```

**Constraints:**

* `0 <= nums.length <= 100`
* `0 <= nums[i] <= 400`

### 分析

牢记DP四个步骤：

1\) 状态定义 - dp\[i\]表示第i个元素的最大值（从1开始），也就是有多少个房子时候的最大值 

2\) 初始化 - dp\[0\] 没有房子，dp\[1\] 一个房子 

3\) 递推式，在普通的动态规划题目中，99% 的情况我们都不需要用到备忘录方法，所以我们最好坚持用自底向上的 dp 数组。写出递推方程 dp\[i\] = max\(dp\[i - 1\], dp\[i - 2\] + nums\[i - 1\]\) - i - 1是当前元素 

4） 考虑状态压缩

### 代码

DP

```java
class Solution {
    /** 1. 定义：dp[i] - 到当前元素时候的最大数量，从1开始，dp[0]表示没有元素
        2. 初始化：当 k=0 时，没有房子，所以 f(0) = 0。当 k=1 时，只有一个房子，偷这个房子即可，所以 f(1) = nums[0]
        3. 递推方程：dp[i] = max(dp[i - 1], dp[i - 2] + nums[i])，dp[i - 1]表示当前元素不选，dp[i - 2] + nums[i]表示当前元素可选
        比如[10, 1 , 1, 5]，就是 选(不选0 vs 选10)-不选(不选10 vs 选1)-选(不选10 vs 选11)-选(不选11 vs 选15)
    */
    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        if (nums.length < 2) {
            return nums[0];
        }
        int len = nums.length;
        int[] dp = new int[len + 1];
        dp[0] = 0;
        dp[1] = nums[0];
        
        for (int i = 2; i <= len; i++) {
            dp[i] = Math.max(dp[i - 1], dp[i - 2] + nums[i - 1]); // i - 1是当前元素，从0个房子开始的
        }
        
        return dp[len];
    }
}
```

状态压缩

```java
class Solution {
    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        if (nums.length < 2) {
            return nums[0];
        }
        int len = nums.length;
        
        int prevPrev = 0;
        int prev = nums[0];
        int curr = 0;
        
        for (int i = 2; i <= len; i++) {
            curr = Math.max(prev, prevPrev + nums[i - 1]);
            
            // 为下一轮更新
            int temp = prev;
            prev = curr;
            prevPrev = temp;
        }
        
        return curr;
    }
}
```

## 213 House Robber II

### 题目

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed. All houses at this place are **arranged in a circle.** That means the first house is the neighbor of the last one. Meanwhile, adjacent houses have security system connected and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight **without alerting the police**.

**Example 1:**

```text
Input: [2,3,2]
Output: 3
Explanation: You cannot rob house 1 (money = 2) and then rob house 3 (money = 2),
             because they are adjacent houses.
```

**Example 2:**

```text
Input: [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
             Total amount you can rob = 1 + 3 = 4.
```

### 分析

跟上一题198唯一的区别是此题中的房间是环状排列的（即首尾相接），而这也是此题的难点。

环状排列意味着第一个房子和最后一个房子中只能选择一个偷窃, 比如说输入数组 `nums=[2,3,2]`，算法返回的结果应该是 3 而不是 4，因为开头和结尾不能同时被抢。首尾房间不能同时被抢，那么只可能有三种不同情况：要么都不被抢；要么第一间房子被抢最后一间不抢；要么最后一间房子被抢第一间不抢。

这三种情况，那种的结果最大，就是最终答案，不过，其实不需要比较三种情况，只要比较情况二和情况三就行了，因为这两种情况对于房子的选择余地比情况一大呀，房子里的钱数都是非负数，所以选择余地大，最优决策结果肯定不会小

* 在不偷窃第一个房子的情况下（即 nums\[1:\]\]），最大金额是 p1​ ； 
* 在不偷窃最后一个房子的情况下（即 nums\[:n−1\]），最大金额是 p2​ 。

综合偷窃最大金额： 为以上两种情况的较大值，即 max\(p1, p2\) 。

1\) 状态定义 - dp\[i\]表示第i个元素的最大值（从1开始），也就是有多少个房子时候的最大值 

2\) 初始化 - dp\[0\] 没有房子，dp\[1\] 一个房子 

3\) 递推式，在普通的动态规划题目中，99% 的情况我们都不需要用到备忘录方法，所以我们最好坚持用自底向上的 dp 数组。写出递推方程 dp\[i\] = max\(dp\[i - 1\], dp\[i - 2\] + nums\[i - 1\]\) - i - 1是当前元素 

4） 考虑状态压缩，dp\[n\] 只与 dp\[n−1\] 和 dp\[n−2\] 有关系，因此可以设两个变量 cur和 pre 交替记录，将空间复杂度降到 O\(1\)。

 **时间复杂度O\(N\)，** **空间复杂度O\(1\)。**

### 代码

DP



状态压缩

```java
class Solution {
    public int rob(int[] nums) {
        int len = nums.length;
        if (len == 1) {
            return nums[0];
        }

        // 0..len-2表示最后一个房子不抢，len-1是最后一个房子
        // 1..len-1表示第一个房子不抢，0是第一个房子
        return Math.max(robRange(nums, 0, len - 2), robRange(nums, 1, len - 1));
    }
    private int robRange(int[] nums, int start, int end) {//[start, end]
        int dp_i_1 = 0, dp_i_2 = 0; //分别表示临近的状态和隔一个房间的状态
        int max = 0; // 记录最大值
        for (int i = end; i >= start; i--) {
            max = Math.max(dp_i_1, nums[i] + dp_i_2);
            dp_i_2 = dp_i_1;
            dp_i_1 = max;
        }
        return max;
    }
}
```

## 727 Minimum Window Subsequence

### 题目

Given strings S and T, find the minimum \(contiguous\) substring W of S, so that T is a subsequence of W.

If there is no such window in S that covers all characters in T, return the empty string "". If there are multiple such minimum-length windows, return the one with the left-most starting index.

Example 1:

```java
Input: 
S = "abcdebdde", T = "bde" 
Output: "bcde" 
Explanation: 
"bcde" is the answer because it occurs before "bdde" which has the same length. 
"deb" is not a smaller window because the elements of T in the window must occur in order.
```

Note:

* All the strings in the input will only contain lowercase letters. 
* The length of S will be in the range \[1, 20000\]. 
* The length of T will be in the range \[1, 100\].

### 分析

DP - 假设 T = 'ab'，当遍历到 S 中的 'b' 时，这时候只需要知道 'b' 之前最近的 'a'，就可以找到一个符合条件的窗口 \[i, j\]，其中 'a' = S\[i\], ..., S\[j\] = 'b'。

比较差的做法是在遍历到 'b' 之后再往前遍历找到最近的 'a'。但对于 'abbb...bb' 这种字符串，这种做法就比较低效了。更好的做法是记住最后一个遇见的 'a'，之后每当遍历到 'b' 时，就根据之前记住的 'a' 来确定窗口。这是简单的例子，如果 T = 'abc'，要怎么拓展呢？在遍历到 S 中的 'c' 时（假设 S\[k\] = 'c'），如果之前已经找到了包含 'ab' 的窗口（假设为 \[i, j\]\)，那么 \[i, k\] 就是一个包含 'abc' 的窗口。

简单来说就是上面描述的这样，先计算包含 T 的前缀子串的窗口，再根据前缀子串窗口不断拓展，找到包含整个字符串的窗口。具体做法：

1. 建立一个二维dp数组，大小为\|S\|\*\|T\|。dp\[i\]\[j\]表示S的\(0~i\)子串和T的\(0~j\)所对应最小W的长度
2. 对S和T中的每个位置进行匹配： 
   1.  如果S\[i\] != T\[j\]，则dp\[i\]\[j\] = dp\[i-1\]\[j\]+1。 
   2.  如果S\[i\] == T\[j\], 则dp\[i\]\[j\] = min\(dp\[i-1\]\[j\]+1, dp\[i-1\]\[j-1\]+1\)
3. 最后遍历dp\[i\]\[len\(T\)\]，求出第一个最小的值ans和ans对应的i，S中从i-ans到i的的子串即为所求

时间复杂度分析：对S和T中的每个位置进行计算，时间复杂度O\(\|S\|∗\|T\|\)

### 代码

```java
class Solution {
    public String minWindow(String S, String T) {
        int lenS = S.length(), lenT = T.length();
        int[][] dp = new int[lenS + 1][lenT + 1];
        for (int j = 1; j <= lenT; j++) {
            dp[0][j] = Integer.MAX_VALUE / 10;
        }

        for (int i = 1; i <= lenS; i++) {
            for (int j = 1; j <= lenT; j++) {
                if (S.charAt(i - 1) != T.charAt(j - 1)) dp[i][j] = dp[i - 1][j] + 1;
                else dp[i][j] = Math.min(dp[i - 1][j] + 1, dp[i - 1][j - 1] + 1);
            }
        }

        int result = Integer.MAX_VALUE, k = -1;
        for (int j = 1; j <= lenS; j++) {
            if (result > dp[j][lenT]) {
                result = dp[j][lenT];
                k = j;
            }
        }
        if (result >= Integer.MAX_VALUE / 10) {
            return "";
        }
        
        return S.substring(k - result, k);
    }
}
 
```

## 887 Super Egg Drop

### 题目

You are given `K` eggs, and you have access to a building with `N` floors from `1` to `N`. 

Each egg is identical in function, and if an egg breaks, you cannot drop it again.

You know that there exists a floor `F` with `0 <= F <= N` such that any egg dropped at a floor higher than `F` will break, and any egg dropped at or below floor `F` will not break.

Each _move_, you may take an egg \(if you have an unbroken one\) and drop it from any floor `X` \(with `1 <= X <= N`\). 

Your goal is to know **with certainty** what the value of `F` is.

What is the minimum number of moves that you need to know with certainty what `F` is, regardless of the initial value of `F`?

1. 
**Example 1:**

```text
Input: K = 1, N = 2
Output: 2
Explanation: 
Drop the egg from floor 1.  If it breaks, we know with certainty that F = 0.
Otherwise, drop the egg from floor 2.  If it breaks, we know with certainty that F = 1.
If it didn't break, then we know with certainty F = 2.
Hence, we needed 2 moves in the worst case to know what F is with certainty.
```

**Example 2:**

```text
Input: K = 2, N = 6
Output: 3
```

**Example 3:**

```text
Input: K = 3, N = 14
Output: 4
```

**Note:**

1. `1 <= K <= 100`
2. `1 <= N <= 10000`

### 分析

若干层楼，若干个鸡蛋，求算出最少的尝试次数，找到鸡蛋恰好摔不碎的那层楼。国内大厂以及谷歌脸书面试都经常考察这道题，只不过他们觉得扔鸡蛋太浪费，改成扔杯子，扔破碗什么的。这道题的解法技巧很多，光动态规划就好几种效率不同的思路，最后还有一种极其高效数学解法。

这里只关注一种效率不算最高，但非常经典的DP解法 - 记忆化搜索。

比方说**现在先不管鸡蛋个数的限制**，有 7 层楼，怎么去找鸡蛋恰好摔碎的那层楼？

最原始的方式就是线性扫描：先在 1 楼扔一下，没碎，再去 2 楼扔一下，没碎，再去 3 楼……

以这种策略，**最坏**情况应该就是我试到第 7 层鸡蛋也没碎（`F = 7`），也就是扔了 7 次鸡蛋，线性时间。

现在再来理解一下什么叫「至少」要扔几次。依然不考虑鸡蛋个数限制，同样是 7 层楼，可以优化策略。

最好的策略是使用二分查找思路，我先去第\(left + right \)/ 2 =  `(1 + 7) / 2 = 4` 层扔一下：

如果碎了说明 `F` 小于 4，就去第 `(1 + 3) / 2 = 2` 层试……

如果没碎说明 `F` 大于等于 4，就去第 `(5 + 7) / 2 = 6` 层试……

以这种策略，**最坏**情况应该是试到第 7 层鸡蛋还没碎（`F = 7`），或者鸡蛋一直碎到第 1 层（`F = 0`）。然而无论那种最坏情况，只需要试 `log7` 向上取整等于 3 次，比刚才尝试 7 次要少，这就是所谓的**至少**要扔几次。这个是对数时间。

所以实际上，如果不限制鸡蛋个数的话，二分思路显然可以得到最少尝试的次数，但问题是，**现在给你了鸡蛋个数的限制 `K`，直接使用二分思路就不行了**。比如说只给 1 个鸡蛋，7 层楼，你敢用二分吗？直接去第 4 层扔一下，如果鸡蛋没碎还好，但如果碎了你就没有鸡蛋继续测试了，无法确定鸡蛋恰好摔不碎的楼层 `F` 了。这种情况下只能用线性扫描的方法，算法返回结果应该是 7。

如果先用二分查找，等到只剩 1 个鸡蛋的时候再执行线性扫描，这样得到的结果是不是就是最少的扔鸡蛋次数呢？并不是，比如说把楼层变高一些，100 层，给你 2 个鸡蛋，你在 50 层扔一下，碎了，那就只能线性扫描 1～49 层了，最坏情况下要扔 50 次，依然是线性扫描。

动态规划步骤：

1） 状态定义，很明显，就是当前拥有的鸡蛋数K和需要测试的楼层数N。随着测试的进行，鸡蛋个数可能减少，楼层的搜索范围会减小，这就是状态的变化。 

2） 状态转移，其实就是去选择哪层楼扔鸡蛋。回顾刚才的线性扫描和二分思路，二分查找每次选择到楼层区间的中间去扔鸡蛋，而线性扫描选择一层层向上测试。不同的选择会造成状态的转移。

有以上两点，可以肯定是个二维的dp数组或者带有两个状态参数的dp函数来表示状态转移；外加一个 for 循环来遍历所有选择，择最优的选择更新结果:

```java
// 当前状态为 (K 个鸡蛋，N 层楼)
// 返回当前这个状态下的最优结果
function dp(K, N):
    int res
    for 1 <= i <= N:
        res = min(res, 这次在第 i 层楼扔鸡蛋)
    return res
```

这段伪码还没有展示递归和状态转移。

3）在第`i`层楼扔了鸡蛋之后，可能出现两种情况：鸡蛋碎了，鸡蛋没碎。**注意，这时候状态转移**：

* **如果鸡蛋碎了**，那么鸡蛋的个数`K`应该减一，搜索的楼层区间应该从`[1..N]`变为`[1..i-1]`共`i-1`层楼；
* **如果鸡蛋没碎**，那么鸡蛋的个数`K`不变，搜索的楼层区间应该从 `[1..N]`变为`[i+1..N]`共`N-i`层楼。
*  在第`i`层楼扔鸡蛋如果没碎，楼层的搜索区间缩小至上面的楼层，是否应该包含第`i`层楼？不必，因为已经包含了。 F 是可以等于 0 的，向上递归后，第`i`层楼其实就相当于第 0 层，可以被取到。

 要求的是**最坏情况**下扔鸡蛋的次数，所以鸡蛋在第`i`层楼碎没碎，取决于那种情况的结果**更大**：

```java
function dp(K, N):
    for 1 <= i <= N:
        // 最坏情况下的最少扔鸡蛋次数
        res = min(res, 
                  max( 
                        dp(K - 1, i - 1), # 碎
                        dp(K, N - i)      # 没碎
                     ) + 1 # 在第 i 楼扔了一次
                 )
    return res
```

递归的 base case 很容易理解：

* 当楼层数`N`等于 0 时，显然不需要扔鸡蛋；
* 当鸡蛋数`K`为 1 时，显然只能线性扫描所有楼层；

### 代码

**动态规划算法的时间复杂度就是子问题个数 × 函数本身的复杂度**。函数本身的复杂度就是忽略递归部分的复杂度，这里`dp`函数中有一个 for 循环，所以函数本身的复杂度是 O\(N\)。子问题个数也就是不同状态组合的总数，显然是两个状态的乘积，也就是 O\(KN\)。

所以算法的总时间复杂度是 O\(K\*N^2\), 空间复杂度为子问题个数，即 O\(KN\)。

```java
class Solution {
    public int superEggDrop(int K, int N) {
        int[][] memo = new int[K + 1][N + 1];
        return helper(K, N, memo);
    }
    private int helper(int eggs, int floors, int[][] memo) {

        if (floors <= 1) {
            return floors;
        }
        if (eggs == 1) {
            return floors;
        }
        
        // 记忆化搜索
        if (memo[eggs][floors] > 0) {
            return memo[eggs][floors];
        }
        
        // 穷举
        int low = 1, high = floors, result = floors;

        while (low < high) {
            int mid = low + (high - low) / 2;

            int left = helper(eggs - 1, mid - 1, memo);
            int right = helper(eggs, floors - mid, memo);

            result = Math.min(result, Math.max(left, right) + 1);

            if (left == right) {// Converge
                break; 
            } else if (left < right) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        memo[eggs][floors] = result; // 记忆化
        
        return result;
    }
}
```

递推

```java
class Solution {
    public int superEggDrop(int K, int N) {
        int[][] dp = new int[N + 1][K + 1];
        int m = 0;
        while (dp[m][K] < N) {
            m++;
            for (int k = 1; k <= K; ++k)
                dp[m][k] = dp[m - 1][k - 1] + dp[m - 1][k] + 1;
        }
        return m;
    }
}
```

## 392 Is Subsequence

### 题目

Given a string **s** and a string **t**, check if **s** is subsequence of **t**.

A subsequence of a string is a new string which is formed from the original string by deleting some \(can be none\) of the characters without disturbing the relative positions of the remaining characters. \(ie, `"ace"` is a subsequence of `"abcde"` while `"aec"` is not\).

**Follow up:**  
If there are lots of incoming S, say S1, S2, ... , Sk where k &gt;= 1B, and you want to check one by one to see if T has its subsequence. In this scenario, how would you change your code?

**Credits:**  
Special thanks to [@pbrother](https://leetcode.com/pbrother/) for adding this problem and creating all test cases.

**Example 1:**

```text
Input: s = "abc", t = "ahbgdc"
Output: true
```

**Example 2:**

```text
Input: s = "axc", t = "ahbgdc"
Output: false
```

**Constraints:**

* `0 <= s.length <= 100`
* `0 <= t.length <= 10^4`
* Both strings consists only of lowercase characters.

### 分析

1）双指针

> 假定当前需要匹配字符 c，而字符 c 在 t 中的位置以 x1 ​和 x2出现（x1 ​&lt; x2 ​），那么贪心取x 1 ​是最优解，因为 x2 ​后面能取到的字符，x1 ​也都能取到，并且通过x1 ​与 x2 ​之间的可选字符，更有希望能匹配成功

2）考虑前面的双指针的做法，可以注意到有大量的时间用于在 t 中找到下一个匹配字符。可以预处理出对于 t 的每一个位置，从该位置开始往后每一个字符第一次出现的位置。这样，就不用花时间逐个寻找下一个字符在后面与否。

可以使用动态规划的方法实现预处理：

**状态定义**- 令 f\[i\]\[j\] 表示字符串 t 中从位置 i 开始**往后**字符 j 第一次出现的位置。

**初始化** - n为较短s的长度，m为较长t的长度；假定下标从0开始，那么 f\[i\]\[j\]中有0≤i≤m−1 ，对于边界状态 f\[m-1\]\[..\]，我们置 f\[m\]\[..\] 为 m，让 f\[m-1\]\[..\] 正常进行转移。这样对于某个字符j，如果 f\[i\]\[j\]=m，则表示从位置 i 开始往后不存在字符 j。

**状态转移方程** - 在进行状态转移时，如果 t 中位置 i 的字符就是 j，那么 f\[i\]\[j\]=i，否则 j 出现在位置 i+1 开始往后，即 f\[i\]\[j\]=f\[i+1\]\[j\]，因此要倒过来进行动态规划，从后往前枚举 i。

这样我们可以写出状态转移方程：

![](../../.gitbook/assets/image%20%28121%29.png)

这样，我们可以利用一个数组来记录（空间换时间），每次花费 O\(1\) 时间就可以跳转到下一个位置（而不需要一个个对比字符寻找），直到位置变为 m 或 s 中的每一个字符都匹配成功。

**考虑状态压缩** - 同时注意到，该解法中对 t 的处理与 s 无关，且预处理完成后，可以利用预处理数组的信息，线性地算出任意一个字符串 s 是否为 t 的子串。这样可以解决follow up问题，“如果有大量输入的 S，称作S1, S2, ... , Sk 其中 k &gt;= 10亿，你需要依次检查它们是否为 T 的子序列。在这种情况下，你会怎样改变代码？”

![](../../.gitbook/assets/image%20%28122%29.png)

3\) Binary Search也可以解决输入大量字符串的问题。

### 代码

双指针

```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        if (s.isEmpty()) {
            return true;
        }
        if (t.isEmpty()) {
            return false;
        }
        int indexS = 0;
        int indexT = 0;
        
        while (indexS < s.length() && indexT < t.length()) {
            if (s.charAt(indexS) == t.charAt(indexT)) {
                indexS++;
                indexT++;
            } else {
                indexT++;
            }
        }
        return indexS == s.length();
    }
}
```

DP

```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        int n = s.length(), m = t.length();
        
        // 二维数组记录在t中每个位置，26个小写字母在当前或者之后出现的位置
        // m + 1表示额外在最后多出来一个记录表示不存在
        int[][] dp = new int[m + 1][26];
        for (int i = 0; i < 26; i++) { // 在最后一个位置上初始化所有26个小写字母的位置为m(不存在)
            dp[m][i] = m;
        }
        
        // 从后向前计算t中每个位置对应的26个小写字母的位置
        for (int i = m - 1; i >= 0; i--) {
            for (int j = 0; j < 26; j++) {
                if (j + 'a' == t.charAt(i)) { //循环查找26个小写字符是否等于从后向前过程中t中的当前字符
                    dp[i][j] = i;
                } else { // 当前字符不等于就让当前等于右边的状态
                    dp[i][j] = dp[i + 1][j];
                }
            }
        }
        
        // 开始查找s是否为t的子串
        int add = 0; // 记录s中下一个字符的位置
        for (int k = 0; k < n; k++) {
            int index = s.charAt(k) - 'a';
            if (dp[add][index] == m) { // 到了最右没找完s中字符
                return false;
            }
            add = dp[add][index] + 1;
        }
        return true;
    }
}
```


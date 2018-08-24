---
description: >-
  面试中的动态规划大致分为单向递归（首或者尾）， O（n^2）或者O(n^3) 距离递归,  O(m*n)递归，有限定条件的NP (背包）。 
  至于贪心和带状态的dp(走道铺砖）一类的dp，在面试中几乎不会遇到， 因为很难临时造出一道这样的题目， 面试官一般也没这个能力和时间来思考题目是不是严谨。
---

# Dynamic Programming

## \*\*\*\*

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




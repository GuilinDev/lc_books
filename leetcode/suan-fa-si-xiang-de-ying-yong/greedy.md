---
description: '贪心准备下Jump Game, 加油站， 迪杰斯特拉， 最小生成树就足够了。'
---

# Greedy

## **406 Queue Reconstruction by Height**

### 原题概述

Suppose you have a random list of people standing in a queue. Each person is described by a pair of integers `(h, k)`, where `h` is the height of the person and `k` is the number of people in front of this person who have a height greater than or equal to `h`. Write an algorithm to reconstruct the queue.

**Note:**  
The number of people is less than 1,100.  


**Example**

```text
Input:
[[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]]

Output:
[[5,0], [7,0], [5,2], [6,1], [4,4], [7,1]]
```

### 题意和分析

给一个队列，里面的人用\[h,k\]二元组表示，h代表当前人的高度，k代表在这个人前面且高度大于等于它的h的人数，现在要求按照这个二元组重构这个队列使之变得有效。这道题的思路是\(贪心算法\)： \(1\)首先找到身高最高的人并对他们进行排序。 \(2\)然后找到身高次高的人，按照他们的前面的人数把他们插入到最高的人群中。 \(3\) 以此类推。

因此这是一个排序和插入的过程，按照身高进行降序排序，如果我们按照先处理身高最高的，那他们的k值就是他们所应该在的位置——因为已经没有比他们更高的了。 所以我们从高度从高到低按照k值的位置一直插入到答案中即可。

### 代码

## **55 Jump Game** 

### **原题概述**

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

**Example 1:**

```text
Input: [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

**Example 2:**

```text
Input: [3,2,1,0,4]
Output: false
Explanation: You will always arrive at index 3 no matter what. Its maximum
             jump length is 0, which makes it impossible to reach the last index.
```

### **题意和分析**

这道题首先可以用DP， 维护一个一位数组dp，其中dp\[i\]表示达到i位置时剩余的步数，到达当前位置跟上一个位置（不是前一个位置）的剩余步数和数字（能达到的最远位置）有关，下一个位置的剩余步数（dp值）就等于当前的这个较大值减去1，因为需要花一个跳力到达下一个位置，所以状态转移方程：dp\[i\] = max\(dp\[i - 1\], nums\[i - 1\]\) - 1，如果当某一个时刻dp数组的值为负了，说明无法抵达当前位置，则直接返回false，最后判断**数**组最后一位是否为非负数即可知道是否能抵达该位置。

Greedy的做法会更优，因为其实没有必要用维护一维数组的方式来对每一步的剩余步数进行关注，只知道是否能到达末尾就行了，只需维护一个变量reach，来记录当前步最远能到达的位置坐标，初始为0，遍历整个数组，如果当前的坐标大于reach或者reach已经到达最后一个位置或超出，就跳出循环；否则就更更新reach为当前reach的值和i + nums\[i\]中的较大值。

### **代码**

DP

```java
class Solution {
    public boolean canJump(int[] nums) {
        int len = nums.length;
        int [] dp = new int[len];
        Arrays.fill(dp, 0);

        for (int i = 1; i < len; i++) {
            dp[i] = Math.max(dp[i - 1], nums[i - 1]) - 1;
            if (dp[i] < 0) {
                return false;
            }
        }
        return dp[len - 1] >= 0;
    }
}
```

Greedy

```java
class Solution {
    public boolean canJump(int[] nums) {
        int len = nums.length;
        int reach = 0;
        for (int i = 0; i < len; i++) {
            if (i > reach || reach >= len - 1) {
                break;
            }
            reach = Math.max(reach, i + nums[i]);
        }
        return reach >= len - 1;
    }
}
```

##  **45 Jump Game II**

### **原题概述** <a id="yuan-ti-gai-shu-30"></a>

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the minimum number of jumps.

**Example:**

```text
Input: [2,3,1,1,4]
Output: 2
Explanation: The minimum number of jumps to reach the last index is 2.
    Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

**Note:**

You can assume that you can always reach the last index.

### **题意和分析** <a id="ti-yi-he-fen-xi-30"></a>

上一道题55 Jump Game是判断能否到达最后，这一道题是找出步数最少的走法，[参考这里](https://www.cnblogs.com/lichen782/p/leetcode_Jump_Game_II.html)；遍历数组，把上一道题的全局最优变化成第step步最优和第step-1步最优，step表示走了多少步，当走到超过第step-1步所能达到最远距离的时候，说明step-1不能到达当前步，这时候更新步数step-1。

### **代码** <a id="dai-ma-30"></a>

```java
class Solution {
    public int jump(int[] nums) {
        int step = 0;
        int last = 0;//上一步能到达的最远距离
        int curr = 0;//当前结点最远能覆盖的距离
        for (int i = 0; i < nums.length; i++) {
            if (i > last) {//上一步不能到达，得再跳一次了
                last = curr;
                step++;
            }
            curr = Math.max(curr, i + nums[i]);
        }
        return step;
    }
}
```


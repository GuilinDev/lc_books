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

```java
class Solution {
    public int[][] reconstructQueue(int[][] people) {
        Arrays.sort(people, new Comparator<int[]>() {//按身高降序排序(h大的在前面)，按k的大小升序排列(k小的在前面)
            public int compare(int[] a, int[] b) {
                if (a[0] != b[0]) {
                    return -a[0] + b[0];//先排身高
                } else {
                    return a[1] - b[1];//身高一样再排前面的人数
                }
            }
        });

        List<int[]> result = new LinkedList<int[]>();//在结果中选择合适的位置插入存储结果
        for (int i = 0; i < people.length; i++) {
            int[] onePeople = people[i];
            result.add(onePeople[1], onePeople);
        }
        return result.toArray(new int[people.length][]);
    }
}
```

## **134 Gas Station**

### 原题概述

There are _N_ gas stations along a circular route, where the amount of gas at station _i_ is `gas[i]`.

You have a car with an unlimited gas tank and it costs `cost[i]` of gas to travel from station _i_ to its next station \(_i_+1\). You begin the journey with an empty tank at one of the gas stations.

Return the starting gas station's index if you can travel around the circuit once in the clockwise direction, otherwise return -1.

**Note:**

* If there exists a solution, it is guaranteed to be unique.
* Both input arrays are non-empty and have the same length.
* Each element in the input arrays is a non-negative integer.

**Example 1:**

```text
Input: 
gas  = [1,2,3,4,5]
cost = [3,4,5,1,2]

Output: 3

Explanation:
Start at station 3 (index 3) and fill up with 4 unit of gas. Your tank = 0 + 4 = 4
Travel to station 4. Your tank = 4 - 1 + 5 = 8
Travel to station 0. Your tank = 8 - 2 + 1 = 7
Travel to station 1. Your tank = 7 - 3 + 2 = 6
Travel to station 2. Your tank = 6 - 4 + 3 = 5
Travel to station 3. The cost is 5. Your gas is just enough to travel back to station 3.
Therefore, return 3 as the starting index.
```

**Example 2:**

```text
Input: 
gas  = [2,3,4]
cost = [3,4,3]

Output: -1

Explanation:
You can't start at station 0 or 1, as there is not enough gas to travel to the next station.
Let's start at station 2 and fill up with 4 unit of gas. Your tank = 0 + 4 = 4
Travel to station 0. Your tank = 4 - 3 + 2 = 3
Travel to station 1. Your tank = 3 - 3 + 3 = 3
You cannot travel back to station 2, as it requires 4 unit of gas but you only have 3.
Therefore, you can't travel around the circuit once no matter w
```

### 题意和分析

首先要知道能走完整个环的前提是gas的总量要大于cost的总量，这样才会有起点的存在。假设开始设置起点start = 0, 并从这里出发，如果当前的gas值大于cost值，就可以继续前进，此时到下一个站点，剩余的gas加上当前的gas再减去cost，看是否大于0，若大于0，则继续前进。当到达某一站点时，若这个值小于0了，则说明从起点到这个点中间的任何一个点都不能作为起点（因为总体不够，前面的油有剩余，中间任何点作为起点的话油肯定更不够），则把起点设为下一个点，继续遍历。当遍历完整个环时，当前保存的起点即为所求。

也可以从后往前遍历，用一个变量max来记录出现过的剩余油量的最大值，total记录当前剩余油量的值，start还是记录起点的位置。当total大于max的时候，说明当前位置可以作为起点，更新start，并且更新max。为啥呢？因为每次total加上的都是当前位置的油量减去消耗，如果这个差值大于0的话，说明当前位置可以当作起点，因为从当前位置到末尾都不会出现油量不够的情况，而一旦差值小于0的话，说明当前位置如果是起点的话，油量就不够，无法走完全程，所以我们不更新起点位置start。最后结束后还是看total是否大于等于0，如果其小于0的话，说明没有任何一个起点能走完全程，因为总油量都不够。

### 代码

正常的greedy

```java
class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int total = 0;//总路程的油量
        int remain = 0;//以某个点为起点，某站点剩余的油量
        int start = 0;//起点

        for (int i = 0; i < gas.length; i++) {
            total += gas[i] - cost[i];//循环完所有的加油站点的油量
            remain += gas[i] - cost[i];
            if (remain < 0) {
                start = i + 1;
                remain = 0;//从起点重新开始
            }
        }
        return total < 0 ? -1 : start;
    }
}
```

从后开始算

```java
class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int total = 0;
        int max = -1;
        int start = 0;

        for (int i = gas.length - 1; i >= 0; i--) {
            total += gas[i] - cost[i];
            if (total > max) {
                start = i;
                max = total;
            }
        }
        return total < 0 ? -1 : start;
    }
}
```

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
        if (nums == null || nums.length == 0) {
            return true;
        }
        int len = nums.length;
        int [] dp = new int[len];
        Arrays.fill(dp, 0);

        for (int i = 1; i < len; i++) {
            dp[i] = Math.max(dp[i - 1], nums[i - 1]) - 1;
            if (dp[i] < 0) {
                return false;
            }
        }
        return dp[len - 1] >= 0; //等于是刚好到最后一个index
    }
}
```

考虑状态压缩

```java
class Solution {
    // dp[i] = max(dp[i - 1], nums[i - 1]) - 1
    public boolean canJump(int[] nums) {
        if (nums == null || nums.length == 0) {
            return true;
        }
        int len = nums.length;
        int prev = 0, curr = 0;
        
        for (int i = 1; i < len; i++) {
            curr = Math.max(prev, nums[i - 1]) - 1;
            
            if (curr < 0) {
                return false;
            }
            prev = curr;
        }
        return curr >= 0;
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

上一道题55 Jump Game是判断能否到达最后，这一道题是找出步数最少的走法；

LC的讨论里面，大部分都是贪婪算法的思路，每次在可跳范围内选择可以使得跳的更远的位置。

如下图，开始的位置是 2，可跳的范围是橙色的。然后因为 3 可以跳的更远，所以选择跳到 3 的位置。

![](../.gitbook/assets/image%20%28100%29.png)

然后现在的位置就是 3 了，能跳的范围是橙色的，然后因为 4 可以跳的更远，所以选择下次跳到 4 的位置。

![](../.gitbook/assets/image%20%2899%29.png)

So on and so forth，写代码时用curFarthest 表示当前能跳的最远边界，对于上边的的例子，第一个图就是橙色 1，第二个图中就是橙色的 4，遍历数组的时候，遇到了边界，就重新更新新的边界直到最后。

### **代码** <a id="dai-ma-30"></a>

Greedy，时间O\(n\)，空间O\(1\)

```java
class Solution {
    public int jump(int[] nums) {
        int currEnd = 0;
        int steps = 0;
        int currFarthest = 0;
        for (int i = 0; i < nums.length - 1; i++) { // 检查到倒数第二个元素，这样才有“下一个”元素
            // 每遇到一个新数就，就判断一下最远可以跳到的距离
            // 每次找局部最优，最后达到全局最优
            currFarthest = Math.max(currFarthest, nums[i] + i);
            if (i == currEnd) {//遇到边界，就更新边界，并且步数加一
                currEnd = currFarthest;
                steps++;
            }
        }
        return steps;
    }
}
```

另一个写法

```java
class Solution {
    public int jump(int[] nums) {
        int step = 0;
        int last = 0;//上一步能到达的最远距离
        int curr = 0;//当前结点最远能覆盖的距离
        for (int i = 0; i < nums.length; i++) {
            if (i > last) {//上一步不能到达，得再跳一次了，步数+1
                last = curr;
                step++;
            }
            curr = Math.max(curr, i + nums[i]);
        }
        return step;
    }
}
```



## 135 Candy

### 原题概述

There are _N_ children standing in a line. Each child is assigned a rating value.

You are giving candies to these children subjected to the following requirements:

* Each child must have at least one candy.
* Children with a higher rating get more candies than their neighbors.

What is the minimum candies you must give?

**Example 1:**

```text
Input: [1,0,2]
Output: 5
Explanation: You can allocate to the first, second and third child with 2, 1, 2 candies respectively.
```

**Example 2:**

```text
Input: [1,2,2]
Output: 4
Explanation: You can allocate to the first, second and third child with 1, 2, 1 candies respectively.
             The third child gets 1 candy because it satisfies the above two conditions.
```

### 题意和分析

做两遍遍历， 首先初始化每个人一个糖果，第一遍从左向右遍历，如果右边的小盆友的等级高，等加一个糖果，这样保证了一个方向上高等级的糖果多。然后再从右向左遍历一遍，如果相邻两个左边的等级高，而左边的糖果又少的话，则左边糖果数为右边糖果数加一。最后再把所有小盆友的糖果数都加起来返回即可，时间和空间复杂度都是 O\(n\) 。

一遍遍历也可以时间为 O\(n\) 空间为 O\(1\) ，参考这里的解法（[http://www.allenlipeng47.com/blog/index.php/2016/07/21/candy/](http://www.allenlipeng47.com/blog/index.php/2016/07/21/candy/)， [http://www.cnblogs.com/grandyang/p/4575026.html](http://www.cnblogs.com/grandyang/p/4575026.html)），解释如下：

先给第一个同学一个糖果，那么对于接下来的一个同学就有三种情况：

1. 接下来的同学的rating等于前一个同学，那么给接下来的同学一个糖果就行。

2. 接下来的同学的rating大于前一个同学，那么给接下来的同学的糖果数要比前一个同学糖果数加1。

3.接下来的同学的rating小于前一个同学，那么我们此时不知道应该给这个同学多少个糖果，需要看后面的情况。

对于第三种情况，我们不确定要给几个，因为要是只给1个的话，那么有可能接下来还有rating更小的同学，总不能一个都不给吧。也不能直接给前一个同学的糖果数减1，有可能给多了，因为如果后面再没人了的话，其实只要给一个就行了。还有就是，如果后面好几个rating越来越小的同学，那么前一个同学的糖果数可能还得追加，以保证最后面的同学至少能有1个糖果。来一个例子吧，四个同学，他们的rating如下：

1 3 2 1

先给第一个rating为1的同学一个糖果，然后从第二个同学开始遍历，第二个同学rating为3，比1大，所以多给一个糖果，第二个同学得到两个糖果。下面第三个同学，他的rating为2，比前一个同学的rating小，如果我们此时给1个糖果的话，那么rating更小的第四个同学就得不到糖果了，所以我们要给第四个同学1个糖果，而给第三个同学2个糖果，此时要给第二个同学追加1个糖果，使其能够比第三个同学的糖果数多至少一个。那么我们就需要统计出多有个连着的同学的rating变小，用变量count来记录，找出了最后一个减小的同学，那么就可以往前推，每往前一个加一个糖果，这就是个等差数列，我们可以直接利用求和公式算出这些rating减小的同学的糖果之和。然后我们还要看第一个开始减小的同学的前一个同学（pre）需不需要追加糖果，只要比较count和pre的大小，pre是之前同学得到的最大糖果数，二者做差加1就是需要追加的糖果数，加到结果result中即可。

### 代码

两次遍历

```java
class Solution {
    public int candy(int[] ratings) {
        if (ratings == null || ratings.length == 0) {
            return 0;
        }
        int len = ratings.length;
        int result = 0;
        int[] nums = new int[len];
        Arrays.fill(nums, 1);

        for (int i = 0; i < len - 1; i++) {//从左向右遍历，保证一个方向上正确
            if (ratings[i + 1] > ratings[i]) {
                nums[i + 1] = nums[i] + 1;
            }
        }

        for (int i = len - 1; i > 0; i--) {//从右到左，检查另一个方向
            if (ratings[i - 1] > ratings[i]) {
                nums[i - 1] = Math.max(nums[i - 1], nums[i] + 1);
            }
        }

        for (int num : nums) {
            result += num;
        }
        return result;
    }
}
```

只遍历一次

```java
class Solution {
    public int candy(int[] ratings) {
        if (ratings == null || ratings.length == 0) {
            return 0;
        }
        int result = 1, pre = 1, count = 0;
        for (int i = 1; i < ratings.length; i++) {
            if (ratings[i] >= ratings[i - 1]) {
                if (count > 0) {
                    result += count * (count + 1) / 2;//等差数列求和
                    if (count >= pre) {
                        result += count - pre + 1;//追加的糖果
                    }
                    count = 0;
                    pre = 1;
                }
                pre = (ratings[i] == ratings[i - 1]) ? 1 : pre + 1;
                result += pre;
            } else {
                count++;
            }
        }
        if (count > 0) {
            result += count * (count + 1) / 2;
            if (count >= pre) {
                result += count - pre + 1;
            }
        }
        return result;
    }
}
```


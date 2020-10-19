# tag4

##  465 Optimal Account Balancing

### 原题

最优账单平衡，一群朋友在度假期间会相互借钱。比如说，小爱同学支付了小新同学的午餐共计 10 美元。如果小明同学支付了小爱同学的出租车钱共计 5 美元。我们可以用一个三元组 \(x, y, z\) 表示一次交易，表示 x 借给 y 共计 z 美元。用 0, 1, 2 表示小爱同学、小新同学和小明同学（0, 1, 2 为人的标号），上述交易可以表示为 \[\[0, 1, 10\], \[2, 0, 5\]\]。

给定一群人之间的交易信息列表，计算能够还清所有债务的最小次数。

注意：

一次交易会以三元组 \(x, y, z\) 表示，并有 x ≠ y 且 z &gt; 0。 人的标号可能不是按顺序的，例如标号可能为 0, 1, 2 也可能为 0, 2, 6。

A group of friends went on holiday and sometimes lent each other money. For example, Alice paid for Bill's lunch for $10. Then later Chris gave Alice $5 for a taxi ride. We can model each transaction as a tuple \(x, y, z\) which means person x gave person y $z. Assuming Alice, Bill, and Chris are person 0, 1, and 2 respectively \(0, 1, 2 are the person's ID\), the transactions can be represented as `[[0, 1, 10], [2, 0, 5]]`.

Given a list of transactions between a group of people, return the minimum number of transactions required to settle the debt.

**Note:**

1. A transaction will be given as a tuple \(x, y, z\). Note that `x ≠ y` and `z > 0`.
2. Person's IDs may not be linear, e.g. we could have the persons 0, 1, 2 or we could also have the persons 0, 2, 6.

**Example 1:**

```text
Input:
[[0,1,10], [2,0,5]]

Output:
2

Explanation:
Person #0 gave person #1 $10.
Person #2 gave person #0 $5.

Two transactions are needed. One way to settle the debt is person #1 pays person #0 and #2 $5 each.
```

**Example 2:**

```text
Input:
[[0,1,10], [1,0,1], [1,2,5], [2,0,5]]

Output:
1

Explanation:
Person #0 gave person #1 $10.
Person #1 gave person #0 $1.
Person #1 gave person #2 $5.
Person #2 gave person #0 $5.

Therefore, person #1 only need to give person #0 $4, and all debt is settled.
```

### 分析

DFS

先把所有人的钱的正负都放到一个数组里。 然后从第一个人开始，找到钱数的正负和他不一样的人，试图把第一个人的钱都放到另外一个人身上。 然后for循环第二个人一直到第n个

### 代码

```java
class Solution {
    int[] acc;
    int res = Integer.MAX_VALUE;
    int n;
    public int minTransfers(int[][] transactions) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < transactions.length; i ++) {
            int first = transactions[i][0];
            int second = transactions[i][1];
            int amount = transactions[i][2];
            map.put(first, map.getOrDefault(first, 0) - amount);
            map.put(second, map.getOrDefault(second, 0) + amount);
        }
        acc = map.values().stream().mapToInt(i -> i).toArray();
        this.n = acc.length;        
        helper(0, 0);
        return res;
    }

    void helper(int start, int cnt) {
        while (start < n && acc[start] == 0) {
            start ++;
        }
        if (start == n) {
            res = Math.min(res, cnt);
            return;
        }
        for (int i = start + 1; i < n; i ++) {
            if (acc[i] < 0 && acc[start] > 0 || acc[start] < 0 && acc[i] > 0) {
                acc[i] += acc[start];
                helper(start + 1, cnt + 1);
                acc[i] -= acc[start];
            }
        }
    }
}
```

## 679 24 Game

### 原题

 你有 4 张写有 1 到 9 数字的牌。你需要判断是否能通过 `*`，`/`，`+`，`-`，`(`，`)` 的运算得到 24。

You have 4 cards each containing a number from 1 to 9. You need to judge whether they could operated through `*`, `/`, `+`, `-`, `(`, `)` to get the value of 24.

**Example 1:**  


```text
Input: [4, 1, 8, 7]
Output: True
Explanation: (8-4) * (7-1) = 24
```

**Example 2:**  


```text
Input: [1, 2, 1, 2]
Output: False
```

**Note:**  


1. The division operator `/` represents real division, not integer division. For example, 4 / \(1 - 2/3\) = 12.
2. Every operation done is between two numbers. In particular, we cannot use `-` as a unary operator. For example, with `[1, 1, 1, 1]` as input, the expression `-1 - 1 - 1 - 1` is not allowed.
3. You cannot concatenate numbers together. For example, if the input is `[1, 2, 1, 2]`, we cannot write this as 12 + 12.

### 分析

我们第一步肯定是挑出两个数，算出一个数，替代它们俩。

然后在三个数中玩 24 点，再挑出两个数，算出一个数替代它们。

然后在两个数中玩 24 点……

这就有了递归的思路。

挑出不同的两数组合，需要两层循环。

递归函数里：

* 我们枚举出所有可能的运算操作——（对应递归树的不同分支） 
* 逐个尝试一种运算——（选择进入一个分支） 
* 传入长度更小的新数组继续递归——（递归计算当前子树（子问题）） 
* 当做到只剩一个数时——（到达了递归树的底部），看看是不是24。 
  * 是就返回true——（结束当前递归，并且控制它不进入别的分支）
  *  否则返回false，离开错误的分支，进入别的递归分支，尝试别的运算。 我是不是给你描绘出一棵递归树啦？

就是穷举，或者说，不带「提前回溯」的「回溯」。

### 代码

```java
class Solution {
  public boolean judgePoint24(int[] nums) {
    List<Double> list = new ArrayList<>(4);
      for (int num : nums) {
         list.add((double) num);
      }
    return solve(list);
  }
  boolean solve(List<Double> nums){
    if(nums.size() == 1){
      return Math.abs(nums.get(0) - 24) <= 0.00001;
    }
    for(int i = 0; i < nums.size(); i++){
      for(int j = i + 1; j < nums.size(); j++){
        List<Double> copy = new ArrayList<>(nums);
        double b = copy.remove(j), a = copy.remove(i);
        boolean valid = false;
        copy.add(a + b);
        valid |= solve(copy);
        copy.set(copy.size() - 1, a - b);
        valid |= solve(copy);
        copy.set(copy.size() - 1, a * b);
        valid |= solve(copy);
        copy.set(copy.size() - 1, a / b);
        valid |= solve(copy);
        copy.set(copy.size() - 1, b - a);
        valid |= solve(copy);
        copy.set(copy.size() - 1, b / a);
        valid |= solve(copy);
        if(valid) return true;
      }
    }
    return false;
  }
}
```

## 837 New 21 Game

### 原题

爱丽丝参与一个大致基于纸牌游戏 “21点” 规则的游戏，描述如下：

爱丽丝以 0 分开始，并在她的得分少于 K 分时抽取数字。 抽取时，她从 \[1, W\] 的范围中随机获得一个整数作为分数进行累计，其中 W 是整数。 每次抽取都是独立的，其结果具有相同的概率。

当爱丽丝获得不少于 K 分时，她就停止抽取数字。 爱丽丝的分数不超过 N 的概率是多少

Alice plays the following game, loosely based on the card game "21".

Alice starts with `0` points, and draws numbers while she has less than `K` points.  During each draw, she gains an integer number of points randomly from the range `[1, W]`, where `W` is an integer.  Each draw is independent and the outcomes have equal probabilities.

Alice stops drawing numbers when she gets `K` or more points.  What is the probability that she has `N` or less points?

**Example 1:**

```text
Input: N = 10, K = 1, W = 10
Output: 1.00000
Explanation:  Alice gets a single card, then stops.
```

**Example 2:**

```text
Input: N = 6, K = 1, W = 10
Output: 0.60000
Explanation:  Alice gets a single card, then stops.
In 6 out of W = 10 possibilities, she is at or below N = 6 points.
```

**Example 3:**

```text
Input: N = 21, K = 17, W = 10
Output: 0.73278
```

**Note:**

1. `0 <= K <= N <= 10000`
2. `1 <= W <= 10000`
3. Answers will be accepted as correct if they are within `10^-5` of the correct answer.
4. The judging time limit has been reduced for this question.

### 分析

![](../.gitbook/assets/image%20%28155%29.png)

时间复杂度：O\(N - K + K\) ==&gt; O\(N\) 

空间复杂度：O\(K + W\)

### 代码

```java
class Solution {
    public double new21Game(int N, int K, int W) {
        // 先判断 K - 1 + W 是否在 N 的里面，如果在的话，说明肯定能赢得游戏，返回 1.0，也就是 100%
        if (N - K + 1 >= W) {
            return 1.0;
        }
        double[] dp = new double[K + W];
        // 将能赢得游戏的点数的概率设置为 1
        for (int i = K; i <= N; i++) {
            dp[i] = 1.0;
        }
        // 计算K + W 这几个点数的概率和
        double sumProb = N - K + 1;
        // 从 K - 1 开始计算，
        for (int i = K - 1; i >= 0; i--) {
            // 点数为 i 的赢得游戏的概率为 i + 1 ~ i + W 的概率和除以 W 
            dp[i] = sumProb / W;
            sumProb = sumProb - dp[i + W] + dp[i];
        }

        return dp[0];
    }
}
```

## \* 1057 Campus Bikes

### 原题

在由 2D 网格表示的校园里有 n 位工人（worker）和 m 辆自行车（bike），n &lt;= m。所有工人和自行车的位置都用网格上的 2D 坐标表示。

我们需要为每位工人分配一辆自行车。在所有可用的自行车和工人中，我们选取彼此之间曼哈顿距离最短的工人自行车对 \(worker, bike\) ，并将其中的自行车分配給工人。如果有多个 \(worker, bike\) 对之间的曼哈顿距离相同，那么我们选择工人索引最小的那对。类似地，如果有多种不同的分配方法，则选择自行车索引最小的一对。不断重复这一过程，直到所有工人都分配到自行车为止。

给定两点 p1 和 p2 之间的曼哈顿距离为 Manhattan\(p1, p2\) = \|p1.x - p2.x\| + \|p1.y - p2.y\|。

返回长度为 n 的向量 ans，其中 a\[i\] 是第 i 位工人分配到的自行车的索引（从 0 开始）。

On a campus represented as a 2D grid, there are `N` workers and `M` bikes, with `N <= M`. Each worker and bike is a 2D coordinate on this grid.

Our goal is to assign a bike to each worker. Among the available bikes and workers, we choose the \(worker, bike\) pair with the shortest Manhattan distance between each other, and assign the bike to that worker. \(If there are multiple \(worker, bike\) pairs with the same shortest Manhattan distance, we choose the pair with the smallest worker index; if there are multiple ways to do that, we choose the pair with the smallest bike index\). We repeat this process until there are no available workers.

The Manhattan distance between two points `p1` and `p2` is `Manhattan(p1, p2) = |p1.x - p2.x| + |p1.y - p2.y|`.

Return a vector `ans` of length `N`, where `ans[i]` is the index \(0-indexed\) of the bike that the `i`-th worker is assigned to.

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/03/06/1261_example_1_v2.png)

```text
Input: workers = [[0,0],[2,1]], bikes = [[1,2],[3,3]]
Output: [1,0]
Explanation: 
Worker 1 grabs Bike 0 as they are closest (without ties), and Worker 0 is assigned Bike 1. So the output is [1, 0].
```

**Example 2:**

![](https://assets.leetcode.com/uploads/2019/03/06/1261_example_2_v2.png)

```text
Input: workers = [[0,0],[1,1],[2,0]], bikes = [[1,0],[2,2],[2,1]]
Output: [0,2,1]
Explanation: 
Worker 0 grabs Bike 0 at first. Worker 1 and Worker 2 share the same distance to Bike 2, thus Worker 1 is assigned to Bike 2, and Worker 2 will take Bike 1. So the output is [0,2,1].
```

**Note:**

1. `0 <= workers[i][j], bikes[i][j] < 1000`
2. All worker and bike locations are distinct.
3. `1 <= workers.length <= bikes.length <= 1000`

### 分析

就是排序，利用pq取出曼哈顿距离。

### 代码

```java
class Solution {

    public int[] assignBikes(int[][] workers, int[][] bikes) {
        PriorityQueue<Manhattan> pQueue = new PriorityQueue<>();
        for (int i = 0; i < workers.length; i++) {
            for (int j = 0; j < bikes.length; j++) {
                int manhattanDis = Math.abs(workers[i][0] - bikes[j][0]) + Math.abs(workers[i][1] - bikes[j][1]);
                pQueue.offer(new Manhattan(i, j, manhattanDis));
            }
        }
        int[] ans = new int[workers.length];
        boolean[] visit = new boolean[workers.length];
        boolean[] bikeVisit = new boolean[bikes.length];
        int workerCount = 0;
        while (!pQueue.isEmpty() && workerCount <= workers.length) {
            Manhattan temp = pQueue.poll();
            if (!visit[temp.workerNum] && !bikeVisit[temp.bikeNum]) {
                ans[temp.workerNum] = temp.bikeNum;
                visit[temp.workerNum] = true;
                bikeVisit[temp.bikeNum] = true;
                workerCount++;
            }
        }
        return ans;
    }
}

class Manhattan implements Comparable<Manhattan> {
    int workerNum;
    int bikeNum;
    int manhatDistance;

    Manhattan(int workerNum, int bikeNum, int manhatDistance) {
        this.workerNum = workerNum;
        this.bikeNum = bikeNum;
        this.manhatDistance = manhatDistance;
    }

    @Override
    public int compareTo(Manhattan o) {
        if (this.manhatDistance == o.manhatDistance) {
            if (this.workerNum == o.workerNum) {
                return this.bikeNum - o.bikeNum;
            } else {
                return this.workerNum - o.workerNum;
            }
        } else {
            return this.manhatDistance - o.manhatDistance;
        }
    }
}
```

## 1066 Campus Bikes II

### 原题

在由 2D 网格表示的校园里有 n 位工人（worker）和 m 辆自行车（bike），n &lt;= m。所有工人和自行车的位置都用网格上的 2D 坐标表示。

我们为每一位工人分配一辆专属自行车，使每个工人与其分配到的自行车之间的曼哈顿距离最小化。

p1 和 p2 之间的曼哈顿距离为 Manhattan\(p1, p2\) = \|p1.x - p2.x\| + \|p1.y - p2.y\|。

返回每个工人与分配到的自行车之间的曼哈顿距离的最小可能总和。

On a campus represented as a 2D grid, there are `N` workers and `M` bikes, with `N <= M`. Each worker and bike is a 2D coordinate on this grid.

We assign one unique bike to each worker so that the sum of the Manhattan distances between each worker and their assigned bike is minimized.

The Manhattan distance between two points `p1` and `p2` is `Manhattan(p1, p2) = |p1.x - p2.x| + |p1.y - p2.y|`.

Return _the minimum possible sum of Manhattan distances between each worker and their assigned bike_.

**Example 1:**![](https://assets.leetcode.com/uploads/2019/03/06/1261_example_1_v2.png)

```text
Input: workers = [[0,0],[2,1]], bikes = [[1,2],[3,3]]
Output: 6
Explanation: 
We assign bike 0 to worker 0, bike 1 to worker 1. The Manhattan distance of both assignments is 3, so the output is 6.
```

**Example 2:**![](https://assets.leetcode.com/uploads/2019/03/06/1261_example_2_v2.png)

```text
Input: workers = [[0,0],[1,1],[2,0]], bikes = [[1,0],[2,2],[2,1]]
Output: 4
Explanation: 
We first assign bike 0 to worker 0, then assign bike 1 to worker 1 or worker 2, bike 2 to worker 2 or worker 1. Both assignments lead to sum of the Manhattan distances as 4.
```

**Example 3:**

```text
Input: workers = [[0,0],[1,0],[2,0],[3,0],[4,0]], bikes = [[0,999],[1,999],[2,999],[3,999],[4,999]]
Output: 4995
```

**Constraints:**

* `N == workers.length`
* `M == bikes.length`
* `1 <= N <= M <= 10`
* `workers[i].length == 2`
* `bikes[i].length == 2`
* `0 <= workers[i][0], workers[i][1], bikes[i][0], bikes[i][1] < 1000`
* All the workers and the bikes locations are **unique**.

### 分析

1）DFS 暴力回溯，The DFS solution is pretty straight forward, try assign each bike to each worker.  
Time Complexy: O\(n \* m !\), n is number of workers, m is number of bikes

Ususally, when input size &lt;= 10, O\(n!\) can be accepeted. When input size &lt;= 12, we probably need do some pruning. if the test case is not strong, or problem designer wants to allow this techonolgy \(dfs + pruning\) to pass. we can luckly get a AC.\(For my experenice in LeetCode, when problem is tagged as Medium, this kind solution can be passed\)

2）BFS，用PQ计算最短距离

3\) 匈牙利算法+二分图匹配

### 代码

1）DFS

```java
class Solution {
    public int assignBikes(int[][] workers, int[][] bikes) {
        int n = workers.length;
        int m = bikes.length;
        int[][] dp = new int[n + 1][1 << m];
        for (int[] d : dp) {
            Arrays.fill(d, Integer.MAX_VALUE / 2);
        }
        dp[0][0] = 0;
        int min = Integer.MAX_VALUE;
        for (int i = 1; i <= n; i++) {
            for (int s = 1; s < (1 << m); s++) {
                for (int j = 0; j < m; j++) {
                    if ((s & (1 << j)) == 0) {
                        continue;
                    }
                    int prev = s ^ (1 << j);
                    dp[i][s] = Math.min(dp[i - 1][prev] + dis(workers[i - 1], bikes[j]), dp[i][s]) ;
                    if (i == n) {
                        min = Math.min(min, dp[i][s]);
                    }
                }
            }
        }
        return min;
    }
  
    public int dis(int[] p1, int[] p2) {
        return Math.abs(p1[0] - p2[0]) + Math.abs(p1[1] - p2[1]);
    }
}
```

2）BFS with PQ

```java
class Solution {
    public int assignBikes(int[][] workers, int[][] bikes) {
        Queue<Node> pq = new PriorityQueue<>(1,(a,b)->(a.cost-b.cost));
        Set<String> seen = new HashSet<>();
        pq.offer(new Node(0,0,0));
        while (!pq.isEmpty()){
            Node curr = pq.poll();
            String key = "$"+curr.worker+"$"+curr.mask;
            // reason - you can skip if you have already seen this mask
            // is because this is a PQ - and lower cost has already been seen
            // with this exact mask (i.e., those bikes used in some order)
            // then there is no point to consider a higher cost one 
            if (seen.contains(key))
                continue;
            seen.add(key);
            // all workers have a bike if this is true
            if (curr.worker == workers.length)
                return curr.cost;
            // scan all bikes - and create new nodes into the PQ for next worker.
            for(int j = 0; j < bikes.length; j++){
                if ( (curr.mask & (1<<j)) == 0){
                    pq.offer( new Node(curr.worker+1, curr.mask | (1 << j), 
                                       curr.cost + getDist(bikes[j], workers[curr.worker]) ));
                }
            }
        }
        return -1;
    }
    private int getDist(int[] bikepos,int[] wpos){
        return Math.abs(bikepos[0]-wpos[0]) + Math.abs(bikepos[1]-wpos[1]);
    }
    static class Node {
        int worker;
        int mask;
        int cost;
        public Node(int w,int m,int cost){
            this.worker = w;
            this.mask = m;
            this.cost = cost;
        }
    }
}
```

## 911 Online Election

### 原题

在选举中，第 i 张票是在时间为 times\[i\] 时投给 persons\[i\] 的。

现在，我们想要实现下面的查询函数： TopVotedCandidate.q\(int t\) 将返回在 t 时刻主导选举的候选人的编号。

在 t 时刻投出的选票也将被计入我们的查询之中。在平局的情况下，最近获得投票的候选人将会获胜。

In an election, the `i`-th vote was cast for `persons[i]` at time `times[i]`.

Now, we would like to implement the following query function: `TopVotedCandidate.q(int t)` will return the number of the person that was leading the election at time `t`.  

Votes cast at time `t` will count towards our query.  In the case of a tie, the most recent vote \(among tied candidates\) wins.

**Example 1:**

```text
Input: ["TopVotedCandidate","q","q","q","q","q","q"], [[[0,1,1,0,0,1,0],[0,5,10,15,20,25,30]],[3],[12],[25],[15],[24],[8]]
Output: [null,0,1,1,0,0,1]
Explanation: 
At time 3, the votes are [0], and 0 is leading.
At time 12, the votes are [0,1,1], and 1 is leading.
At time 25, the votes are [0,1,1,0,0,1], and 1 is leading (as ties go to the most recent vote.)
This continues for 3 more queries at time 15, 24, and 8.
```

**Note:**

1. `1 <= persons.length = times.length <= 5000`
2. `0 <= persons[i] <= persons.length`
3. `times` is a strictly increasing array with all elements in `[0, 10^9]`.
4. `TopVotedCandidate.q` is called at most `10000` times per test case.
5. `TopVotedCandidate.q(int t)` is always called with `t >= times[0]`.

### 分析

![](../.gitbook/assets/image%20%28169%29.png)

* 时间复杂度：O\(N + Q \log^2 N\)，其中 N 是选票的个数，QQ 是询问的个数。
* 空间复杂度：O\(N\)

![](../.gitbook/assets/image%20%28148%29.png)

* 时间复杂度：O\(N+QlogN\)，其中 N 是选票个数，Q 是询问个数。
* 空间复杂度：O\(N\)

### 代码

1\)

```java
class TopVotedCandidate {
    List<List<Vote>> A;
    public TopVotedCandidate(int[] persons, int[] times) {
        A = new ArrayList();
        Map<Integer, Integer> count = new HashMap();
        for (int i = 0; i < persons.length; ++i) {
            int p = persons[i], t = times[i];
            int c = count.getOrDefault(p, 0) + 1;

            count.put(p, c);
            while (A.size() <= c)
                A.add(new ArrayList<Vote>());
            A.get(c).add(new Vote(p, t));
        }
    }

    public int q(int t) {
        // Binary search on A[i][0].time for smallest i
        // such that A[i][0].time > t
        int lo = 1, hi = A.size();
        while (lo < hi) {
            int mi = lo + (hi - lo) / 2;
            if (A.get(mi).get(0).time <= t)
                lo = mi + 1;
            else
                hi = mi;
        }
        int i = lo - 1;

        // Binary search on A[i][j].time for smallest j
        // such that A[i][j].time > t
        lo = 0; hi = A.get(i).size();
        while (lo < hi) {
            int mi = lo + (hi - lo) / 2;
            if (A.get(i).get(mi).time <= t)
                lo = mi + 1;
            else
                hi = mi;
        }
        int j = Math.max(lo-1, 0);
        return A.get(i).get(j).person;
    }
}

class Vote {
    int person, time;
    Vote(int p, int t) {
        person = p;
        time = t;
    }
}
```

2\)

```java
class TopVotedCandidate {
    List<Vote> A;
    public TopVotedCandidate(int[] persons, int[] times) {
        A = new ArrayList();
        Map<Integer, Integer> count = new HashMap();
        int leader = -1;  // current leader
        int m = 0;  // current number of votes for leader

        for (int i = 0; i < persons.length; ++i) {
            int p = persons[i], t = times[i];
            int c = count.getOrDefault(p, 0) + 1;
            count.put(p, c);

            if (c >= m) {
                if (p != leader) {  // lead change
                    leader = p;
                    A.add(new Vote(leader, t));
                }

                if (c > m) m = c;
            }
        }
    }

    public int q(int t) {
        int lo = 1, hi = A.size();
        while (lo < hi) {
            int mi = lo + (hi - lo) / 2;
            if (A.get(mi).time <= t)
                lo = mi + 1;
            else
                hi = mi;
        }

        return A.get(lo - 1).person;
    }
}

class Vote {
    int person, time;
    Vote(int p, int t) {
        person = p;
        time = t;
    }
}
```

## 394 Decode String

### 原题

给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: k\[encoded\_string\]，表示其中方括号内部的 encoded\_string 正好重复 k 次。注意 k 保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 k ，例如不会出现像 3a 或 2\[4\] 的输入。

Given an encoded string, return its decoded string.

The encoding rule is: `k[encoded_string]`, where the encoded\_string inside the square brackets is being repeated exactly k times. Note that k is guaranteed to be a positive integer.

You may assume that the input string is always valid; No extra white spaces, square brackets are well-formed, etc.

Furthermore, you may assume that the original data does not contain any digits and that digits are only for those repeat numbers, k. For example, there won't be input like `3a` or `2[4]`.

**Example 1:**

```text
Input: s = "3[a]2[bc]"
Output: "aaabcbc"
```

**Example 2:**

```text
Input: s = "3[a2[c]]"
Output: "accaccacc"
```

**Example 3:**

```text
Input: s = "2[abc]3[cd]ef"
Output: "abcabccdcdcdef"
```

**Example 4:**

```text
Input: s = "abc3[cd]xyz"
Output: "abccdcdcdxyz"
```

### 分析

![](../.gitbook/assets/image%20%28171%29.png)

![](../.gitbook/assets/image%20%28152%29.png)

### 代码

1\)

```java
class Solution {
    public String decodeString(String s) {
        StringBuilder res = new StringBuilder();
        int multi = 0;
        LinkedList<Integer> stack_multi = new LinkedList<>();
        LinkedList<String> stack_res = new LinkedList<>();
        for(Character c : s.toCharArray()) {
            if(c == '[') {
                stack_multi.addLast(multi);
                stack_res.addLast(res.toString());
                multi = 0;
                res = new StringBuilder();
            }
            else if(c == ']') {
                StringBuilder tmp = new StringBuilder();
                int cur_multi = stack_multi.removeLast();
                for(int i = 0; i < cur_multi; i++) tmp.append(res);
                res = new StringBuilder(stack_res.removeLast() + tmp);
            }
            else if(c >= '0' && c <= '9') multi = multi * 10 + Integer.parseInt(c + "");
            else res.append(c);
        }
        return res.toString();
    }
}
```

2\)

```java
class Solution {
    public String decodeString(String s) {
        return dfs(s, 0)[0];
    }
    private String[] dfs(String s, int i) {
        StringBuilder res = new StringBuilder();
        int multi = 0;
        while(i < s.length()) {
            if(s.charAt(i) >= '0' && s.charAt(i) <= '9') 
                multi = multi * 10 + Integer.parseInt(String.valueOf(s.charAt(i))); 
            else if(s.charAt(i) == '[') {
                String[] tmp = dfs(s, i + 1);
                i = Integer.parseInt(tmp[0]);
                while(multi > 0) {
                    res.append(tmp[1]);
                    multi--;
                }
            }
            else if(s.charAt(i) == ']') 
                return new String[] { String.valueOf(i), res.toString() };
            else 
                res.append(String.valueOf(s.charAt(i)));
            i++;
        }
        return new String[] { res.toString() };
    } 
}
```

## 315 Count of Smaller Numbers After Self

[https://app.gitbook.com/@guilindev/s/interview/v/master/leetcode/segment-tree\#315-count-of-smaller-numbers-after-self](https://app.gitbook.com/@guilindev/s/interview/v/master/leetcode/segment-tree#315-count-of-smaller-numbers-after-self)

## 250 Count Univalue Subtrees

[https://app.gitbook.com/@guilindev/s/interview/v/master/leetcode/untitled-1\#250-count-univalue-subtrees](https://app.gitbook.com/@guilindev/s/interview/v/master/leetcode/untitled-1#250-count-univalue-subtrees)

## 53 - Maximum Subarray

[https://app.gitbook.com/@guilindev/s/interview/v/master/leetcode/array/zi-shu-zu-subarray\#53-maximum-subarray](https://app.gitbook.com/@guilindev/s/interview/v/master/leetcode/array/zi-shu-zu-subarray#53-maximum-subarray)

## 688 Knight Probability in Chessboard

### 原题

On an `N`x`N` chessboard, a knight starts at the `r`-th row and `c`-th column and attempts to make exactly `K` moves. The rows and columns are 0 indexed, so the top-left square is `(0, 0)`, and the bottom-right square is `(N-1, N-1)`.

A chess knight has 8 possible moves it can make, as illustrated below. Each move is two squares in a cardinal direction, then one square in an orthogonal direction.

![](https://assets.leetcode.com/uploads/2018/10/12/knight.png)

Each time the knight is to move, it chooses one of eight possible moves uniformly at random \(even if the piece would go off the chessboard\) and moves there.

The knight continues moving until it has made exactly `K` moves or has moved off the chessboard. Return the probability that the knight remains on the board after it has stopped moving.

**Example:**

```text
Input: 3, 2, 0, 0
Output: 0.0625
Explanation: There are two moves (to (1,2), (2,1)) that will keep the knight on the board.
From each of those positions, there are also two moves that will keep the knight on the board.
The total probability the knight stays on the board is 0.0625.
```

**Note:**

* `N` will be between 1 and 25.
* `K` will be between 0 and 100.
* The knight always initially starts on the board.

### 分析

BFS

### 代码

```java
class Solution {
    /*
    这道题给了一个大小为NxN国际象棋棋盘，上面有个骑士，相当于中国象棋中的马，能走‘日’字，给了我们一个起始位置，然后说允许我们走K步，问走完K步之后还能留在棋盘上的概率是多少。
    */
    int[][] moves = {{1,2}, {1,-2},{2,1},{2,-1},{-1,2},{-1,-2},{-2,1},{-2,-1}};
    public double knightProbability(int N, int K, int r, int c) {
        int len = N;
        double dp0[][] = new double[len][len];
        for (double[] row : dp0) {
            Arrays.fill(row, 1);
        }
        for (int l = 0; l < K; l++) {
            double[][] dp1 = new double[len][len];
            for (int i = 0; i < len; i++) {
                for (int j = 0; j < len; j++) {
                    for (int[] move : moves) {
                        int row = i + move[0];
                        int col = j + move[1];
                        if (isLegal(row, col, len)) {
                            dp1[i][j] += dp0[row][col];
                        }
                    }
                }
            }
            dp0 = dp1;
        }
        return dp0[r][c]/Math.pow(8, K);
    }
    
    private boolean isLegal(int r, int c, int len) {
        return r >= 0 && r < len && c >= 0 && c < len;
    }
}
```

## 57 Insert Interval

### 原题

给出一个_无重叠的 ，_按照区间起始端点排序的区间列表。

在列表中插入一个新的区间，你需要确保列表中的区间仍然有序且不重叠（如果有必要的话，可以合并区间）。

Given a set of _non-overlapping_ intervals, insert a new interval into the intervals \(merge if necessary\).

You may assume that the intervals were initially sorted according to their start times.

**Example 1:**

```text
Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]
```

**Example 2:**

```text
Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].
```

**Example 3:**

```text
Input: intervals = [], newInterval = [5,7]
Output: [[5,7]]
```

**Example 4:**

```text
Input: intervals = [[1,5]], newInterval = [2,3]
Output: [[1,5]]
```

**Example 5:**

```text
Input: intervals = [[1,5]], newInterval = [2,7]
Output: [[1,7]]
```

### 分析

![](../.gitbook/assets/image%20%28164%29.png)

![](../.gitbook/assets/image%20%28172%29.png)

* 时间复杂度：\mathcal{O}\(N\)O\(N\)。我们只遍历了一次输入元素。
* 空间复杂度：\mathcal{O}\(N\)O\(N\)，输出答案所使用的空间。

### 代码

```java
class Solution {
  public int[][] insert(int[][] intervals, int[] newInterval) {
    // init data
    int newStart = newInterval[0], newEnd = newInterval[1];
    int idx = 0, n = intervals.length;
    LinkedList<int[]> output = new LinkedList<int[]>();

    // add all intervals starting before newInterval
    while (idx < n && newStart > intervals[idx][0])
      output.add(intervals[idx++]);

    // add newInterval
    int[] interval = new int[2];
    // if there is no overlap, just add the interval
    if (output.isEmpty() || output.getLast()[1] < newStart)
      output.add(newInterval);
    // if there is an overlap, merge with the last interval
    else {
      interval = output.removeLast();
      interval[1] = Math.max(interval[1], newEnd);
      output.add(interval);
    }

    // add next intervals, merge with newInterval if needed
    while (idx < n) {
      interval = intervals[idx++];
      int start = interval[0], end = interval[1];
      // if there is no overlap, just add an interval
      if (output.getLast()[1] < start) output.add(interval);
      // if there is an overlap, merge with the last interval
      else {
        interval = output.removeLast();
        interval[1] = Math.max(interval[1], end);
        output.add(interval);
      }
    }
    return output.toArray(new int[output.size()][2]);
  }
}
```

## 174 Dungeon Game

### 原题

一些恶魔抓住了公主（P）并将她关在了地下城的右下角。地下城是由 M x N 个房间组成的二维网格。我们英勇的骑士（K）最初被安置在左上角的房间里，他必须穿过地下城并通过对抗恶魔来拯救公主。

骑士的初始健康点数为一个正整数。如果他的健康点数在某一时刻降至 0 或以下，他会立即死亡。

有些房间由恶魔守卫，因此骑士在进入这些房间时会失去健康点数（若房间里的值为负整数，则表示骑士将损失健康点数）；其他房间要么是空的（房间里的值为 0），要么包含增加骑士健康点数的魔法球（若房间里的值为正整数，则表示骑士将增加健康点数）。

为了尽快到达公主，骑士决定每次只向右或向下移动一步。

编写一个函数来计算确保骑士能够拯救到公主所需的最低初始健康点数。

例如，考虑到如下布局的地下城，如果骑士遵循最佳路径 右 -&gt; 右 -&gt; 下 -&gt; 下，则骑士的初始健康点数至少为 7。

The demons had captured the princess \(**P**\) and imprisoned her in the bottom-right corner of a dungeon. The dungeon consists of M x N rooms laid out in a 2D grid. Our valiant knight \(**K**\) was initially positioned in the top-left room and must fight his way through the dungeon to rescue the princess.

The knight has an initial health point represented by a positive integer. If at any point his health point drops to 0 or below, he dies immediately.

Some of the rooms are guarded by demons, so the knight loses health \(_negative_ integers\) upon entering these rooms; other rooms are either empty \(_0's_\) or contain magic orbs that increase the knight's health \(_positive_ integers\).

In order to reach the princess as quickly as possible, the knight decides to move only rightward or downward in each step.

**Write a function to determine the knight's minimum initial health so that he is able to rescue the princess.**

For example, given the dungeon below, the initial health of the knight must be at least **7** if he follows the optimal path `RIGHT-> RIGHT -> DOWN -> DOWN`.

| -2 \(K\) | -3 | 3 |
| :--- | :--- | :--- |
| -5 | -10 | 1 |
| 10 | 30 | -5 \(P\) |

**Note:**

* The knight's health has no upper bound.
* Any room can contain threats or power-ups, even the first room the knight enters and the bottom-right room where the princess is imprisoned.

### 分析

DFS + memoinization

时间复杂度：_O\(m∗n\)，_m,n 为 dungeon 的行数和列数。

空间复杂度：O\(m∗n\)，m,n 为 dungeon 的行数和列数。

### 代码

朴素DFS

```java
class Solution {

    public int calculateMinimumHP(int[][] dungeon) {
        return dfs(dungeon, dungeon.length, dungeon[0].length, 0, 0);
    }

    private int dfs(int[][] dungeon, int m, int n, int i, int j) {
        // 到达终点，递归终止。
        if (i == m - 1 && j == n - 1) {
            return Math.max(1 - dungeon[i][j], 1);
        }
        // 最后一行，只能向右搜索。
        if (i == m - 1) {
            return Math.max(dfs(dungeon, m, n, i, j + 1) - dungeon[i][j], 1);
        }
        // 最后一列，只能向下搜索。
        if (j == n - 1) {
           return Math.max(dfs(dungeon, m, n, i + 1, j) - dungeon[i][j], 1);
        }
        // 向下搜索 + 向右搜索，得到(i, j)点的后续路径所要求的最低血量 Math.min(dfs(i + 1, j), dfs(i, j + 1))，
        // 又因为(i, j)点本身提供血量dungeon[i][j], 因此从(i, j)开始所需的最低血量为 Math.min(dfs(i + 1, j), dfs(i, j + 1)) - dungeon[i][j]
        // 因为骑士的血量不能小于1，因此要和1取个max。
        return Math.max(Math.min(dfs(dungeon, m, n, i + 1, j), dfs(dungeon, m, n, i, j + 1)) - dungeon[i][j], 1);
    }
}
```

DFS + memoinization

```java
class Solution {
    int[][] memo; // 定义记忆化数组
    public int calculateMinimumHP(int[][] dungeon) {
        memo = new int[dungeon.length][dungeon[0].length];
        return dfs(dungeon, dungeon.length, dungeon[0].length, 0, 0);
    }

    private int dfs(int[][] dungeon, int m, int n, int i, int j) {
        // 到达终点，递归终止。
        if (i == m - 1 && j == n - 1) {
            return Math.max(1 - dungeon[i][j], 1);
        }
        // 如果memo数组中有值，直接取出并返回，不进行后续的搜索。
        if (memo[i][j] > 0) {
            return memo[i][j];
        }
        // 同解法一，向右搜+向下搜
        int minRes = 0;
        if (i == m - 1) {
            minRes =  Math.max(dfs(dungeon, m, n, i, j + 1) - dungeon[i][j], 1);
        } else if (j == n - 1) {
            minRes = Math.max(dfs(dungeon, m, n, i + 1, j) - dungeon[i][j], 1);
        } else {
            minRes = Math.max(Math.min(dfs(dungeon, m, n, i + 1, j), dfs(dungeon, m, n, i, j + 1)) - dungeon[i][j], 1);
        }
        // 将结果存入memo数组
        return memo[i][j] = minRes;
    }
}
```

## 1140 Stone Game II

### 原题

亚历克斯和李继续他们的石子游戏。许多堆石子 排成一行，每堆都有正整数颗石子 piles\[i\]。游戏以谁手中的石子最多来决出胜负。

亚历克斯和李轮流进行，亚历克斯先开始。最初，M = 1。

在每个玩家的回合中，该玩家可以拿走剩下的 前 X 堆的所有石子，其中 1 &lt;= X &lt;= 2M。然后，令 M = max\(M, X\)。

游戏一直持续到所有石子都被拿走。

假设亚历克斯和李都发挥出最佳水平，返回亚历克斯可以得到的最大数量的石头。

Alex and Lee continue their games with piles of stones.  There are a number of piles **arranged in a row**, and each pile has a positive integer number of stones `piles[i]`.  The objective of the game is to end with the most stones. 

Alex and Lee take turns, with Alex starting first.  Initially, `M = 1`.

On each player's turn, that player can take **all the stones** in the **first** `X` remaining piles, where `1 <= X <= 2M`.  Then, we set `M = max(M, X)`.

The game continues until all the stones have been taken.

Assuming Alex and Lee play optimally, return the maximum number of stones Alex can get.

**Example 1:**

```text
Input: piles = [2,7,9,4,4]
Output: 10
Explanation:  If Alex takes one pile at the beginning, Lee takes two piles, then Alex takes 2 piles again. Alex can get 2 + 4 + 4 = 10 piles in total. If Alex takes two piles at the beginning, then Lee can take all three piles left. In this case, Alex get 2 + 7 = 9 piles in total. So we return 10 since it's larger. 
```

**Constraints:**

* `1 <= piles.length <= 100`
* `1 <= piles[i] <= 10 ^ 4`

### 分析

![](../.gitbook/assets/image%20%28153%29.png)

对于piles = \[2,7,9,4,4\]，我们可以得到下图所示的dp数组，结果为dp\[0\]\[1\]

![](../.gitbook/assets/image%20%28145%29.png)

### 代码

```java
class Solution {
    public int stoneGameII(int[] piles) {
        int len = piles.length, sum = 0;
        int[][] dp = new int[len][len + 1];
        for (int i = len - 1; i >= 0; i--) {
            sum += piles[i];
            for (int M = 1; M <= len; M++) {
                if (i + 2 * M >= len) {
                    dp[i][M] = sum;
                } else {
                    for (int x = 1; x <= 2 * M; x++) {
                        dp[i][M] = Math.max(dp[i][M], sum - dp[i + x][Math.max(M, x)]);
                    }
                }
            }
        }
        return dp[0][1];
    }
}
```

记忆化递归

我们是这样设计状态的（很重要，这样的状态设计会给转移带来一定方便）：当前做决策的人，拿石子的时候得分为正，留给下一轮队手选择的时候，得分为负，因此定义的分数是 相对分数； 这一轮可以选择的石子堆数，与上一轮相关，因此需要设置一个参数 MM（和题目中的 MM 意思一样），表示当前可以选择的石子堆数； 从叶子结点开始向上每一步进行选择和比较，这样的做法才叫做「假设亚历克斯和李都发挥出最佳水平」，即每个人都让自己的利益最大化，等价于「让他人利益最小化」，这是因为石子的总数是固定的。

由于拿的都是左边的连续的石子堆，需要先计算一下前缀和，然后计算区间和； 当前区间（或者说由左边界 begin 决定的剩下的区间，右边界一定是 len - 1）里的石子堆数小于等于 2M2M 的时候，全部拿走，是使得自己获利最多的做法； 否则的话，就需要枚举拿 1 堆、2 堆、3 堆的时候，选出一个当对手利益最大化的时候，自己利益最大化的选择； 由于 memo 或者说 dfs 定义的是「相对分数」，因此输出的时候，还要做一个小的转化。

```java
public class Solution {

    // 一开始把这道题和区间 dp 联系在一起，是不对的

    public int stoneGameII(int[] piles) {
        int len = piles.length;
        int[][] memo = new int[len][len + 1];

        // [i, j] 的前缀和 preSum[j + 1] - preSum[i]
        int[] preSum = new int[len + 1];
        for (int i = 0; i < len; i++) {
            preSum[i + 1] = preSum[i] + piles[i];
        }
//        x + y = preSum[len];
//        x - y = res;
        int res = dfs(piles, 0, 1, preSum, memo);
        // 由于得到的是相对分数，需要转换成为绝对分数
        return (preSum[len] + res) / 2;
    }

    /**
     * @param piles
     * @param begin 定义石子堆的起始下标，即在 [start, len - 1] 这个区间里取石子
     * @param M     当前先手可以拿 [1, 2 * M] 堆石子（如果石子数够的话）
     * @param memo
     * @return 当前玩家在区间 [start, len - 1] 这个区间里取石子，得到的「相对分数」
     */
    private int dfs(int[] piles, int begin, int M, int[] preSum, int[][] memo) {
        int len = piles.length;
        if (begin >= len) {
            return 0;
        }

        if (memo[begin][M] != 0) {
            return memo[begin][M];
        }
        // 当前区间 [begin, len - 1] 的元素个数 len - begin <= 2M 的时候，
        // 全部拿走是利益最大的，这是因为 1 <= piles[i] <= 10 ^ 4
        if (len - begin <= 2 * M) {
            memo[begin][M] = preSum[len] - preSum[begin];
            return preSum[len] - preSum[begin];
        }

        // 走到这里，可以取的石子堆数 1 <= X <= 2M
        // 区间 [begin, j] 的长度 j - begin + 1 >= 2 * M
        int minLen = Math.min(2 * M, len - begin);
        // 这个初始化很重要，因为有可能是负分，所以不能初始化为 0
        int res = Integer.MIN_VALUE;
        for (int X = 1; X <= minLen; X++) {
            // 区间 [begin, begin + X - 1] 的前缀和 = preSum[begin + X] - preSum[begin - 1]
            int chooseLeft = preSum[begin + X] - preSum[begin];
            res = Math.max(res, chooseLeft - dfs(piles, begin + X, Math.max(M, X), preSum, memo));
        }
        memo[begin][M] = res;
        return res;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        int[] piles = new int[]{2, 7, 9, 4, 4};
        int res = solution.stoneGameII(piles);
        System.out.println(res);
    }
}
```

## 1255 Maximum Score Words Formed by Letters

### 原题

你将会得到一份单词表 words，一个字母表 letters （可能会有重复字母），以及每个字母对应的得分情况表 score。

请你帮忙计算玩家在单词拼写游戏中所能获得的「最高得分」：能够由 letters 里的字母拼写出的 任意 属于 words 单词子集中，分数最高的单词集合的得分。

单词拼写游戏的规则概述如下：

* 玩家需要用字母表 letters 里的字母来拼写单词表 words 中的单词。 
* 可以只使用字母表 letters 中的部分字母，但是每个字母最多被使用一次。 
* 单词表 words 中每个单词只能计分（使用）一次。 
* 根据字母得分情况表score，字母 'a', 'b', 'c', ... , 'z' 对应的得分分别为 score\[0\], score\[1\], ..., score\[25\]。 
* 本场游戏的「得分」是指：玩家所拼写出的单词集合里包含的所有字母的得分之和。

Given a list of `words`, list of  single `letters` \(might be repeating\) and `score` of every character.

Return the maximum score of **any** valid set of words formed by using the given letters \(`words[i]` cannot be used two or more times\).

It is not necessary to use all characters in `letters` and each letter can only be used once. Score of letters `'a'`, `'b'`, `'c'`, ... ,`'z'` is given by `score[0]`, `score[1]`, ... , `score[25]` respectively.

**Example 1:**

```text
Input: words = ["dog","cat","dad","good"], letters = ["a","a","c","d","d","d","g","o","o"], score = [1,0,9,5,0,0,3,0,0,0,0,0,0,0,2,0,0,0,0,0,0,0,0,0,0,0]
Output: 23
Explanation:
Score  a=1, c=9, d=5, g=3, o=2
Given letters, we can form the words "dad" (5+1+5) and "good" (3+2+2+5) with a score of 23.
Words "dad" and "dog" only get a score of 21.
```

**Example 2:**

```text
Input: words = ["xxxz","ax","bx","cx"], letters = ["z","a","b","c","x","x","x"], score = [4,4,4,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,5,0,10]
Output: 27
Explanation:
Score  a=4, b=4, c=4, x=5, z=10
Given letters, we can form the words "ax" (4+5), "bx" (4+5) and "cx" (4+5) with a score of 27.
Word "xxxz" only get a score of 25.
```

**Example 3:**

```text
Input: words = ["leetcode"], letters = ["l","e","t","c","o","d"], score = [0,0,1,1,1,0,0,0,0,0,0,1,0,0,1,0,0,0,0,1,0,0,0,0,0,0]
Output: 0
Explanation:
Letter "e" can only be used once.
```

**Constraints:**

* `1 <= words.length <= 14`
* `1 <= words[i].length <= 15`
* `1 <= letters.length <= 100`
* `letters[i].length == 1`
* `score.length == 26`
* `0 <= score[i] <= 10`
* `words[i]`, `letters[i]` contains only lower case English letters.

### 分析

DFS

1、再maxmaxScorewords方法当中对字母的数组进行一个数量的统计 定义一个int\[26\] 数组通过int\[c-'a'\]++ （c-'a'是字母表当中对应位置字母的索引）来存储字母的个数 

2、将统计了字母个数的数组传入另外一个数组（设置了单词索引，成立单词的值，数组）， 

3、在DFS数组当中对单词索引的大小和单词数组长度做一个判断避免索引越界 

4、将单词words\[index\]变成字符串数组，增强for循环遍历数组，然后同样通过 -'a'来得到索引，判断在字母个数数组当中该字符的个数是否大于00 

5、大于零则计算对应的socre值，小于0说明该单词没有，直接重新递归当前方法 

6、若一个成立则继续往下执行，将单词的值给成员方法sum 

7、单词有四个，可知没有遍历完单词 

8、再次递归DFS方法，num=num+sum0，传递上一个单词的值。 9、收尾

### 代码

```java
class Solution {
    int sum;
    String[] words;
    int[] score;

    public int maxScoreWords(String[] words, char[] letters, int[] score) {
        this.words = words;
        this.score = score;
        int[] charNum = new int[score.length];
        for (char ch : letters) {
            charNum[ch - 'a']++;
        }
        DFS(0, 0, charNum);
        return sum;
    }

    public void DFS(int index, int num, int[] charNum) {
        if (index >= words.length) {
            return;
        }
        int[] charNum0 = charNum.clone();//复制数组
        int sum0 = 0;
        for (char c : words[index].toCharArray()) {
            if (charNum[c - 'a'] > 0) {
                sum0 = sum0 + score[c - 'a'];
                charNum[c - 'a']--;//减去可以组成单词的字母
            } else {
                DFS(index + 1, num, charNum0);
                return;
            }
        }
        //计算一个值
        sum = Integer.max(sum, num + sum0);
        //sum=sum>num+sum0?sum:num+sum0;
        //System.out.println(num);

        //上面只能计算出一个单词的和
        DFS(index + 1, num + sum0, charNum);
        DFS(index + 1, num, charNum0);


    }
}
```

## 1499 Max Value of Equation

### 原题

满足不等式的最大值，给你一个数组 points 和一个整数 k 。数组中每个元素都表示二维平面上的点的坐标，并按照横坐标 x 的值从小到大排序。也就是说 points\[i\] = \[xi, yi\] ，并且在 1 &lt;= i &lt; j &lt;= points.length 的前提下， xi &lt; xj 总成立。

请你找出 yi + yj + \|xi - xj\| 的 最大值，其中 \|xi - xj\| &lt;= k 且 1 &lt;= i &lt; j &lt;= points.length。

题目测试数据保证至少存在一对能够满足 \|xi - xj\| &lt;= k 的点。

Given an array `points` containing the coordinates of points on a 2D plane, sorted by the x-values, where `points[i] = [xi, yi]` such that `xi < xj` for all `1 <= i < j <= points.length`. You are also given an integer `k`.

Find the _maximum value of the equation_ `yi + yj + |xi - xj|` where `|xi - xj| <= k` and `1 <= i < j <= points.length`. It is guaranteed that there exists at least one pair of points that satisfy the constraint `|xi - xj| <= k`.

**Example 1:**

```text
Input: points = [[1,3],[2,0],[5,10],[6,-10]], k = 1
Output: 4
Explanation: The first two points satisfy the condition |xi - xj| <= 1 and if we calculate the equation we get 3 + 0 + |1 - 2| = 4. Third and fourth points also satisfy the condition and give a value of 10 + -10 + |5 - 6| = 1.
No other pairs satisfy the condition, so we return the max of 4 and 1.
```

**Example 2:**

```text
Input: points = [[0,0],[3,0],[9,2]], k = 3
Output: 3
Explanation: Only the first two points have an absolute difference of 3 or less in the x-values, and give the value of 0 + 0 + |0 - 3| = 3.
```

**Constraints:**

* `2 <= points.length <= 10^5`
* `points[i].length == 2`
* `-10^8 <= points[i][0], points[i][1] <= 10^8`
* `0 <= k <= 2 * 10^8`
* `points[i][0] < points[j][0]` for all `1 <= i < j <= points.length`
* `xi` form a strictly increasing sequence.

### 分析

线段树，优先队列，红黑树，单调队列，四种解法，本质一样

使用双端队列，首先将原表达式化为yi-xi+yj+xj的形式，其中i&gt;j且xi-xj&lt;=k，我们在固定j的值后，问题就变成了寻找区间内的最大值，我们从大到小遍历j，那么区间就是一个滑动窗口，我们参考滑动窗口中的最大值中使用双端队列的解法，由于每个元素最多进一次和出一次队列，所以时间复杂度为O\(n\)。

### 代码

```java
class Solution {
    int deque[];
    int head, rear, res;

    public void push_que(int p, int[][] points) {
        while (head != rear && points[deque[rear - 1]][1] - points[deque[rear - 1]][0] < points[p][1] - points[p][0]) {
            rear--;
        }
        deque[rear++] = p;
    }

    public void delete_que(int p) {
        if (head != rear && p == deque[head]) head++;
    }

    public int get_max(int[][] points) {
        return points[deque[head]][1] - points[deque[head]][0];
    }

    public int findMaxValueOfEquation(int[][] points, int k) {
        deque = new int[100004];
        head = 0;
        rear = 0;
        res = -Integer.MAX_VALUE;
        int n = points.length - 1, now = n - 1;
        for (int i = n; i > 0; i--) {
            delete_que(i);
            int j = i - 1;
            while (j > now && points[i][0] - points[j][0] > k) {
                delete_que(j);
                j--;
            }
            if (now >= i) now = i - 1;
            while (now >= 0 && points[i][0] - points[now][0] <= k) {
                push_que(now, points);
                now--;
            }
            //System.out.println(deque[head]==i);
            if (rear != head) res = Math.max(res, get_max(points) + points[i][0] + points[i][1]);
        }
        return res;
    }
}
```

## 


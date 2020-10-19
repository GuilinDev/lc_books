# tag3

## 815 Bus Route

### 原题

我们有一系列公交路线。每一条路线 routes\[i\] 上都有一辆公交车在上面循环行驶。例如，有一条路线 routes\[0\] = \[1, 5, 7\]，表示第一辆 \(下标为0\) 公交车会一直按照 1-&gt;5-&gt;7-&gt;1-&gt;5-&gt;7-&gt;1-&gt;... 的车站路线行驶。

假设我们从 S 车站开始（初始时不在公交车上），要去往 T 站。 期间仅可乘坐公交车，求出最少乘坐的公交车数量。返回 -1 表示不可能到达终点车站。

We have a list of bus routes. Each `routes[i]` is a bus route that the i-th bus repeats forever. For example if `routes[0] = [1, 5, 7]`, this means that the first bus \(0-th indexed\) travels in the sequence 1-&gt;5-&gt;7-&gt;1-&gt;5-&gt;7-&gt;1-&gt;... forever.

We start at bus stop `S` \(initially not on a bus\), and we want to go to bus stop `T`. Travelling by buses only, what is the least number of buses we must take to reach our destination? Return -1 if it is not possible.

```text
Example:
Input: 
routes = [[1, 2, 7], [3, 6, 7]]
S = 1
T = 6
Output: 2
Explanation: 
The best strategy is take the first bus to the bus stop 7, then take the second bus to the bus stop 6.
```

**Constraints:**

* `1 <= routes.length <= 500`.
* `1 <= routes[i].length <= 10^5`.
* `0 <= routes[i][j] < 10 ^ 6`.

### 分析

构建图，BFS

我们将每一条公交路线（而不是每一个车站）看成图中的一个点，如果两条公交路线有交集，那么它们在图中对应的点之间就有一条边。此外，起点站 S 和终点站 T 也分别是图中的一个点，如果一条公交路线包含了 S 或 T，那么也需要和 S 或 T 对应的点连一条边。此时，在这个图上从 S 到 T 的最短路径长度即为答案，我们可以用广度优先搜索来找出最短路径。

在计算两条公交路线是否有交集时，可以用的方法有很多种。例如将公交路线放在集合中，检查两个集合的交集是否为空；或者将公交路线中的车站进行递增排序，并使用双指针的方法检查是否有相同的车站。

时间复杂度：设 N 为公交路线的总数，b\_i为第 i 条公交路线中的车站数目，在建图时，我们的时间集中在判断两条公交路线是否有交集上。在广度优先搜索时，包含 N 个点的图的边数最大可以达到 O\(N^2\)，因此时间复杂度为 O\(N^2\)。

空间复杂度：O\(N^2\)，用来存储图。

### 代码

```java
class Solution {
    public int numBusesToDestination(int[][] routes, int S, int T) {
        if (S == T) return 0;
        int N = routes.length;

        List<List<Integer>> graph = new ArrayList();
        for (int i = 0; i < N; ++i) {
            Arrays.sort(routes[i]);
            graph.add(new ArrayList());
        }
        Set<Integer> seen = new HashSet();
        Set<Integer> targets = new HashSet();
        Queue<Point> queue = new ArrayDeque();

        // Build the graph.  Two buses are connected if
        // they share at least one bus stop.
        for (int i = 0; i < N; ++i)
            for (int j = i + 1; j < N; ++j)
                if (intersect(routes[i], routes[j])) {
                    graph.get(i).add(j);
                    graph.get(j).add(i);
                }

        // Initialize seen, queue, targets.
        // seen represents whether a node has ever been enqueued to queue.
        // queue handles our breadth first search.
        // targets is the set of goal states we have.
        for (int i = 0; i < N; ++i) {
            if (Arrays.binarySearch(routes[i], S) >= 0) {
                seen.add(i);
                queue.offer(new Point(i, 0));
            }
            if (Arrays.binarySearch(routes[i], T) >= 0)
                targets.add(i);
        }

        while (!queue.isEmpty()) {
            Point info = queue.poll();
            int node = info.x, depth = info.y;
            if (targets.contains(node)) return depth + 1;
            for (Integer nei : graph.get(node)) {
                if (!seen.contains(nei)) {
                    seen.add(nei);
                    queue.offer(new Point(nei, depth + 1));
                }
            }
        }

        return -1;
    }

    public boolean intersect(int[] A, int[] B) {
        int i = 0, j = 0;
        while (i < A.length && j < B.length) {
            if (A[i] == B[j]) return true;
            if (A[i] < B[j]) i++;
            else j++;
        }
        return false;
    }
}
```

## 1062 Longest Repeating Substring

### 原题

 给定字符串 `S`，找出最长重复子串的长度。如果不存在重复子串就返回 `0`。

Given a string `S`, find out the length of the longest repeating substring\(s\). Return `0` if no repeating substring exists.

**Example 1:**

```text
Input: S = "abcd"
Output: 0
Explanation: There is no repeating substring.
```

**Example 2:**

```text
Input: S = "abbaba"
Output: 2
Explanation: The longest repeating substrings are "ab" and "ba", each of which occurs twice.
```

**Example 3:**

```text
Input: S = "aabcaabdaab"
Output: 3
Explanation: The longest repeating substring is "aab", which occurs 3 times.
```

**Example 4:**

```text
Input: S = "aaaaa"
Output: 4
Explanation: The longest repeating substring is "aaaa", which occurs twice.
```

**Constraints:**

* The string `S` consists of only lowercase English letters from `'a'` - `'z'`.
* `1 <= S.length <= 1500`

### 分析

由于数据范围的限制，我们必须找到一个时间复杂度低于暴力 O\(N^2\) 的方法。

1）包含子串的哈希集合

这种方法实现起来非常直接：

使用滑动窗口来遍历所有长度为 L 的子串； 检查当前子串是否已经出现在集合中： 如果已经出现，就说明找到了长度为 L 的重复子串； 如果没有出现，我们将当前子串加入集合中

缺点也很显然，直接将字符串存储在集合中会占用大量的空间

时间复杂度：在平均情况下，为 O\(NlogN\)，最坏情况下，L 为 N/2，可以达到 O\(N^2\)。

空间复杂度：O\(N^2\)，为哈希集合占用的空间。

2）包含子串哈希值的哈希集合

为了减少方法一中的空间复杂度，我们可以在哈希集合中存储字符串的哈希值，而不是字符串的本身，别的都一样。

3）Rabin-Karp 字符串哈希算法

![](../.gitbook/assets/image%20%28156%29.png)

![](../.gitbook/assets/image%20%28178%29.png)

时间复杂度：O\(NlogN\)，二分查找的时间复杂度为 O\(logN\)，Rabin-Karp 字符串哈希的时间复杂度为 O\(N\)。

空间复杂度：O\(N\)，为哈希集合占用的空间。

### 代码

1）包含子串的哈希集合

```java
class Solution {
    /*
        Search a substring of given length
        that occurs at least 2 times.
        Return start position if the substring exits and -1 otherwise.
    */
    public int search(int L, int n, String S) {
        HashSet<String> seen = new HashSet();
        String tmp;
        for (int start = 0; start < n - L + 1; ++start) {
            tmp = S.substring(start, start + L);
            if (seen.contains(tmp)) return start;
            seen.add(tmp);
        }
        return -1;
    }

    public int longestRepeatingSubstring(String S) {
        int n = S.length();
        // binary search, L = repeating string length
        int left = 1, right = n;
        int L;
        while (left <= right) {
            L = left + (right - left) / 2;
            if (search(L, n, S) != -1) left = L + 1;
            else right = L - 1;
        }

        return left - 1;
    }
}
```

2）包含子串哈希值的哈希集合

```java
class Solution {
    /*
        Search a substring of given length
        that occurs at least 2 times.
        Return start position if the substring exits and -1 otherwise.
    */
    public int search(int L, int n, String S) {
        HashSet<Integer> seen = new HashSet();
        String tmp;
        int h;
        for (int start = 0; start < n - L + 1; ++start) {
            tmp = S.substring(start, start + L);
            h = tmp.hashCode();
            if (seen.contains(h)) return start;
            seen.add(h);
        }
        return -1;
    }

    public int longestRepeatingSubstring(String S) {
        int n = S.length();
        // binary search, L = repeating string length
        int left = 1, right = n;
        int L;
        while (left <= right) {
            L = left + (right - left) / 2;
            if (search(L, n, S) != -1) left = L + 1;
            else right = L - 1;
        }

        return left - 1;
    }
}
```

3）Rabin-Karp 字符串哈希算法

```java
class Solution {
    /*
      Rabin-Karp with polynomial rolling hash.
      Search a substring of given length
      that occurs at least 2 times.
      Return start position if the substring exits and -1 otherwise.
    */

    public int search(int L, int a, long modulus, int n, int[] nums) {
        // compute the hash of string S[:L]
        long h = 0;
        for (int i = 0; i < L; ++i) h = (h * a + nums[i]) % modulus;

        // already seen hashes of strings of length L
        HashSet<Long> seen = new HashSet();
        seen.add(h);
        // const value to be used often : a**L % modulus
        long aL = 1;
        for (int i = 1; i <= L; ++i) aL = (aL * a) % modulus;

        for(int start = 1; start < n - L + 1; ++start) {
            // compute rolling hash in O(1) time
            h = (h * a - nums[start - 1] * aL % modulus + modulus) % modulus;
            h = (h + nums[start + L - 1]) % modulus;
            if (seen.contains(h)) return start;
            seen.add(h);
        }
        return -1;
    }

    public int longestRepeatingSubstring(String S) {
        int n = S.length();
        // convert string to array of integers
        // to implement constant time slice
        int[] nums = new int[n];
        for (int i = 0; i < n; ++i) nums[i] = (int)S.charAt(i) - (int)'a';
        // base value for the rolling hash function
        int a = 26;
        // modulus value for the rolling hash function to avoid overflow
        long modulus = (long)Math.pow(2, 24);

        // binary search, L = repeating string length
        int left = 1, right = n;
        int L;
        while (left <= right) {
            L = left + (right - left) / 2;
            if (search(L, a, modulus, n, nums) != -1) left = L + 1;
            else right = L - 1;
        }

        return left - 1;
    }
}
```

## 1377 Frog Position After T Seconds

### 原题

T 秒后青蛙的位置

给你一棵由 n 个顶点组成的无向树，顶点编号从 1 到 n。青蛙从 顶点 1 开始起跳。规则如下：

* 在一秒内，青蛙从它所在的当前顶点跳到另一个 未访问 过的顶点（如果它们直接相连）。 
* 青蛙无法跳回已经访问过的顶点。 
* 如果青蛙可以跳到多个不同顶点，那么它跳到其中任意一个顶点上的机率都相同。 
* 如果青蛙不能跳到任何未访问过的顶点上，那么它每次跳跃都会停留在原地。 无向树的边用数组 edges 描述，其中 edges\[i\] = \[fromi, toi\] 意味着存在一条直接连通 fromi 和 toi 两个顶点的边。

返回青蛙在 t 秒后位于目标顶点 target 上的概率。

Given an undirected tree consisting of `n` vertices numbered from 1 to `n`. A frog starts jumping from the **vertex 1**. In one second, the frog jumps from its current vertex to another **unvisited** vertex if they are directly connected. The frog can not jump back to a visited vertex. In case the frog can jump to several vertices it jumps randomly to one of them with the same probability, otherwise, when the frog can not jump to any unvisited vertex it jumps forever on the same vertex. 

The edges of the undirected tree are given in the array `edges`, where `edges[i] = [fromi, toi]` means that exists an edge connecting directly the vertices `fromi` and `toi`.

_Return the probability that after `t` seconds the frog is on the vertex `target`._

**Example 1:**

![](https://assets.leetcode.com/uploads/2020/02/20/frog_2.png)

```text
Input: n = 7, edges = [[1,2],[1,3],[1,7],[2,4],[2,6],[3,5]], t = 2, target = 4
Output: 0.16666666666666666 
Explanation: The figure above shows the given graph. The frog starts at vertex 1, jumping with 1/3 probability to the vertex 2 after second 1 and then jumping with 1/2 probability to vertex 4 after second 2. Thus the probability for the frog is on the vertex 4 after 2 seconds is 1/3 * 1/2 = 1/6 = 0.16666666666666666. 
```

**Example 2:**

![](https://assets.leetcode.com/uploads/2020/02/20/frog_3.png)

```text
Input: n = 7, edges = [[1,2],[1,3],[1,7],[2,4],[2,6],[3,5]], t = 1, target = 7
Output: 0.3333333333333333
Explanation: The figure above shows the given graph. The frog starts at vertex 1, jumping with 1/3 = 0.3333333333333333 probability to the vertex 7 after second 1. 
```

**Example 3:**

```text
Input: n = 7, edges = [[1,2],[1,3],[1,7],[2,4],[2,6],[3,5]], t = 20, target = 6
Output: 0.16666666666666666
```

**Constraints:**

* `1 <= n <= 100`
* `edges.length == n-1`
* `edges[i].length == 2`
* `1 <= edges[i][0], edges[i][1] <= n`
* `1 <= t <= 50`
* `1 <= target <= n`
* Answers within `10^-5` of the actual value will be accepted as correct.

### 分析

先建立树

1）BFS，注意到达目标点后还有时间和节点会让概率置0

2）DFS回溯，注意选择下一步时减去相应的概率，就会让跳过的点概率归0

### 代码

BFS

```java
class Solution {
    HashMap<Integer, List<Integer>> map = new HashMap<>();

    public double frogPosition(int n, int[][] edges, int t, int target) {
        for (int i = 0; i < edges.length; i++) {
            if (map.containsKey(edges[i][0])) {
                map.get(edges[i][0]).add(edges[i][1]);
            } else {
                List<Integer> ls = new ArrayList<>();
                ls.add(edges[i][1]);
                map.put(edges[i][0], ls);
            }
            if (map.containsKey(edges[i][1])) {
                map.get(edges[i][1]).add(edges[i][0]);
            } else {
                List<Integer> ls = new ArrayList<>();
                ls.add(edges[i][0]);
                map.put(edges[i][1], ls);
            }
        }
        int[] flag = new int[n + 1];

        Queue<Integer> queue = new LinkedList<>();
        double[] res = new double[n + 1];
        res[1] = 1.0;
        flag[1] = 1;
        queue.offer(1);
        while (!queue.isEmpty()) {
            if (res[target] != 0 || t == 0) {
                break;
            }
            int num = queue.size();
            for (int i = 0; i < num; i++) {
                int cur = queue.poll();
                List<Integer> ls = map.get(cur);
                if (ls != null) {
                    for (int x : ls) {
                        if (flag[x] == 0) {
                            if (cur != 1) {
                                res[x] = res[cur] * 1.0 / (double) (ls.size() - 1);
                            } else {
                                res[x] = res[cur] * 1.0 / (double) (ls.size());
                            }
                            flag[x] = 1;
                            queue.offer(x);
                        }
                    }
                }
            }
            t--;
        }
        if (t > 0) {
            List<Integer> ls = map.get(target);
            if (ls != null) {
                for (int m : ls) {
                    if (flag[m] == 0) {
                        return 0.0;
                    }
                }
            }
        }
        return res[target];
    }
}
```

DFS

```java
class Solution {

    int[] visited = new int[105];
    double[] res = new double[105];
    HashMap<Integer, List<Integer>> map = new HashMap<>();

    public double frogPosition(int n, int[][] edges, int t, int target) {

        for (int i = 0; i < edges.length; i++) {
            if (map.containsKey(edges[i][0])) {
                map.get(edges[i][0]).add(edges[i][1]);
            } else {
                List<Integer> ls = new ArrayList<>();
                ls.add(edges[i][1]);
                map.put(edges[i][0], ls);
            }
            if (map.containsKey(edges[i][1])) {
                map.get(edges[i][1]).add(edges[i][0]);
            } else {
                List<Integer> ls = new ArrayList<>();
                ls.add(edges[i][0]);
                map.put(edges[i][1], ls);
            }
        }
        visited[1] = 1;
        res[1] = 1.0;
        dfs(1, t);
        return res[target];
    }

    public void dfs(int i, int t) {
        if (t == 0) return;
        List<Integer> ls = map.get(i);
        if (ls == null) return;
        double p = i == 1 ? res[i] / ls.size() : res[i] / (ls.size() - 1);
        for (int next : ls) {
            if (visited[next] != 1) {
                visited[next] = 1;
                res[i] -= p;
                res[next] += p;
                dfs(next, t - 1);
                visited[next] = 0;
            }
        }

    }
}
```

## 375 Guess Number Higher or Lower II

### 原题

我们正在玩一个猜数游戏，游戏规则如下：

我从 1 到 n 之间选择一个数字，你来猜我选了哪个数字。

每次你猜错了，我都会告诉你，我选的数字比你的大了或者小了。

然而，当你猜了数字 x 并且猜错了的时候，你需要支付金额为 x 的现金。直到你猜到我选的数字，你才算赢得了这个游戏。

We are playing the Guessing Game. The game will work as follows:

1. I pick a number between `1` and `n`.
2. You guess a number.
3. If you guess the right number, **you win the game**.
4. If you guess the wrong number, then I will tell you whether the number I picked is **higher or lower**, and you will continue guessing.
5. Every time you guess a wrong number `x`, you will pay `x` dollars. If you run out of money, **you lose the game**.

Given a particular `n`, return _the minimum amount of money you need to **guarantee a win regardless of what number I pick**_.

**Example 1:**![](https://assets.leetcode.com/uploads/2020/09/10/graph.png)

```text
Input: n = 10
Output: 16
Explanation: The winning strategy is as follows:
- The range is [1,10]. Guess 7.
    - If this is my number, your total is $0. Otherwise, you pay $7.
    - If my number is higher, the range is [8,10]. Guess 9.
        - If this is my number, your total is $7. Otherwise, you pay $9.
        - If my number is higher, it must be 10. Guess 10. Your total is $7 + $9 = $16.
        - If my number is lower, it must be 8. Guess 8. Your total is $7 + $9 = $16.
    - If my number is lower, the range is [1,6]. Guess 3.
        - If this is my number, your total is $7. Otherwise, you pay $3.
        - If my number is higher, the range is [4,6]. Guess 5.
            - If this is my number, your total is $7 + $3 = $10. Otherwise, you pay $5.
            - If my number is higher, it must be 6. Guess 6. Your total is $7 + $3 + $5 = $15.
            - If my number is lower, it must be 4. Guess 4. Your total is $7 + $3 + $5 = $15.
        - If my number is lower, the range is [1,2]. Guess 1.
            - If this is my number, your total is $7 + $3 = $10. Otherwise, you pay $1.
            - If my number is higher, it must be 2. Guess 2. Your total is $7 + $3 + $1 = $11.
The worst case in all these scenarios is that you pay $16. Hence, you only need $16 to guarantee a win.
```

**Example 2:**

```text
Input: n = 1
Output: 0
Explanation: There is only one possible number, so you can guess 1 and not have to pay anything.
```

**Example 3:**

```text
Input: n = 2
Output: 1
Explanation: There are two possible numbers, 1 and 2.
- Guess 1.
    - If this is my number, your total is $0. Otherwise, you pay $1.
    - If my number is higher, it must be 2. Guess 2. Your total is $1.
The worst case is that you pay $1.
```

**Constraints:**

* `1 <= n <= 200`

### 分析

1）暴力解（TLE） 时间复杂度: O\(N!\) 空间复杂度： O\(N\) 

我们首先在\(1, n\)中任意挑选一个数字i， 假设i是错误的（最坏情况）， 我们需要用最小代价去 猜到需要的数字。那么在一次尝试后，答案要么在i的左边， 要么在i的右边， 为了考虑最差的情况，我们 需要考虑两者的较大值。 cost\(1,n\) = i + max\( cost\(1, i - 1\), cost\(i + 1\), n\)\)

2）DP，从暴力解可以看出， 大区间的猜数字的成本求解， 可以由小区间计算出来。  
时间复杂度： O\(N ^ 3\), 空间复杂度O\(N ^ 2\)

![](../.gitbook/assets/image%20%28149%29.png)

### 代码

1）暴力

```java
class Solution {
    public int getMoneyAmount(int n){
        return cost(1, n);
    }
    public int cost(int l, int r){
        if(l >= r)  return 0;
        int min = Integer.MAX_VALUE;
        for(int i = l; i <= r; i++){
            int res = i + Math.max(cost(l, i - 1), cost(i + 1, r));
            min = Math.min(min, res);
        }
        return min;
    }
}
```

2）DP

```java
class Solution {
        public int getMoneyAmount(int n){
        int[][] dp = new int[n + 1][n + 1];
        for(int len = 2; len <= n; len ++){
            for(int i = 1; i + len - 1 <= n; i++){
                int j = i + len - 1;
                dp[i][j] = Integer.MAX_VALUE;
                for(int k = i; k <= j; k++){
                    dp[i][j] = Math.min(dp[i][j],
                            k + Math.max(k <= 1 ? 0 : dp[i][k - 1],
                                    k + 1 > j ? 0: dp[k + 1][j]));
                }
            }
        }
        return dp[1][n];
    }
}
```

## 1326 Minimum Number of Taps to Open to Water a Garden

### 原题

在 x 轴上有一个一维的花园。花园长度为 n，从点 0 开始，到点 n 结束。

花园里总共有 n + 1 个水龙头，分别位于 \[0, 1, ..., n\] 。

给你一个整数 n 和一个长度为 n + 1 的整数数组 ranges ，其中 ranges\[i\] （下标从 0 开始）表示：如果打开点 i 处的水龙头，可以灌溉的区域为 \[i - ranges\[i\], i + ranges\[i\]\] 。

请你返回可以灌溉整个花园的 最少水龙头数目 。如果花园始终存在无法灌溉到的地方，请你返回 -1 。

There is a one-dimensional garden on the x-axis. The garden starts at the point `0` and ends at the point `n`. \(i.e The length of the garden is `n`\).

There are `n + 1` taps located at points `[0, 1, ..., n]` in the garden.

Given an integer `n` and an integer array `ranges` of length `n + 1` where `ranges[i]` \(0-indexed\) means the `i-th` tap can water the area `[i - ranges[i], i + ranges[i]]` if it was open.

Return _the minimum number of taps_ that should be open to water the whole garden, If the garden cannot be watered return **-1**.

**Example 1:**![](https://assets.leetcode.com/uploads/2020/01/16/1685_example_1.png)

```text
Input: n = 5, ranges = [3,4,1,1,0,0]
Output: 1
Explanation: The tap at point 0 can cover the interval [-3,3]
The tap at point 1 can cover the interval [-3,5]
The tap at point 2 can cover the interval [1,3]
The tap at point 3 can cover the interval [2,4]
The tap at point 4 can cover the interval [4,4]
The tap at point 5 can cover the interval [5,5]
Opening Only the second tap will water the whole garden [0,5]
```

**Example 2:**

```text
Input: n = 3, ranges = [0,0,0,0]
Output: -1
Explanation: Even if you activate all the four taps you cannot water the whole garden.
```

**Example 3:**

```text
Input: n = 7, ranges = [1,2,1,0,2,1,0,1]
Output: 3
```

**Example 4:**

```text
Input: n = 8, ranges = [4,0,0,0,0,0,0,0,4]
Output: 2
```

**Example 5:**

```text
Input: n = 8, ranges = [4,0,0,0,4,0,0,0,4]
Output: 1
```

**Constraints:**

* `1 <= n <= 10^4`
* `ranges.length == n + 1`
* `0 <= ranges[i] <= 100`

### 分析

1）DP，O\(N ^ 2\)

![](../.gitbook/assets/image%20%28150%29.png)

2）DP小优化，`O(N * maxRange)`maxrange 为所有水龙头中最大的灌溉区域长度。

![](../.gitbook/assets/image%20%28168%29.png)

3）贪心

可以将问题转换一下，水龙头浇灌的范围是一个区间，花园的范围也是一个区间。求最小水龙头数，实质上就是求能够覆盖花园整个区间的最小区间数。 那么我们就可以利用贪心来求解问题了。我们的目的是在能够覆盖当前最左范围的所有水龙头中选择灌溉最右范围最大的水龙头。

1.将水龙头按照其能灌溉到的最右位置进行排序； 

2.从后往前查找第一个能够灌溉到花园最左边的水龙头，就是我们第一个选择的水龙头\(因为我们是按照最右位置的大小排序的\) 

3.更新左范围为当前所选水龙头最右位置，并删除当前水龙头之前的水龙头即可\(并不是真的删除，改一下可用水龙头的index即可\) 重复2，3即可。

排序了，O\(nlogn\)

### 代码

1）DP

```java
class Solution {

    class Interval {
        int start;
        int end;
        Interval(int start, int end){
            this.start = start;
            this.end = end;
        }
    }
    
    public int minTaps(int n, int[] ranges) {

        List<Interval> intervals = new ArrayList<>();
        int pos = 0;
        // 得到所有灌溉区间。
        for (int range: ranges){
            intervals.add(new Interval(pos - range, pos + range));
            pos += 1;
        }
        
        int size = intervals.size();
        // n + 1个点一共有 n 个区间。 dp矩阵表示前i个区间所需最小的taps
        int[] dp = new int[n + 1];
        dp[0] = 0;
        for (int i = 0; i < n; i++){
            dp[i + 1] = Integer.MAX_VALUE;
            // 枚举所有覆盖它的区间。 
            for (int j =0; j < size; j++){
                if (intervals.get(j).end < i + 1 || intervals.get(j).start > i){
                    continue;
                }
                // 满足：intervals.get(j).start <= i &&  intervals.get(j).end >= i + 1                
                int start = intervals.get(j).start >= 0 ? intervals.get(j).start : 0;
                
                if (dp[start] != Integer.MAX_VALUE){
                    dp[i + 1] = Math.min(dp[i + 1], dp[start] + 1);
                }

            }
        }
        return dp[n] == Integer.MAX_VALUE ? -1 : dp[n];
    }
}
```

2）DP优化

```java
class Solution {

    public int minTaps(int n, int[] ranges) {
        int[] dp = new int[n + 1];
        Arrays.fill(dp, Integer.MAX_VALUE);

        // dp[i] 表示为了覆盖区间 [0,i] 所需要 的最小taps 即前i个数为止的区间。
        dp[0] = 0; 

        // 遍历所有水龙头的位置
        for (int i = 0; i <= n; i++){
            // 找到当前处的tap能覆盖的最左和最右的位置
            int left = Math.max(i- ranges[i], 0);
            int right = Math.min(i + ranges[i], n);
            for (int j = left + 1; j <= right;j++){
                //查看如果用这个tap去覆盖这些位置,所用的水龙头的位置均为前【0，left】区间所需要的水龙头的数量 + 1
                if (dp[left] != Integer.MAX_VALUE){
                     dp[j] = Math.min(dp[j], dp[left] + 1);
                }
            }
        }
        return dp[n] == Integer.MAX_VALUE ? -1: dp[n];
    }
}
```

3）贪心

```java
class Solution {
	//水龙头范围
    class Range{
        public Range(int l, int r) {
        	this.l = l;
        	this.r = r;
        }
        int l;
        int r;
    }
	//计数君
	class IntHolder{
		int cnt = 0;
	}

	public int minTaps(int n, int[] ranges) {
    	Range[] Ranges = new Range[ranges.length];
    	for(int i = 0; i < ranges.length; i++) {
    		Range ran = new Range(i-ranges[i], i+ranges[i]);
    		Ranges[i] = ran;
    	}
    	
	//将水龙头按最右位置排序
    	Arrays.sort(Ranges, new Comparator<Range>() {
    		@Override
    		public int compare(Range r1, Range r2) {
    			return r1.r-r2.r;
    		}
    	});
    	
    	IntHolder intholder = new IntHolder();
    	
    	boolean flag = match(0, n, 0, Ranges, intholder);
    	
    	if(flag) return intholder.cnt;
    	return -1;
    }
    
	/**
     * 是否能够灌溉到当前左边界到右边界
     * 
     * @param lrange 左边界
     * @param rlange 右边界
     * @param lindex 可选水龙头的起始索引
     * @param Ranges 水龙头范围数组
     * @param intholder 计数君
     * @return 是否能够灌溉到当前lrange-rlange
     */
    public boolean match(int lrange, int rlange, int lindex, Range[] Ranges, IntHolder intholder) {
    	boolean isMatch = false;
    	for(int i = Ranges.length-1; i >= lindex; i--) {
    		//灌溉了整个区间
    		if(Ranges[i].l <= lrange && Ranges[i].r >= rlange) {
    			intholder.cnt++;
    			return true;
    		}
    		//能灌溉到左边界
    		if(Ranges[i].l <= lrange) {
    			intholder.cnt++;
    			isMatch = match(Ranges[i].r, rlange, i+1, Ranges, intholder);
    			break;
    		}
    	}
	//若不能灌溉到当前左边界，则返回false，能灌溉到则返回true
    	return isMatch;
    }
}
```

## 871 Minimum Number of Refueling Stops

### 原题

最低加油次数，汽车从起点出发驶向目的地，该目的地位于出发位置东面 target 英里处。

沿途有加油站，每个 station\[i\] 代表一个加油站，它位于出发位置东面 station\[i\]\[0\] 英里处，并且有 station\[i\]\[1\] 升汽油。

假设汽车油箱的容量是无限的，其中最初有 startFuel 升燃料。它每行驶 1 英里就会用掉 1 升汽油。

当汽车到达加油站时，它可能停下来加油，将所有汽油从加油站转移到汽车中。

为了到达目的地，汽车所必要的最低加油次数是多少？如果无法到达目的地，则返回 -1 。

注意：如果汽车到达加油站时剩余燃料为 0，它仍然可以在那里加油。如果汽车到达目的地时剩余燃料为 0，仍然认为它已经到达目的地。

A car travels from a starting position to a destination which is `target` miles east of the starting position.

Along the way, there are gas stations.  Each `station[i]` represents a gas station that is `station[i][0]` miles east of the starting position, and has `station[i][1]` liters of gas.

The car starts with an infinite tank of gas, which initially has `startFuel` liters of fuel in it.  It uses 1 liter of gas per 1 mile that it drives.

When the car reaches a gas station, it may stop and refuel, transferring all the gas from the station into the car.

What is the least number of refueling stops the car must make in order to reach its destination?  If it cannot reach the destination, return `-1`.

Note that if the car reaches a gas station with 0 fuel left, the car can still refuel there.  If the car reaches the destination with 0 fuel left, it is still considered to have arrived.

**Example 1:**

```text
Input: target = 1, startFuel = 1, stations = []
Output: 0
Explanation: We can reach the target without refueling.
```

**Example 2:**

```text
Input: target = 100, startFuel = 1, stations = [[10,100]]
Output: -1
Explanation: We can't reach the target (or even the first gas station).
```

**Example 3:**

```text
Input: target = 100, startFuel = 10, stations = [[10,60],[20,30],[30,30],[60,40]]
Output: 2
Explanation: 
We start with 10 liters of fuel.
We drive to position 10, expending 10 liters of fuel.  We refuel from 0 liters to 60 liters of gas.
Then, we drive from position 10 to position 60 (expending 50 liters of fuel),
and refuel from 10 liters to 50 liters of gas.  We then drive to and reach the target.
We made 2 refueling stops along the way, so we return 2.
```

**Note:**

1. `1 <= target, startFuel, stations[i][1] <= 10^9`
2. `0 <= stations.length <= 500`
3. `0 < stations[0][0] < stations[1][0] < ... < stations[stations.length-1][0] < target`

### 分析

一开始看到这个题， 多各油站， 要么加油，要么不加油， 油量要大于等于0， 这TM不是01背包问题吗 结果， 这个油量的取值范围是1e9， 搞了半天， 弄个暴搜出来TLE了 最后无奈无看题解， 这集合划分也实在太给力点。。。确实想不出来。。。

**思路**

dp\[i\] 为加 i 次油能走的最远距离，需要满足 dp\[i\] &gt;= target 的最小 i。

**算法**

依次计算每个 dp\[i\]，对于 dp\[0\]，就只用初始的油量 startFuel 看能走多远。

每多一个加油站 station\[i\] = \(location, capacity\)，如果之前可以通过加 t 次油到达这个加油站，现在就可以加 t+1 次油得到 capcity 的油量。

举个例子，原本加一次油可以行驶的最远距离为 15，现在位置 10 有一个加油站，有 30 升油量储备，那么显然现在可以加两次油行驶 45 距离。

![](../.gitbook/assets/image%20%28157%29.png)



* 时间复杂度： O\(N^2\)，其中 N 为加油站的个数。
* 空间复杂度： O\(N\)，`dp` 数组占用的空间。

### 代码

```java
class Solution {
        public int minRefuelStops(int target, int startFuel, int[][] stations){
        int n = stations.length;
        if(n == 0) return startFuel >= target ? 0: -1;
        int[] dp = new int[n + 1];
        dp[0] = startFuel;
        for(int i = 1; i <= n; i++){
            int used = -1;
            for(int k = 1; k <= n && stations[k - 1][0] <= dp[i - 1]; k++){
                if(dp[i - 1] + stations[k - 1][1] > dp[i]){
                    dp[i] = dp[i - 1] + stations[k - 1][1];
                    used = k;
                }
            }
            //如果这个加油站加过油了， 把油量置为0
            if(used != -1)  stations[used - 1][1] = 0;
        }
        int ans = n + 10;
        for(int i = 0; i <= n; i++){
            if(dp[i] >= target) ans = Math.min(ans, i);
        }
        return ans == n + 10 ? -1: ans;
    }
}
```

```java
class Solution {
    public int minRefuelStops(int target, int startFuel, int[][] stations) {
        int N = stations.length;
        long[] dp = new long[N + 1];
        dp[0] = startFuel;
        for (int i = 0; i < N; ++i)
            for (int t = i; t >= 0; --t)
                if (dp[t] >= stations[i][0])
                    dp[t+1] = Math.max(dp[t+1], dp[t] + (long) stations[i][1]);

        for (int i = 0; i <= N; ++i)
            if (dp[i] >= target) return i;
        return -1;
    }
}
```

## 420 Strong Password Checker

### 原题

一个强密码应满足以下所有条件：

1. 由至少6个，至多20个字符组成。 
2. 至少包含一个小写字母，一个大写字母，和一个数字。 
3. 同一字符不能连续出现三次 \(比如 "...aaa..." 是不允许的, 但是 "...aa...a..." 是可以的\)。

 编写函数 strongPasswordChecker\(s\)，s 代表输入字符串，如果 s 已经符合强密码条件，则返回0；否则返回要将 s 修改为满足强密码条件的字符串所需要进行修改的最小步数。

插入、删除、替换任一字符都算作一次修改。

A password is considered strong if below conditions are all met:

1. It has at least 6 characters and at most 20 characters.
2. It must contain at least one lowercase letter, at least one uppercase letter, and at least one digit.
3. It must NOT contain three repeating characters in a row \("...aaa..." is weak, but "...aa...a..." is strong, assuming other conditions are met\).

Write a function strongPasswordChecker\(s\), that takes a string s as input, and return the **MINIMUM** change required to make s a strong password. If s is already strong, return 0.

Insertion, deletion or replace of any one character are all considered as one change.

### 分析

就是先把一段段连续的长度放进优先队列，队列以len % 3的大小判断优先级，然后根据原来长度是小于6还是6-20还是20以上分别讨论。用了优先队列可以避免一些复杂操作。

### 代码

```java
class Solution {
    public int strongPasswordChecker(String s) {
        int len = s.length();
        if (len == 0) return 6;
        char[] ss = s.toCharArray();

        // 记录所需小写字母、大写字母和数字的个数
        // cnt记录重复序列的字符计数
        int low = 1, up = 1, num = 1, cnt = 1;
        if (Character.isLowerCase(ss[0])) low = 0;
        else if (Character.isUpperCase(ss[0])) up = 0;
        else if (Character.isDigit(ss[0])) num = 0;

        // 优先队列，x%3小的先出队，为方便需要删除操作时先处理段的序列
        Queue<Integer> queue = new PriorityQueue<>((a, b) -> a % 3 - b % 3);

        for (int i = 1; i < len; i ++) {
            if (low == 1 && Character.isLowerCase(ss[i])) low = 0;
            else if (up == 1 && Character.isUpperCase(ss[i])) up = 0;
            else if (num == 1 && Character.isDigit(ss[i])) num = 0;

            // 对连续相同的序列计数，并存入优先队列
            if (ss[i] != ss[i - 1]) {
                if (cnt >= 3) queue.add(cnt);
                cnt = 1;
            } else {
                cnt ++;
            }
        }
        if (cnt >= 3) queue.add(cnt);
        
        int ans = 0, all = low + up + num;

        // 长度不足，则根据是否有重复序列进行替换、加添操作
        if (len < 6) return Math.max(6 - len, all);

        // 删除操作，从最短的连续序列开始处理
        while (!queue.isEmpty() && len > 20) {
            int cur = queue.remove();
            ans ++;
            len --;
            if (-- cur >= 3) queue.add(cur);
        }

        // 解决完重复序列后，字符串仍然过长
        if (len > 20) return ans + len - 20 + all;

        // 未处理完重复序列就已经达到长度要求，继续处理重复序列
        // 此时就只考虑替换操作就好了
        int n = 0;
        while (!queue.isEmpty()) {
            n += queue.remove() / 3;
        }

        return ans + Math.max(n, all);
    }

}
```

## 609 Find Duplicate File in System

### 原题

在系统中查找重复文件

给定一个目录信息列表，包括目录路径，以及该目录中的所有包含内容的文件，您需要找到文件系统中的所有重复文件组的路径。一组重复的文件至少包括二个具有完全相同内容的文件。

输入列表中的单个目录信息字符串的格式如下：

"root/d1/d2/.../dm f1.txt\(f1\_content\) f2.txt\(f2\_content\) ... fn.txt\(fn\_content\)"

这意味着有 n 个文件（f1.txt, f2.txt ... fn.txt 的内容分别是 f1\_content, f2\_content ... fn\_content）在目录 root/d1/d2/.../dm 下。注意：n&gt;=1 且 m&gt;=0。如果 m=0，则表示该目录是根目录。

该输出是重复文件路径组的列表。对于每个组，它包含具有相同内容的文件的所有文件路径。文件路径是具有下列格式的字符串：

"directory\_path/file\_name.txt"

Given a list of directory info including directory path, and all the files with contents in this directory, you need to find out all the groups of duplicate files in the file system in terms of their paths.

A group of duplicate files consists of at least **two** files that have exactly the same content.

A single directory info string in the **input** list has the following format:

`"root/d1/d2/.../dm f1.txt(f1_content) f2.txt(f2_content) ... fn.txt(fn_content)"`

It means there are **n** files \(`f1.txt`, `f2.txt` ... `fn.txt` with content `f1_content`, `f2_content` ... `fn_content`, respectively\) in directory `root/d1/d2/.../dm`. Note that n &gt;= 1 and m &gt;= 0. If m = 0, it means the directory is just the root directory.

The **output** is a list of group of duplicate file paths. For each group, it contains all the file paths of the files that have the same content. A file path is a string that has the following format:

`"directory_path/file_name.txt"`

**Example 1:**

```text
Input:
["root/a 1.txt(abcd) 2.txt(efgh)", "root/c 3.txt(abcd)", "root/c/d 4.txt(efgh)", "root 4.txt(efgh)"]
Output:  
[["root/a/2.txt","root/c/d/4.txt","root/4.txt"],["root/a/1.txt","root/c/3.txt"]]
```

**Note:**

1. No order is required for the final output.
2. You may assume the directory name, file name and file content only has letters and digits, and the length of file content is in the range of \[1,50\].
3. The number of files given is in the range of \[1,20000\].
4. You may assume no files or directories share the same name in the same directory.
5. You may assume each given directory info represents a unique directory. Directory path and file info are separated by a single blank space.

 **Follow-up beyond contest:**

1. Imagine you are given a real file system, how will you search files? DFS or BFS?
2. If the file content is very large \(GB level\), how will you modify your solution?
3. If you can only read the file by 1kb each time, how will you modify your solution?
4. What is the time complexity of your modified solution? What is the most time-consuming part and memory consuming part of it? How to optimize?
5. How to make sure the duplicated files you find are not false positive?

### 分析

哈希表 首先我们通过字符串操作获取目录路径、文件名和文件内容。我们使用哈希映射（HashMap）来寻找重复文件，哈希映射中的键（key）是文件内容，值（value）是存储路径和文件名的列表。

我们遍历每一个文件，并把它加入哈希映射中。在这之后，我们遍历哈希映射，如果一个键对应的值列表的长度大于 1，说明我们找到了重复文件，可以把这个列表加入到答案中。

### 代码

```java
public class Solution {
    public List<List<String>> findDuplicate(String[] paths) {
        HashMap<String, List<String>> map = new HashMap<>();
        for (String path : paths) {
            String[] values = path.split(" ");
            for (int i = 1; i < values.length; i++) {
                String[] name_cont = values[i].split("\\(");
                name_cont[1] = name_cont[1].replace(")", "");
                List<String> list = map.getOrDefault(name_cont[1], new ArrayList<String>());
                list.add(values[0] + "/" + name_cont[0]);
                map.put(name_cont[1], list);
            }
        }
        List<List<String>> res = new ArrayList<>();
        for (String key : map.keySet()) {
            if (map.get(key).size() > 1)
                res.add(map.get(key));
        }
        return res;
    }
}
```

## 1292 Maximum Side Length of a Square with Sum Less than or Equal to Threshold

### 原题

给你一个大小为 m x n 的矩阵 mat 和一个整数阈值 threshold。

请你返回元素总和小于或等于阈值的正方形区域的最大边长；如果没有这样的正方形区域，则返回 0 。

Given a `m x n` matrix `mat` and an integer `threshold`. Return the maximum side-length of a square with a sum less than or equal to `threshold` or return **0** if there is no such square.

**Example 1:**![](https://assets.leetcode.com/uploads/2019/12/05/e1.png)

```text
Input: mat = [[1,1,3,2,4,3,2],[1,1,3,2,4,3,2],[1,1,3,2,4,3,2]], threshold = 4
Output: 2
Explanation: The maximum side length of square with sum less than 4 is 2 as shown.
```

**Example 2:**

```text
Input: mat = [[2,2,2,2,2],[2,2,2,2,2],[2,2,2,2,2],[2,2,2,2,2],[2,2,2,2,2]], threshold = 1
Output: 0
```

**Example 3:**

```text
Input: mat = [[1,1,1,1],[1,0,0,0],[1,0,0,0],[1,0,0,0]], threshold = 6
Output: 3
```

**Example 4:**

```text
Input: mat = [[18,70],[61,1],[25,85],[14,40],[11,96],[97,96],[63,45]], threshold = 40184
Output: 2
```

**Constraints:**

* `1 <= m, n <= 300`
* `m == mat.length`
* `n == mat[i].length`
* `0 <= mat[i][j] <= 10000`
* `0 <= threshold <= 10^5`

### 分析

1）时间复杂度：O\(min\(M, N\) \* M \* N\)，其中 M 为矩阵长度，N 为矩阵宽度。

![](../.gitbook/assets/image%20%28159%29.png)

2）时间复杂度：O\(log\(min\(M, N\)\) \* M \* N\)O\(log\(min\(M,N\)\)∗M∗N\)，其中 M 为矩阵长度，N 为矩阵宽度。

![](../.gitbook/assets/image%20%28176%29.png)

### 代码

1）

```java
class Solution {
    public int maxSideLength(int[][] mat, int threshold) {
        int m = mat.length, n = mat[0].length;
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                dp[i][j] = mat[i - 1][j - 1] + dp[i - 1][j] + dp[i][j - 1] - dp[i - 1][j - 1];
            }
        }
        int ans = 0;
        for (int k = 1; k <= Math.min(m, n); k++) {
            for (int i = 1; i <= m; i++) {
                for (int j = 1; j <= n; j++) {
                    if (i - k < 0 || j - k < 0) {
                        continue;
                    }
                    int tmp = dp[i][j] - dp[i - k][j] - dp[i][j - k] + dp[i - k][j - k];
                    if (tmp <= threshold) {
                        ans = Math.max(ans, k);
                    }
                }
            }
        }
        return ans;
    }
}
```

2）

```java
class Solution {
    int m, n;
    int[][] dp;
    public int maxSideLength(int[][] mat, int threshold) {
        m = mat.length;
        n = mat[0].length;
        dp = new int[m + 1][n + 1];
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                dp[i][j] = mat[i - 1][j - 1] + dp[i - 1][j] + dp[i][j - 1] - dp[i - 1][j - 1];
            }
        }
        int l = 0, h = Math.min(m, n);
        while (l <= h) {
            int mid = l + (h - l) / 2;
            if (l == h || l + 1 == h) {
                break;
            }
            if (help(mid, threshold)) {
                l = mid;
            } else {
                h = mid - 1;
            }
        }
        if (help(h, threshold)) {
            return h;
        } else {
            return l;
        }
    }
    public boolean help(int k, int threshold) {
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (i - k < 0 || j - k < 0) {
                    continue;
                }
                if (dp[i][j] - dp[i - k][j] - dp[i][j - k] + dp[i - k][j - k] <= threshold) {
                    return true;
                }
            }
        }
        return false;
    }
}
```

## 968 Binary Tree Cameras

### 原题

监控二叉树

给定一个二叉树，我们在树的节点上安装摄像头。

节点上的每个摄影头都可以监视**其父对象、自身及其直接子对象。**

计算监控树的所有节点所需的最小摄像头数量。

Given a binary tree, we install cameras on the nodes of the tree. 

Each camera at a node can monitor **its parent, itself, and its immediate children**.

Calculate the minimum number of cameras needed to monitor all nodes of the tree.

**Example 1:**![](https://assets.leetcode.com/uploads/2018/12/29/bst_cameras_01.png)

```text
Input: [0,0,null,0,0]
Output: 1
Explanation: One camera is enough to monitor all nodes if placed as shown.
```

**Example 2:**![](https://assets.leetcode.com/uploads/2018/12/29/bst_cameras_02.png)

```text
Input: [0,0,null,0,null,0,null,null,0]
Output: 2
Explanation: At least two cameras are needed to monitor all nodes of the tree. The above image shows one of the valid configurations of camera placement.
```

  
**Note:**

1. The number of nodes in the given tree will be in the range `[1, 1000]`.
2. **Every** node has value 0.

### 分析

DFS TopDown，叶子节点会返回1。如果一个节点的左右节点有叶子节点就会返回2，并且这个节点需要放camera，否则那个叶子节点就观测不到了。如果一个节点的左右节点有camera了，那么这个节点就返回0，然后继续从所谓的空节点开始。 需要注意的就是，叶子节点的左右子节点是空节点，返回0，左右子节点有camera的节点也返回0。

### 代码

DFS

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 * int val;
 * TreeNode left;
 * TreeNode right;
 * TreeNode() {}
 * TreeNode(int val) { this.val = val; }
 * TreeNode(int val, TreeNode left, TreeNode right) {
 * this.val = val;
 * this.left = left;
 * this.right = right;
 * }
 * }
 */
class Solution {
    int result = 0;

    public int minCameraCover(TreeNode root) {
        if (dfs(root) == 1) {
            result++;
        }
        return result;
    }

    //0:可被观测但无监控，上一层节点为1
    //1：不可被观测到，上一层节点为2
    //2：有摄像机，上一层节点为0
    private int dfs(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int leftStatus = dfs(root.left), rightStatus = dfs(root.right);
        if (leftStatus == 1 || rightStatus == 1) {
            result++;
            return 2;
        } else if (leftStatus == 2 || rightStatus == 2) {
            return 0;
        } else {
            return 1;
        }
    }
}
```

* 1，当前节点有相机
* 2，当前节点不需要相机（子节点有相机把它给覆盖了）
* 3，当前节点没有相机并且也没有被子节点给覆盖（那么他只能等他的父节点把它给覆盖了）

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 * int val;
 * TreeNode left;
 * TreeNode right;
 * TreeNode() {}
 * TreeNode(int val) { this.val = val; }
 * TreeNode(int val, TreeNode left, TreeNode right) {
 * this.val = val;
 * this.left = left;
 * this.right = right;
 * }
 * }
 */
class Solution {
    //NO_CAMERA表示的是子节点没有相机，当前节点也没放相机
    private final int NO_CAMERA = 0;
    //HAS_CAMERA表示当前节点有一个相机
    private final int HAS_CAMERA = 1;
    //NO_NEEDED表示当前节点没有相机，但他的子节点有一个相机，把它给
//覆盖了，所以它不需要了。或者他是一个空的节点也是不需要相机的
    private final int NO_NEEDED = 2;

    //全局的，统计有多少相机
    int res = 0;

    public int minCameraCover(TreeNode root) {
        //边界条件判断
        if (root == null)
            return 0;
        //如果最后返回的是NO_CAMERA，表示root节点的子节点也没有相机，
        //所以root节点要添加一个相机
        if (dfs(root) == NO_CAMERA)
            res++;
        //返回结果
        return res;
    }

    public int dfs(TreeNode root) {
        //如果是空的，就不需要相机了
        if (root == null)
            return NO_NEEDED;
        int left = dfs(root.left), right = dfs(root.right);
        //如果左右子节点有一个是NO_CAMERA，表示的是子节点既没相机，也没相机覆盖它，
        //所以当前节点需要有一个相机
        if (left == NO_CAMERA || right == NO_CAMERA) {
            //在当前节点放一个相机，统计相机的个数
            res++;
            return HAS_CAMERA;
        }
        //如果左右子节点只要有一个有相机，那么当前节点就不需要相机了，否则返回一个没有相机的标记
        return left == HAS_CAMERA || right == HAS_CAMERA ? NO_NEEDED : NO_CAMERA;
    }
}
```

## 347 Top K Frequent Elements

### 原题

 给定一个非空的整数数组，返回其中出现频率前 _**k**_ 高的元素。

Given a non-empty array of integers, return the **k** most frequent elements.

**Example 1:**

```text
Input: nums = [1,1,1,2,2,3], k = 2
Output: [1,2]
```

**Example 2:**

```text
Input: nums = [1], k = 1
Output: [1]
```

**Note:**

* You may assume k is always valid, 1 ≤ k ≤ number of unique elements.
* Your algorithm's time complexity **must be** better than O\(n log n\), where n is the array's size.
* It's guaranteed that the answer is unique, in other words the set of the top k frequent elements is unique.
* You can return the answer in any order.

### 分析

排序就是暴力法

1）堆排序，时间复杂度：O\(Nlogk\)，堆的大小至多为 k；空间复杂度：O\(N\)O\(N\)。哈希表的大小为 O\(N\)，而堆的大小为 O\(k\)，共计为 O\(N\)。

首先遍历整个数组，并使用哈希表记录每个数字出现的次数，并形成一个「出现次数数组」。找出原数组的前 kk 个高频元素，就相当于找出「出现次数数组」的前 kk 大的值。

最简单的做法是给「出现次数数组」排序。但由于可能有 O\(N\)O\(N\) 个不同的出现次数（其中 NN 为原数组长度），故总的算法复杂度会达到 O\(N\log N\)O\(NlogN\)，不满足题目的要求。

在这里，我们可以利用堆的思想：建立一个小顶堆，然后遍历「出现次数数组」：

如果堆的元素个数小于 kk，就可以直接插入堆中。 如果堆的元素个数等于 kk，则检查堆顶与当前出现次数的大小。如果堆顶更大，说明至少有 kk 个数字的出现次数比当前值大，故舍弃当前值；否则，就弹出堆顶，并将当前值插入堆中。 遍历完成后，堆中的元素就代表了「出现次数数组」中前 kk 大的值。

2）基于快速排序，时间复杂度：时间复杂度：最坏O\(N^2\)，空间复杂度：O\(N\)O\(N\)

3）桶排序也可以， **时间复杂度**：O\(n\)， **空间复杂度**O\(n\)

### 代码

1

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> occurrences = new HashMap<Integer, Integer>();
        for (int num : nums) {
            occurrences.put(num, occurrences.getOrDefault(num, 0) + 1);
        }

        // int[] 的第一个元素代表数组的值，第二个元素代表了该值出现的次数
        PriorityQueue<int[]> queue = new PriorityQueue<int[]>(new Comparator<int[]>() {
            public int compare(int[] m, int[] n) {
                return m[1] - n[1];
            }
        });
        for (Map.Entry<Integer, Integer> entry : occurrences.entrySet()) {
            int num = entry.getKey(), count = entry.getValue();
            if (queue.size() == k) {
                if (queue.peek()[1] < count) {
                    queue.poll();
                    queue.offer(new int[]{num, count});
                }
            } else {
                queue.offer(new int[]{num, count});
            }
        }
        int[] ret = new int[k];
        for (int i = 0; i < k; ++i) {
            ret[i] = queue.poll()[0];
        }
        return ret;
    }
}基于快速排序
```

2

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> occurrences = new HashMap<Integer, Integer>();
        for (int num : nums) {
            occurrences.put(num, occurrences.getOrDefault(num, 0) + 1);
        }

        List<int[]> values = new ArrayList<int[]>();
        for (Map.Entry<Integer, Integer> entry : occurrences.entrySet()) {
            int num = entry.getKey(), count = entry.getValue();
            values.add(new int[]{num, count});
        }
        int[] ret = new int[k];
        qsort(values, 0, values.size() - 1, ret, 0, k);
        return ret;
    }

    public void qsort(List<int[]> values, int start, int end, int[] ret, int retIndex, int k) {
        int picked = (int) (Math.random() * (end - start + 1)) + start;
        Collections.swap(values, picked, start);
        
        int pivot = values.get(start)[1];
        int index = start;
        for (int i = start + 1; i <= end; i++) {
            if (values.get(i)[1] >= pivot) {
                Collections.swap(values, index + 1, i);
                index++;
            }
        }
        Collections.swap(values, start, index);

        if (k <= index - start) {
            qsort(values, start, index - 1, ret, retIndex, k);
        } else {
            for (int i = start; i <= index; i++) {
                ret[retIndex++] = values.get(i)[0];
            }
            if (k > index - start + 1) {
                qsort(values, index + 1, end, ret, retIndex, k - (index - start + 1));
            }
        }
    }
}
```

3 桶排序，注意方法的singature不一样

```java
//基于桶排序求解「前 K 个高频元素」
class Solution {
    public List<Integer> topKFrequent(int[] nums, int k) {
        List<Integer> res = new ArrayList();
        // 使用字典，统计每个元素出现的次数，元素为键，元素出现的次数为值
        HashMap<Integer,Integer> map = new HashMap();
        for(int num : nums){
            if (map.containsKey(num)) {
               map.put(num, map.get(num) + 1);
             } else {
                map.put(num, 1);
             }
        }
        
        //桶排序
        //将频率作为数组下标，对于出现频率不同的数字集合，存入对应的数组下标
        List<Integer>[] list = new List[nums.length+1];
        for(int key : map.keySet()){
            // 获取出现的次数作为下标
            int i = map.get(key);
            if(list[i] == null){
               list[i] = new ArrayList();
            } 
            list[i].add(key);
        }
        
        // 倒序遍历数组获取出现顺序从大到小的排列
        for(int i = list.length - 1;i >= 0 && res.size() < k;i--){
            if(list[i] == null) continue;
            res.addAll(list[i]);
        }
        return res;
    }
}
```

## 1146 Snapshot Array

### 原题

实现支持下列接口的「快照数组」- SnapshotArray：

* SnapshotArray\(int length\) - 初始化一个与指定长度相等的 类数组 的数据结构。初始时，每个元素都等于 0。 
* void set\(index, val\) - 会将指定索引 index 处的元素设置为 val。 
* int snap\(\) - 获取该数组的快照，并返回快照的编号 snap\_id（快照号是调用 snap\(\) 的总次数减去 1）。 
* int get\(index, snap\_id\) - 根据指定的 snap\_id 选择快照，并返回该快照指定索引 index 的值。

Implement a SnapshotArray that supports the following interface:

* `SnapshotArray(int length)` initializes an array-like data structure with the given length.  **Initially, each element equals 0**.
* `void set(index, val)` sets the element at the given `index` to be equal to `val`.
* `int snap()` takes a snapshot of the array and returns the `snap_id`: the total number of times we called `snap()` minus `1`.
* `int get(index, snap_id)` returns the value at the given `index`, at the time we took the snapshot with the given `snap_id`

**Example 1:**

```text
Input: ["SnapshotArray","set","snap","set","get"]
[[3],[0,5],[],[0,6],[0,0]]
Output: [null,null,0,null,5]
Explanation: 
SnapshotArray snapshotArr = new SnapshotArray(3); // set the length to be 3
snapshotArr.set(0,5);  // Set array[0] = 5
snapshotArr.snap();  // Take a snapshot, return snap_id = 0
snapshotArr.set(0,6);
snapshotArr.get(0,0);  // Get the value of array[0] with snap_id = 0, return 5
```

**Constraints:**

* `1 <= length <= 50000`
* At most `50000` calls will be made to `set`, `snap`, and `get`.
* `0 <= index < length`
* `0 <= snap_id <` \(the total number of times we call `snap()`\)
* `0 <= val <= 10^9`

### 分析

1）利用treemap记录snapid对应的list中保存此snapid的索引

2）List&lt;Map&lt;Integer,Integer&gt;&gt;，题意是要构建一个带有不同版本信息的数组，一种方法是每个版本都保存一个数组，但是这样显然产生了太多的冗余 只需要记录每个版本之间有区别的值即可

### 代码

```java
class SnapshotArray {

    Node[] arr;
    int snapTimes;

    public SnapshotArray(int length) {
        this.arr = new Node[length];
        this.snapTimes = 0;
        for(int i = 0; i < length; ++i) 
            arr[i] = new Node(0);
    }
    
    public void set(int index, int val) {
        Node cur = arr[index];
        if(cur.snapId == snapTimes) {
            cur.val = val;
            cur.vals.set(cur.idx, val);
        } else {
            cur.val = val;
            cur.vals.add(val);
            cur.idx++;
            cur.snapId = snapTimes;
            cur.map.put(snapTimes, cur.idx);
        }
        return;
    }
    
    public int snap() {
        snapTimes++;
        return snapTimes - 1;
    }
    
    public int get(int index, int snap_id) {
        Node cur = arr[index];
        if(cur.map.containsKey(snap_id)) {
            int idx = cur.map.get(snap_id);
            return cur.vals.get(idx);
        } else {
            int key = cur.map.lowerKey(snap_id);
            return cur.vals.get(cur.map.get(key));
        }
    }
}

class Node {
    int val;
    int idx;
    int snapId;
    List<Integer> vals;
    TreeMap<Integer, Integer> map;

    Node(int v) {
        this.val = v;
        this.vals = new ArrayList();
        this.idx = 0;
        this.snapId = 0;
        this.map = new TreeMap();
        vals.add(v);
        
        map.put(0, idx);
    }
}

/**
 * Your SnapshotArray object will be instantiated and called as such:
 * SnapshotArray obj = new SnapshotArray(length);
 * obj.set(index,val);
 * int param_2 = obj.snap();
 * int param_3 = obj.get(index,snap_id);
 */

```

```java
class SnapshotArray {
    
    //list.get(index)应为数组下标为index处的值
    //每一个Map记录的是当前下标位置的值的变动，<版本号，新值>
    private List<Map<Integer,Integer>> list;
    private int snapId;

    public SnapshotArray(int length) {
        list = new ArrayList<>();
        for(int i = 0; i < length; i++){
            list.add(new HashMap<Integer,Integer>());
        }
        snapId = 0;
    }
    
    public void set(int index, int val) {
        //在index处放入此次版本更新的值
        list.get(index).put(this.snapId,val);
    }
    
    public int snap() {
        return snapId++;
    }
    
    public int get(int index, int snap_id) {
        //寻找snap_id版本前，最后一次更新的值
        for(int i = snap_id; i >= 0; i--) {
            if (list.get(index).containsKey(snap_id)) {
                return list.get(index).get(snap_id);
            }
            --snap_id;
        }
        return 0;
    }
}

/**
 * Your SnapshotArray object will be instantiated and called as such:
 * SnapshotArray obj = new SnapshotArray(length);
 * obj.set(index,val);
 * int param_2 = obj.snap();
 * int param_3 = obj.get(index,snap_id);
 */
```

## 1223 Dice Roll Simulation

### 原题

有一个骰子模拟器会每次投掷的时候生成一个 1 到 6 的随机数。

不过我们在使用它时有个约束，就是使得投掷骰子时，连续 掷出数字 i 的次数不能超过 rollMax\[i\]（i 从 1 开始编号）。

现在，给你一个整数数组 rollMax 和一个整数 n，请你来计算掷 n 次骰子可得到的不同点数序列的数量。

假如两个序列中至少存在一个元素不同，就认为这两个序列是不同的。由于答案可能很大，所以请返回 模 10^9 + 7 之后的结果。

A die simulator generates a random number from 1 to 6 for each roll. You introduced a constraint to the generator such that it cannot roll the number `i` more than `rollMax[i]` \(1-indexed\) **consecutive** times. 

Given an array of integers `rollMax` and an integer `n`, return the number of distinct sequences that can be obtained with exact `n` rolls.

Two sequences are considered different if at least one element differs from each other. Since the answer may be too large, return it modulo `10^9 + 7`.

**Example 1:**

```text
Input: n = 2, rollMax = [1,1,2,2,2,3]
Output: 34
Explanation: There will be 2 rolls of die, if there are no constraints on the die, there are 6 * 6 = 36 possible combinations. In this case, looking at rollMax array, the numbers 1 and 2 appear at most once consecutively, therefore sequences (1,1) and (2,2) cannot occur, so the final answer is 36-2 = 34.
```

**Example 2:**

```text
Input: n = 2, rollMax = [1,1,1,1,1,1]
Output: 30
```

**Example 3:**

```text
Input: n = 3, rollMax = [1,1,1,2,2,3]
Output: 181
```

**Constraints:**

* `1 <= n <= 5000`
* `rollMax.length == 6`
* `1 <= rollMax[i] <= 15`

### 分析

DP

### 代码

![](../.gitbook/assets/image%20%28167%29.png)

```java
class Solution {
    public int dieSimulator(int n) {
        int[][] dp = new int[n][6];
        // init, roll once, only one sequence end up by every number
        for (int i = 0; i < 6; i++) dp[0][i] = 1;
        // start dp
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < 6; j++) {
                dp[i][j] = Arrays.stream(dp[i-1]).sum();
            }
        }
        return Arrays.stream(dp[n-1]).sum();
    }
}
```

![](../.gitbook/assets/image%20%28143%29.png)

```java
class Solution {
    
    private static int MOD = 1000000007;
    
    public int dieSimulator(int n, int[] rollMax) {
        int[][] dp = new int[n][6];
        for (int i = 0; i < 6; i++) dp[0][i] = 1;
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < 6; j++) {
                dp[i][j] = Arrays.stream(dp[i-1]).reduce(0, (a, b) -> (a + b) % MOD);
                // 参见算法部分第2条
                if (i == rollMax[j]) dp[i][j]--;
                else if (i > rollMax[j]) {
                    for (int k = 0; k < 6; k++) {
                        if (j != k) dp[i][j] = (dp[i][j] - dp[i - rollMax[j] - 1][k] + MOD) % MOD;
                    }
                }
            }
        }
        return Arrays.stream(dp[n-1]).reduce(0, (a, b) -> (a + b) % MOD);
    }
}


```

## 1088 Confusing Number II

### 原题

本题我们会将数字旋转 180° 来生成一个新的数字。

比如 0、1、6、8、9 旋转 180° 以后，我们得到的新数字分别为 0、1、9、8、6。

2、3、4、5、7 旋转 180° 后，是 无法 得到任何数字的。

易混淆数（Confusing Number）指的是一个数字在整体旋转 180° 以后，能够得到一个和原来 不同 的数，且新数字的每一位都应该是有效的。（请注意，旋转后得到的新数字可能大于原数字）

给出正整数 N，请你返回 1 到 N 之间易混淆数字的数量。

We can rotate digits by 180 degrees to form new digits. When 0, 1, 6, 8, 9 are rotated 180 degrees, they become 0, 1, 9, 8, 6 respectively. When 2, 3, 4, 5 and 7 are rotated 180 degrees, they become invalid.

A _confusing number_ is a number that when rotated 180 degrees becomes a **different** number with each digit valid.\(Note that the rotated number can be greater than the original number.\)

Given a positive integer `N`, return the number of confusing numbers between `1` and `N` inclusive.

**Example 1:**

```text
Input: 20
Output: 6
Explanation: 
The confusing numbers are [6,9,10,16,18,19].
6 converts to 9.
9 converts to 6.
10 converts to 01 which is just 1.
16 converts to 91.
18 converts to 81.
19 converts to 61.
```

**Example 2:**

```text
Input: 100
Output: 19
Explanation: 
The confusing numbers are [6,9,10,16,18,19,60,61,66,68,80,81,86,89,90,91,98,99,100].
```

**Note:**

1. `1 <= N <= 10^9`

### 分析

DFS，一个问题，int值不要越界

### 代码

```java
class Solution {
    int[] a = new int[]{0, 1, 6, 8, 9};

    int[] change = new int[]{0, 1, 2, 3, 4, 5, 9, 7, 8, 6};
    int res = 0;

    public int confusingNumberII(int N) {
        for (int i = 1; i < 5 && a[i] <= N; i++) {
            dfs(N, a[i], 1, depth(N));
        }
        return res;
    }

    private void dfs(int N, int cur, int dep, int max) {
        if (max < dep) {
            return;
        }
        if (cur > N) {
            return;
        }
        if (check(cur)) {
            res++;
        }
        for (int i = 0; i < 5; i++) {
            if (cur > Integer.MAX_VALUE / 10) {
                continue;
            }
            dfs(N, cur * 10 + a[i], dep + 1, max);
        }
    }

    private int depth(int x) {
        int res = 0;
        while (x != 0) {
            x /= 10;
            res++;
        }
        return res;
    }

    private boolean check(int x) {
        if (x == 0 || x == 1 || x == 8) {
            return false;
        }
        int pre = x;
        int y = 0;
        while (x != 0) {
            y = y * 10 + change[x % 10];
            x /= 10;
        }
        return pre != y;
    }
}
```

## 351 Android Unlock Patterns

### 原题

我们都知道安卓有个手势解锁的界面，是一个 3 x 3 的点所绘制出来的网格。

给你两个整数，分别为 ​​m 和 n，其中 1 ≤ m ≤ n ≤ 9，那么请你统计一下有多少种解锁手势，是至少需要经过 m 个点，但是最多经过不超过 n 个点的。

先来了解下什么是一个有效的安卓解锁手势:

1. 每一个解锁手势必须至少经过 m 个点、最多经过 n 个点。 
2. 解锁手势里不能设置经过重复的点。 
3. 假如手势中有两个点是顺序经过的，那么这两个点的手势轨迹之间是绝对不能跨过任何未被经过的点。 
4. 经过点的顺序不同则表示为不同的解锁手势。

Given an Android **3x3** key lock screen and two integers **m** and **n**, where 1 ≤ m ≤ n ≤ 9, count the total number of unlock patterns of the Android lock screen, which consist of minimum of **m** keys and maximum **n** keys.

**Rules for a valid pattern:**

1. Each pattern must connect at least **m** keys and at most **n** keys.
2. All the keys must be distinct.
3. If the line connecting two consecutive keys in the pattern passes through any other keys, the other keys must have previously selected in the pattern. No jumps through non selected key is allowed.
4. The order of keys used matters.

```text

```

**Explanation:**

```text
| 1 | 2 | 3 |
| 4 | 5 | 6 |
| 7 | 8 | 9 |
```

**Invalid move:** `4 - 1 - 3 - 6`  
Line 1 - 3 passes through key 2 which had not been selected in the pattern.

**Invalid move:** `4 - 1 - 9 - 2`  
Line 1 - 9 passes through key 5 which had not been selected in the pattern.

**Valid move:** `2 - 4 - 1 - 3 - 6`  
Line 1 - 3 is valid because it passes through key 2, which had been selected in the pattern

**Valid move:** `6 - 5 - 4 - 1 - 9 - 2`  
Line 1 - 9 is valid because it passes through key 5, which had been selected in the pattern.

**Example:**

```text
Input: m = 1, n = 1
Output: 9
```

### 分析

DFS回溯

![](../.gitbook/assets/image%20%28144%29.png)

时间复杂度：O\(n!\)，空间复杂度：O\(n\)

### 代码

```java
public class Solution {

    private boolean used[] = new boolean[9];

    public int numberOfPatterns(int m, int n) {
        int res = 0;
        for (int len = m; len <= n; len++) {
            res += calcPatterns(-1, len);
            for (int i = 0; i < 9; i++) {
                used[i] = false;
            }
        }
        return res;
    }

    private boolean isValid(int index, int last) {
        if (used[index])
            return false;
        // first digit of the pattern    
        if (last == -1)
            return true;
        // knight moves or adjacent cells (in a row or in a column)	       
        if ((index + last) % 2 == 1)
            return true;
        // indexes are at both end of the diagonals for example 0,0, and 8,8          
        int mid = (index + last) / 2;
        if (mid == 4)
            return used[mid];
        // adjacent cells on diagonal  - for example 0,0 and 1,0 or 2,0 and //1,1
        if ((index % 3 != last % 3) && (index / 3 != last / 3)) {
            return true;
        }
        // all other cells which are not adjacent
        return used[mid];
    }

    private int calcPatterns(int last, int len) {
        if (len == 0)
            return 1;
        int sum = 0;
        for (int i = 0; i < 9; i++) {
            if (isValid(i, last)) {
                used[i] = true;
                sum += calcPatterns(i, len - 1);
                used[i] = false;
            }
        }
        return sum;
    }
}
```

## 1218 Longest Arithmetic Subsequence of Given Difference

### 原题

最长定差子序列，给你一个整数数组 arr 和一个整数 difference，请你找出 arr 中所有相邻元素之间的差等于给定 difference 的等差子序列，并返回其中最长的等差子序列的长度。

Given an integer array `arr` and an integer `difference`, return the length of the longest subsequence in `arr` which is an arithmetic sequence such that the difference between adjacent elements in the subsequence equals `difference`.

**Example 1:**

```text
Input: arr = [1,2,3,4], difference = 1
Output: 4
Explanation: The longest arithmetic subsequence is [1,2,3,4].
```

**Example 2:**

```text
Input: arr = [1,3,5,7], difference = 1
Output: 1
Explanation: The longest arithmetic subsequence is any single element.
```

**Example 3:**

```text
Input: arr = [1,5,7,8,5,3,4,2,1], difference = -2
Output: 4
Explanation: The longest arithmetic subsequence is [7,5,3,1].
```

**Constraints:**

* `1 <= arr.length <= 10^5`
* `-10^4 <= arr[i], difference <= 10^4`

### 分析

![](../.gitbook/assets/image%20%28154%29.png)

### 代码

```java
class Solution {
    public int longestSubsequence(int[] arr, int difference) {
        int ans = 1;
        Map<Integer, Integer> map = new HashMap<>();
        for (int i: arr) {
            int temp = map.getOrDefault(i - difference, 0) + 1;
            map.put(i, temp);
            ans = Math.max(ans, temp);
        }
        return ans;
    }
}
```

## 1125 Smallest Sufficient Team

### 原题

最小的必要团队，作为项目经理，你规划了一份需求的技能清单 req\_skills，并打算从备选人员名单 people 中选出些人组成一个「必要团队」（ 编号为 i 的备选人员 people\[i\] 含有一份该备选人员掌握的技能列表）。

所谓「必要团队」，就是在这个团队中，对于所需求的技能列表 req\_skills 中列出的每项技能，团队中至少有一名成员已经掌握。

我们可以用每个人的编号来表示团队中的成员：例如，团队 team = \[0, 1, 3\] 表示掌握技能分别为 people\[0\]，people\[1\]，和 people\[3\] 的备选人员。

请你返回 任一 规模最小的必要团队，团队成员用人员编号表示。你可以按任意顺序返回答案，本题保证答案存在。

In a project, you have a list of required skills `req_skills`, and a list of `people`.  The i-th person `people[i]` contains a list of skills that person has.

Consider a _sufficient team_: a set of people such that for every required skill in `req_skills`, there is at least one person in the team who has that skill.  We can represent these teams by the index of each person: for example, `team = [0, 1, 3]` represents the people with skills `people[0]`, `people[1]`, and `people[3]`.

Return **any** sufficient team of the smallest possible size, represented by the index of each person.

You may return the answer in any order.  It is guaranteed an answer exists.

**Example 1:**

```text
Input: req_skills = ["java","nodejs","reactjs"], people = [["java"],["nodejs"],["nodejs","reactjs"]]
Output: [0,2]
```

**Example 2:**

```text
Input: req_skills = ["algorithms","math","java","reactjs","csharp","aws"], people = [["algorithms","math","java"],["algorithms","math","reactjs"],["java","csharp","aws"],["reactjs","csharp"],["csharp","math"],["aws","java"]]
Output: [1,2]
```

**Constraints:**

* `1 <= req_skills.length <= 16`
* `1 <= people.length <= 60`
* `1 <= people[i].length, req_skills[i].length, people[i][j].length <= 16`
* Elements of `req_skills` and `people[i]` are \(respectively\) distinct.
* `req_skills[i][j], people[i][j][k]` are lowercase English letters.
* Every skill in `people[i]` is a skill in `req_skills`.
* It is guaranteed a sufficient team exists.

### 分析

DFS，回溯 + 剪枝

看到这个问题的时候，第一想法就是回溯，奈何一直超时，然后一路调试优化，从超时到2000+ms再到18ms最后到5ms，最终优化的逻辑如下 

1.由于技能req\_skills最多16个，则可以用二进制1，10，100...来表示从第一个到最后一个技能，由此可以将员工技能转换为对应第数字。 

2.若存在两个员工技能重复可以去重，或者一员工技能是另外员工技能真子集，则该员工必不在最优解中，去除该员工，从而提高回溯效率 

3.如果该员工集合中存在无交集员工（和其他所有员工技能不重复），那么该员工一定在最终解中，则可以得出无交集员工集合作为解的子集。 

4.判断无交集员工技能是否是解，若是，则肯定是最优解，否则说明还缺少其他员工，则可以根据该集合员工数量+1来作为最小回溯深度来进行回溯，从而降低回溯深度。 

5.对员工技能数peopleSkillNums进行排序，为了加快查找速度（主要为了对员工技能逐个匹配，例如匹配下一个技能员工时，只要已匹配技能中没有包含该技能，则添加） 

6.从最小回溯深度开始回溯，对技能数进行或运算，若有解，则肯定是最优解，直接结束，若无解，则回溯深度+1，继续回溯

### 代码

```java
class Solution {
    //成员技能对应二进制数字都数组下标
    private int[] peopleSubscript;

    //当前递归深度
    private int currentDepth = 0;

    public int[] smallestSufficientTeam(String[] req_skills, List<List<String>> people) {
        int[] result = null;
        int skillLen = req_skills.length, mustSkill = (1 << skillLen) - 1, peopleSize = people.size();
        //技能对应二进制，用1，10，100来表示
        Map<String, Integer> skillMap = new HashMap<String, Integer>(skillLen << 1);
        for (int i = 0; i < skillLen; i++) {
            skillMap.put(req_skills[i], 1 << i);
        }
        //员工技能对应二进制数
        int[] peopleSkillNums = new int[peopleSize];
        peopleSubscript = new int[1 << skillLen];
        for (int i = 0; i < people.size(); i++) {
            int skillNum = 0;
            List<String> skills = people.get(i);
            for (String skill : skills) {
                skillNum += skillMap.get(skill);
            }
            peopleSkillNums[i] = skillNum;
            peopleSubscript[skillNum] = i;
        }
        //技能或运算结果
        int comSkills = 0;
        //和其他所有员工没交集对应员工数量
        int aloneCount = 0;
        //和其他所有员工没交集对应员工数组下标
        int[] noIntersectionArr = new int[skillLen];
        for (int i = 0; i < peopleSize; i++) {
            if (peopleSkillNums[i] == 0) {
                continue;
            }
            //是否无交集
            boolean isNoIntersection = true;
            for (int j = 0; j < peopleSize; j++) {
                if (peopleSkillNums[j] == 0 || i == j) {
                    continue;
                }
                //重复的，去重
                if (peopleSkillNums[i] == peopleSkillNums[j]) {
                    peopleSkillNums[j] = 0;
                    continue;
                }
                //若一个员工技能是另外一个员工子集，则必定不在最优解中，去除
                if ((peopleSkillNums[i] | peopleSkillNums[j]) == peopleSkillNums[j]) {
                    peopleSkillNums[i] = 0;
                    isNoIntersection = false;
                    break;
                } else if ((peopleSkillNums[i] | peopleSkillNums[j]) == peopleSkillNums[i]) {
                    peopleSkillNums[j] = 0;
                    continue;
                }
                if ((peopleSkillNums[i] & peopleSkillNums[j]) != 0) {
                    isNoIntersection = false;
                    break;
                }
            }
            //无交集员工提前保存，方便之后回溯（降低后续回溯深度）
            if (isNoIntersection) {
                comSkills |= peopleSkillNums[i];
                noIntersectionArr[aloneCount] = peopleSubscript[peopleSkillNums[i]];
                peopleSkillNums[i] = 0;
                aloneCount++;
            }
        }
        //员工技能数字排序
        Arrays.sort(peopleSkillNums);
        //最小回溯深度，由小到大，则第一个得到结果就为最优解
        int minDepth = aloneCount;
        //若无交集员工技能组成等于必须技能，则输出结果，否则开始回溯深度+1
        if (comSkills == mustSkill) {
            result = new int[aloneCount];
            System.arraycopy(noIntersectionArr, 0, result, 0, aloneCount);
            return result;
        } else {
            minDepth++;
        }
        //从minDepth回溯深度开始回溯，noIntersectionArr肯定在该结果中，回溯深度从无交集员工数量开始
        for (int i = minDepth; i < skillLen; i++) {
            currentDepth = i;
            result = new int[i];
            System.arraycopy(noIntersectionArr, 0, result, 0, aloneCount);
            if (addNextPeople(mustSkill, comSkills, result, peopleSkillNums, aloneCount)) {
                break;
            }
        }
        return result;
    }

    private boolean addNextPeople(int mustSkill, int comSkills, int[] result, int[] peopleSkillNums, int count) {
        //判断是否为解
        if (mustSkill == comSkills) {
            return true;
        }
        //大于回溯深度，则不存在
        if (count >= currentDepth) {
            return false;
        }
        for (int i = peopleSkillNums.length - 1; i >= 0; i--) {
            int skillNum = peopleSkillNums[i];
            //由于排序，则技能为0则后续都为0，直接结束
            if (skillNum == 0) {
                break;
            }
            //组合技能已包含该技能，则跳过
            if ((comSkills | skillNum) == comSkills) {
                continue;
            }
            result[count] = peopleSubscript[peopleSkillNums[i]];
            peopleSkillNums[i] = 0;
            if (addNextPeople(mustSkill, comSkills | skillNum, result, peopleSkillNums, count + 1)) {
                return true;
            }
            peopleSkillNums[i] = skillNum;
        }
        return false;
    }
}
```

## 1466 Reorder Routes to Make All Paths Lead to the City Zero

### 原题

n 座城市，从 0 到 n-1 编号，其间共有 n-1 条路线。因此，要想在两座不同城市之间旅行只有唯一一条路线可供选择（路线网形成一颗树）。去年，交通运输部决定重新规划路线，以改变交通拥堵的状况。

路线用 connections 表示，其中 connections\[i\] = \[a, b\] 表示从城市 a 到 b 的一条有向路线。

今年，城市 0 将会举办一场大型比赛，很多游客都想前往城市 0 。

请你帮助重新规划路线方向，使每个城市都可以访问城市 0 。返回需要变更方向的最小路线数。

题目数据 保证 每个城市在重新规划路线方向后都能到达城市 0 。

There are `n` cities numbered from `0` to `n-1` and `n-1` roads such that there is only one way to travel between two different cities \(this network form a tree\). Last year, The ministry of transport decided to orient the roads in one direction because they are too narrow.

Roads are represented by `connections` where `connections[i] = [a, b]` represents a road from city `a` to `b`.

This year, there will be a big event in the capital \(city 0\), and many people want to travel to this city.

Your task consists of reorienting some roads such that each city can visit the city 0. Return the **minimum** number of edges changed.

It's **guaranteed** that each city can reach the city 0 after reorder.

**Example 1:**

![](https://assets.leetcode.com/uploads/2020/05/13/sample_1_1819.png)

```text
Input: n = 6, connections = [[0,1],[1,3],[2,3],[4,0],[4,5]]
Output: 3
Explanation: Change the direction of edges show in red such that each node can reach the node 0 (capital).
```

**Example 2:**

![](https://assets.leetcode.com/uploads/2020/05/13/sample_2_1819.png)

```text
Input: n = 5, connections = [[1,0],[1,2],[3,2],[3,4]]
Output: 2
Explanation: Change the direction of edges show in red such that each node can reach the node 0 (capital).
```

**Example 3:**

```text
Input: n = 3, connections = [[1,0],[2,0]]
Output: 0
```

**Constraints:**

* `2 <= n <= 5 * 10^4`
* `connections.length == n-1`
* `connections[i].length == 2`
* `0 <= connections[i][0], connections[i][1] <= n-1`
* `connections[i][0] != connections[i][1]`

### 分析

BFS

1. 同课程安排这个题，我们也是用HashMap的临接表形式存储图，也同样是用BFS来遍历。
2. 使用BFS的话，这道问题的关键就变成了保存一个无向图，而用其他方式来保存方向以计算。
3. 所以我们构建HashMap的时候，就一下子保存两个方向，因为我们是“忽略了”方向的，所以在邻接表中需要统计两个方向。（如果感觉绕的话……不妨看一下无向图的邻接表方法） 但是我们又不能真的忽略方向，因为这样BFS就没法计算需要变更的线路，所以我对反方向取了个负数。最后即可得出一个邻接表
4. 此时我们再进行bfs，该有的队列，访问数组依然都有，因为map中保存了可达路径，所以只要有关联，不管方向，都可以被遍历到。重点在于如果发现正数，我们就要调换方向（也就是让ans++）。

补邻接表： 可以看到无向图其实就是一种特殊的有向图，只不过相连的两个节点中有两个箭头指向 那么在本题中，如果考虑方向，就如左上及左下图所示。不考虑方向，就需要如右上及右下所示，能加的箭头都加上。

![](../.gitbook/assets/image%20%28142%29.png)

### 代码

```java
class Solution {
    public int minReorder(int n, int[][] connections) {
        //遍历整个数组,构建一个可达图，这里的可达的定义为联通即可，不在乎方向（但是这里我用负数代表了反方向，方便后续统计）
        HashMap<Integer, HashSet<Integer>> map = new HashMap<>();
        for (int[] val : connections) {
            HashSet<Integer> set = map.containsKey(val[0]) ? map.get(val[0]) : new HashSet<>();
            set.add(val[1]);
            map.put(val[0], set);
            HashSet<Integer> setReverse = map.containsKey(val[1]) ? map.get(val[1]) : new HashSet<>();
            setReverse.add(val[0] * -1);
            map.put(val[1], setReverse);
        }
        return mybfs(map, n);
    }

    private int mybfs(HashMap<Integer, HashSet<Integer>> map, int n) {
        boolean[] tBool = new boolean[n];
        LinkedList<Integer> queue = new LinkedList<>();
        int ans = 0;
        queue.offerLast(0);
        tBool[0] = true;
        while (!queue.isEmpty()) {
            for (Integer i : map.get(queue.pollFirst())) {
                //访问之后禁止再访问
                if (tBool[Math.abs(i)]) continue;
                //前面说到，我们用负数代表了反方向，这里就用作统计了，如果是正数，就说明方向需要调整，建议仔细品味下
                if (i > 0) ans++;
                tBool[Math.abs(i)] = true;
                queue.offerLast(Math.abs(i));
            }
        }
        return ans;
    }
}
```

## 855 Exam Room

### 原题

在考场里，一排有 N 个座位，分别编号为 0, 1, 2, ..., N-1 。

当学生进入考场后，他必须坐在能够使他与离他最近的人之间的距离达到最大化的座位上。如果有多个这样的座位，他会坐在编号最小的座位上。\(另外，如果考场里没有人，那么学生就坐在 0 号座位上。\)

返回 ExamRoom\(int N\) 类，它有两个公开的函数：其中，函数 ExamRoom.seat\(\) 会返回一个 int （整型数据），代表学生坐的位置；函数 ExamRoom.leave\(int p\) 代表坐在座位 p 上的学生现在离开了考场。每次调用 ExamRoom.leave\(p\) 时都保证有学生坐在座位 p 上。

In an exam room, there are `N` seats in a single row, numbered `0, 1, 2, ..., N-1`.

When a student enters the room, they must sit in the seat that maximizes the distance to the closest person.  If there are multiple such seats, they sit in the seat with the lowest number.  \(Also, if no one is in the room, then the student sits at seat number 0.\)

Return a class `ExamRoom(int N)` that exposes two functions: `ExamRoom.seat()` returning an `int` representing what seat the student sat in, and `ExamRoom.leave(int p)` representing that the student in seat number `p` now leaves the room.  It is guaranteed that any calls to `ExamRoom.leave(p)` have a student sitting in seat `p`.

**Example 1:**

```text
Input: ["ExamRoom","seat","seat","seat","seat","leave","seat"], [[10],[],[],[],[],[4],[]]
Output: [null,0,9,4,2,null,5]
Explanation:
ExamRoom(10) -> null
seat() -> 0, no one is in the room, then the student sits at seat number 0.
seat() -> 9, the student sits at the last seat number 9.
seat() -> 4, the student sits at the last seat number 4.
seat() -> 2, the student sits at the last seat number 2.
leave(4) -> null
seat() -> 5, the student sits at the last seat number 5.
```

​​​​​​​

**Note:**

1. `1 <= N <= 10^9`
2. `ExamRoom.seat()` and `ExamRoom.leave()` will be called at most `10^4` times across all test cases.
3. Calls to `ExamRoom.leave(p)` are guaranteed to have a student currently sitting in seat number `p`.

### 分析

维护有序的座位编号 

我们可以用有序集合（Java 中 TreeSet，C++ 中的 set）存储目前有学生的座位编号。当我们要调用 leave\(p\) 函数时，我们只需要把有序集合中的 p 移除即可。当我们要调用 seat\(\) 函数时，我们遍历这个有序集合，对于相邻的两个座位 i 和 j，如果选择在这两个座位之间入座，那么最近的距离 d 为 \(j - i\) / 2，选择的座位为 i + d。除此之外，我们还需要考虑坐在最左侧 0 和最右侧 N - 1 的情况。

### 代码

```java
class ExamRoom {
    int N;
    TreeSet<Integer> students;

    public ExamRoom(int N) {
        this.N = N;
        students = new TreeSet();
    }

    public int seat() {
        //Let's determine student, the position of the next
        //student to sit down.
        int student = 0;
        if (students.size() > 0) {
            //Tenatively, dist is the distance to the closest student,
            //which is achieved by sitting in the position 'student'.
            //We start by considering the left-most seat.
            int dist = students.first();
            Integer prev = null;
            for (Integer s: students) {
                if (prev != null) {
                    //For each pair of adjacent students in positions (prev, s),
                    //d is the distance to the closest student;
                    //achieved at position prev + d.
                    int d = (s - prev) / 2;
                    if (d > dist) {
                        dist = d;
                        student = prev + d;
                    }
                }
                prev = s;
            }

            //Considering the right-most seat.
            if (N - 1 - students.last() > dist)
                student = N - 1;
        }

        //Add the student to our sorted TreeSet of positions.
        students.add(student);
        return student;
    }

    public void leave(int p) {
        students.remove(p);
    }
}
```

## 684 Redundant Connection

### 原题

在本问题中, 树指的是一个连通且无环的无向图。

输入一个图，该图由一个有着N个节点 \(节点值不重复1, 2, ..., N\) 的树及一条附加的边构成。附加的边的两个顶点包含在1到N中间，这条附加的边不属于树中已存在的边。

结果图是一个以边组成的二维数组。每一个边的元素是一对\[u, v\] ，满足 u &lt; v，表示连接顶点u 和v的无向图的边。

返回一条可以删去的边，使得结果图是一个有着N个节点的树。如果有多个答案，则返回二维数组中最后出现的边。答案边 \[u, v\] 应满足相同的格式 u &lt; v。

来源：力扣（LeetCode） 链接：[https://leetcode-cn.com/problems/redundant-connection](https://leetcode-cn.com/problems/redundant-connection) 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

In this problem, a tree is an **undirected** graph that is connected and has no cycles.

The given input is a graph that started as a tree with N nodes \(with distinct values 1, 2, ..., N\), with one additional edge added. The added edge has two different vertices chosen from 1 to N, and was not an edge that already existed.

The resulting graph is given as a 2D-array of `edges`. Each element of `edges` is a pair `[u, v]` with `u < v`, that represents an **undirected** edge connecting nodes `u` and `v`.

Return an edge that can be removed so that the resulting graph is a tree of N nodes. If there are multiple answers, return the answer that occurs last in the given 2D-array. The answer edge `[u, v]` should be in the same format, with `u < v`.

**Example 1:**  


```text
Input: [[1,2], [1,3], [2,3]]
Output: [2,3]
Explanation: The given undirected graph will be like this:
  1
 / \
2 - 3
```

**Example 2:**  


```text
Input: [[1,2], [2,3], [3,4], [1,4], [1,5]]
Output: [1,4]
Explanation: The given undirected graph will be like this:
5 - 1 - 2
    |   |
    4 - 3
```

**Note:**  


The size of the input 2D-array will be between 3 and 1000.

Every integer represented in the 2D-array will be between 1 and N, where N is the size of the input array.

**Update \(2017-09-26\):**  
We have overhauled the problem description + test cases and specified clearly the graph is an **undirected** graph. For the **directed** graph follow up please see [**Redundant Connection II**](https://leetcode.com/problems/redundant-connection-ii/description/)\). We apologize for any inconvenience caused.

### 分析

![](../.gitbook/assets/image%20%28182%29.png)

![](../.gitbook/assets/image%20%28181%29.png)

### 代码

```java
class Solution {
    //缓存结果集
    int[] result = new int[2];

    public int[] findRedundantConnection(int[][] edges) {
        int[] father = new int[edges.length + 1];
        for (int i = 0; i < father.length; i++) {
            father[i] = i;
        }
        for (int[] edge : edges) {
            union(father, edge[0], edge[1]);
        }
        return result;
    }

    //路径压缩
    public int findXFather(int[] father, int x) {
        while (father[x]!=x){
            father[x] = father[father[x]];
            x = father[x];
        }
        return x;
    }

    //合并两个能连接上的点，father合为最后确定的father
    public void union(int[] father, int x, int y) {
        int xFather = findXFather(father, x);
        int yFather = findXFather(father, y);
        if (xFather != yFather) {
            father[xFather]=yFather;
        } else {
            //在发现两个点的连接已经存在时，就更新缓存，题目要最后一个，遍历到最后一个就是结果
            result[0] = x;
            result[1] = y;
        }
    }

}

作者：luma730
链接：https://leetcode-cn.com/problems/redundant-connection/solution/java-bing-cha-ji-by-luma730/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 685 Redundant Connection II

### 原题

在本问题中，有根树指满足以下条件的有向图。该树只有一个根节点，所有其他节点都是该根节点的后继。每一个节点只有一个父节点，除了根节点没有父节点。

输入一个有向图，该图由一个有着N个节点 \(节点值不重复1, 2, ..., N\) 的树及一条附加的边构成。附加的边的两个顶点包含在1到N中间，这条附加的边不属于树中已存在的边。

结果图是一个以边组成的二维数组。 每一个边 的元素是一对 \[u, v\]，用以表示有向图中连接顶点 u 和顶点 v 的边，其中 u 是 v 的一个父节点。

返回一条能删除的边，使得剩下的图是有N个节点的有根树。若有多个答案，返回最后出现在给定二维数组的答案。

来源：力扣（LeetCode） 链接：[https://leetcode-cn.com/problems/redundant-connection-ii](https://leetcode-cn.com/problems/redundant-connection-ii) 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

In this problem, a rooted tree is a **directed** graph such that, there is exactly one node \(the root\) for which all other nodes are descendants of this node, plus every node has exactly one parent, except for the root node which has no parents.

The given input is a directed graph that started as a rooted tree with N nodes \(with distinct values 1, 2, ..., N\), with one additional directed edge added. The added edge has two different vertices chosen from 1 to N, and was not an edge that already existed.

The resulting graph is given as a 2D-array of `edges`. Each element of `edges` is a pair `[u, v]` that represents a **directed** edge connecting nodes `u` and `v`, where `u` is a parent of child `v`.

Return an edge that can be removed so that the resulting graph is a rooted tree of N nodes. If there are multiple answers, return the answer that occurs last in the given 2D-array.

**Example 1:**  


```text
Input: [[1,2], [1,3], [2,3]]
Output: [2,3]
Explanation: The given directed graph will be like this:
  1
 / \
v   v
2-->3
```

**Example 2:**  


```text
Input: [[1,2], [2,3], [3,4], [4,1], [1,5]]
Output: [4,1]
Explanation: The given directed graph will be like this:
5 <- 1 -> 2
     ^    |
     |    v
     4 <- 3
```

**Note:**  


The size of the input 2D-array will be between 3 and 1000.

Every integer represented in the 2D-array will be between 1 and N, where N is the size of the input array.

### 分析

并查集

### 代码

```java
class Solution {
   
   int[] result = new int[2];
    int doubleRoot = 0;
    int[] hadRoot;
    int[][] rootResult = new int[2][2];

    public int[] findRedundantDirectedConnection(int[][] edges) {
        hadRoot = new int[edges.length + 1];
        int[] father = new int[edges.length + 1];
        for (int i = 0; i < father.length; i++) {
            father[i] = i;
        }

        for (int[] edge : edges) {
            hadRoot[edge[1]]++;
            if (hadRoot[edge[1]] == 2) {
                doubleRoot = edge[1];
                rootResult[1] = edge;
            } else {
                union(father, edge[1], edge[0]);
            }
        }
        if (doubleRoot != 0) {
            for (int[] edge : edges) {
                if (edge[1] == doubleRoot) {
                    rootResult[0] = edge;
                    break;
                }
            }
            int root = 0;
            for (int i = 1; i < father.length; i++) {
                if (root == 0) {
                    root = findXFather(father,i);
                }
                if (root != findXFather(father,i)) {
                    return rootResult[0];
                }
            }
            return rootResult[1];
        }
        return result;
    }

    public int findXFather(int[] father, int x) {
        while (father[x] != x) {
            father[x] = father[father[x]];
            x = father[x];
        }
        return x;
    }

    public void union(int[] father, int x, int y) {
        int xFather = findXFather(father, x);
        int yFather = findXFather(father, y);
        if (xFather != yFather) {
            father[xFather] = yFather;
        } else {
            result[0] = y;
            result[1] = x;
        }
    }


}
```

## 

### 原题

### 分析

### 代码




# tag2

## 741 Cherry Pickup

### 题目

一个N x N的网格\(grid\) 代表了一块樱桃地，每个格子由以下三种数字的一种来表示：

* 0 表示这个格子是空的，所以你可以穿过它。 
* 1 表示这个格子里装着一个樱桃，你可以摘到樱桃然后穿过它。 
* -1 表示这个格子里有荆棘，挡着你的路。

你的任务是在遵守下列规则的情况下，尽可能的摘到最多樱桃：

* 从位置 \(0, 0\) 出发，最后到达 \(N-1, N-1\) ，只能向下或向右走，并且只能穿越有效的格子（即只可以穿过值为0或者1的格子）； 
* 当到达 \(N-1, N-1\) 后，你要继续走，直到返回到 \(0, 0\) ，只能向上或向左走，并且只能穿越有效的格子； 
* 当你经过一个格子且这个格子包含一个樱桃时，你将摘到樱桃并且这个格子会变成空的（值变为0）； 
* 如果在 \(0, 0\) 和 \(N-1, N-1\) 之间不存在一条可经过的路径，则没有任何一个樱桃能被摘到。

In a N x N `grid` representing a field of cherries, each cell is one of three possible integers.

* 0 means the cell is empty, so you can pass through;
* 1 means the cell contains a cherry, that you can pick up and pass through;
* -1 means the cell contains a thorn that blocks your way.

Your task is to collect maximum number of cherries possible by following the rules below:

* Starting at the position \(0, 0\) and reaching \(N-1, N-1\) by moving right or down through valid path cells \(cells with value 0 or 1\);
* After reaching \(N-1, N-1\), returning to \(0, 0\) by moving left or up through valid path cells;
* When passing through a path cell containing a cherry, you pick it up and the cell becomes an empty cell \(0\);
* If there is no valid path between \(0, 0\) and \(N-1, N-1\), then no cherries can be collected.

**Example 1:**

```text
Input: grid =
[[0, 1, -1],
 [1, 0, -1],
 [1, 1,  1]]
Output: 5
Explanation: 
The player started at (0, 0) and went down, down, right right to reach (2, 2).
4 cherries were picked up during this single trip, and the matrix becomes [[0,1,-1],[0,0,-1],[0,0,0]].
Then, the player went left, up, up, left to return home, picking up one more cherry.
The total number of cherries picked up is 5, and this is the maximum possible.
```

**Note:**

* `grid` is an `N` by `N` 2D array, with `1 <= N <= 50`.
* Each `grid[i][j]` is an integer in the set `{-1, 0, 1}`.
* It is guaranteed that grid\[0\]\[0\] and grid\[N-1\]\[N-1\] are not -1.

### 分析

自己摘樱桃。DP 

### 代码

topdown

![](../.gitbook/assets/image%20%28179%29.png)

时间复杂度：O\(N^3\)。其中 NN 是 grid 的长度，动态规划有 O\(N^3\)O\(N 3 \) 的状态 

空间复杂度：O\(N^3\)，memo 所使用的空间。

```java
class Solution {
    int[][][] memo;
    int[][] grid;
    int N;
    public int cherryPickup(int[][] grid) {
        this.grid = grid;
        N = grid.length;
        memo = new int[N][N][N];
        for (int[][] layer: memo)
            for (int[] row: layer)
                Arrays.fill(row, Integer.MIN_VALUE);
        return Math.max(0, dp(0, 0, 0));
    }
    public int dp(int r1, int c1, int c2) {
        int r2 = r1 + c1 - c2;
        if (N == r1 || N == r2 || N == c1 || N == c2 ||
                grid[r1][c1] == -1 || grid[r2][c2] == -1) {
            return -999999;        
        } else if (r1 == N-1 && c1 == N-1) {
            return grid[r1][c1];
        } else if (memo[r1][c1][c2] != Integer.MIN_VALUE) {
            return memo[r1][c1][c2];
        } else {
            int ans = grid[r1][c1];
            if (c1 != c2) ans += grid[r2][c2];
            ans += Math.max(Math.max(dp(r1, c1+1, c2+1), dp(r1+1, c1, c2+1)),
                            Math.max(dp(r1, c1+1, c2), dp(r1+1, c1, c2)));
            memo[r1][c1][c2] = ans;
            return ans;
        }
    }
}
```

bottom up

![](../.gitbook/assets/image%20%28175%29.png)

* 时间复杂度：O\(N^3\)。其中 NN 是 `grid` 的长度。
* 空间复杂度：O\(N^2\)，`dp` 和 `dp2` 所使用的空间

```java
class Solution {
    public int cherryPickup(int[][] grid) {
        int N = grid.length;
        int[][] dp = new int[N][N];
        for (int[] row: dp) Arrays.fill(row, Integer.MIN_VALUE);
        dp[0][0] = grid[0][0];

        for (int t = 1; t <= 2*N - 2; ++t) {
            int[][] dp2 = new int[N][N];
            for (int[] row: dp2) Arrays.fill(row, Integer.MIN_VALUE);

            for (int i = Math.max(0, t-(N-1)); i <= Math.min(N-1, t); ++i) {
                for (int j = Math.max(0, t-(N-1)); j <= Math.min(N-1, t); ++j) {
                    if (grid[i][t-i] == -1 || grid[j][t-j] == -1) continue;
                    int val = grid[i][t-i];
                    if (i != j) val += grid[j][t-j];
                    for (int pi = i-1; pi <= i; ++pi)
                        for (int pj = j-1; pj <= j; ++pj)
                            if (pi >= 0 && pj >= 0)
                                dp2[i][j] = Math.max(dp2[i][j], dp[pi][pj] + val);
                }
            }
            dp = dp2;
        }
        return Math.max(0, dp[N-1][N-1]);
    }
}
```

## 1463 Cherry Pickup II

### 原题

给你一个 rows x cols 的矩阵 grid 来表示一块樱桃地。 grid 中每个格子的数字表示你能获得的樱桃数目。

你有两个机器人帮你收集樱桃，机器人 1 从左上角格子 \(0,0\) 出发，机器人 2 从右上角格子 \(0, cols-1\) 出发。

请你按照如下规则，返回两个机器人能收集的最多樱桃数目：

* 从格子 \(i,j\) 出发，机器人可以移动到格子 \(i+1, j-1\)，\(i+1, j\) 或者 \(i+1, j+1\) 。 
* 当一个机器人经过某个格子时，它会把该格子内所有的樱桃都摘走，然后这个位置会变成空格子，即没有樱桃的格子。 
* 当两个机器人同时到达同一个格子时，它们中只有一个可以摘到樱桃。 
* 两个机器人在任意时刻都不能移动到 grid 外面。 
* 两个机器人最后都要到达 grid 最底下一行。

Given a `rows x cols` matrix `grid` representing a field of cherries. Each cell in `grid` represents the number of cherries that you can collect.

You have two robots that can collect cherries for you, Robot \#1 is located at the top-left corner \(0,0\) , and Robot \#2 is located at the top-right corner \(0, cols-1\) of the grid.

Return the maximum number of cherries collection using both robots  by following the rules below:

* From a cell \(i,j\), robots can move to cell \(i+1, j-1\) , \(i+1, j\) or \(i+1, j+1\).
* When any robot is passing through a cell, It picks it up all cherries, and the cell becomes an empty cell \(0\).
* When both robots stay on the same cell, only one of them takes the cherries.
* Both robots cannot move outside of the grid at any moment.
* Both robots should reach the bottom row in the `grid`.

**Example 1:**

![](https://assets.leetcode.com/uploads/2020/04/29/sample_1_1802.png)

```text
Input: grid = [[3,1,1],[2,5,1],[1,5,5],[2,1,1]]
Output: 24
Explanation: Path of robot #1 and #2 are described in color green and blue respectively.
Cherries taken by Robot #1, (3 + 2 + 5 + 2) = 12.
Cherries taken by Robot #2, (1 + 5 + 5 + 1) = 12.
Total of cherries: 12 + 12 = 24.
```

**Example 2:**

![](https://assets.leetcode.com/uploads/2020/04/23/sample_2_1802.png)

```text
Input: grid = [[1,0,0,0,0,0,1],[2,0,0,0,0,3,0],[2,0,9,0,0,0,0],[0,3,0,5,4,0,0],[1,0,2,3,0,0,6]]
Output: 28
Explanation: Path of robot #1 and #2 are described in color green and blue respectively.
Cherries taken by Robot #1, (1 + 9 + 5 + 2) = 17.
Cherries taken by Robot #2, (1 + 3 + 4 + 3) = 11.
Total of cherries: 17 + 11 = 28.
```

**Example 3:**

```text
Input: grid = [[1,0,0,3],[0,0,0,3],[0,0,3,3],[9,0,3,3]]
Output: 22
```

**Example 4:**

```text
Input: grid = [[1,1],[1,1]]
Output: 4
```

**Constraints:**

* `rows == grid.length`
* `cols == grid[i].length`
* `2 <= rows, cols <= 70`
* `0 <= grid[i][j] <= 100` 

### 思路

两个机器人摘樱桃

DP

### 代码

```java


class Solution {
    public int cherryPickup(int[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        // dp[i][j][p]表示当前到达第i行，机器人1在第j列，机器人2在第p列时得最大值
        // 其中：j<=i && p>=m-1-i && j<p 
        // j<=i && p>=m-1-i的原因：比如第0行的中间部分，机器人无法到达
        // j<p 为了让机器人1的路径始终在机器人2的左边。万一有交叉呢？那就把交叉部分互换，反正两个机器人不分你我
        // 使用滚动数组优化
        int[][][] dp = new int[2][m][m];
        dp[0][0][m - 1] = grid[0][0] + grid[0][m - 1];
        int now = 0;
        int[] f = {-1, 0, 1};
        for (int i = 1; i < n; i++) {
            now ^= 1;
            for (int j = 0; j <= i && j < m; j++) {
                for (int p = m - 1; p > j && p >= m - 1 - i; p--) {
                    dp[now][j][p] = 0;
                    int val = grid[i][j] + grid[i][p];
                    for (int x = 0; x < 3; x++) {
                        int fj = j + f[x];
                        if (fj < 0 || fj >= m) {
                            continue;
                        }
                        for (int y = 0; y < 3; y++) {
                            int fp = p + f[y];
                            if (fp < m && fj < fp) {
                                dp[now][j][p] = Math.max(dp[now][j][p], dp[now ^ 1][fj][fp] + val);
                            }
                        }
                    }
                }
            }
        }
        int ans = 0;
        for (int j = 0; j < m; j++) {
            for (int p = j + 1; p < m; p++) {
                ans = Math.max(ans, dp[now][j][p]);
            }
        }
        return ans;
    }
}

```

## 1504 Count Submatrices With All Ones 

### 原题

Given a `rows * columns` matrix `mat` of ones and zeros, return how many **submatrices** have all ones.

**Example 1:**

```text
Input: mat = [[1,0,1],
              [1,1,0],
              [1,1,0]]
Output: 13
Explanation:
There are 6 rectangles of side 1x1.
There are 2 rectangles of side 1x2.
There are 3 rectangles of side 2x1.
There is 1 rectangle of side 2x2. 
There is 1 rectangle of side 3x1.
Total number of rectangles = 6 + 2 + 3 + 1 + 1 = 13.
```

**Example 2:**

```text
Input: mat = [[0,1,1,0],
              [0,1,1,1],
              [1,1,1,0]]
Output: 24
Explanation:
There are 8 rectangles of side 1x1.
There are 5 rectangles of side 1x2.
There are 2 rectangles of side 1x3. 
There are 4 rectangles of side 2x1.
There are 2 rectangles of side 2x2. 
There are 2 rectangles of side 3x1. 
There is 1 rectangle of side 3x2. 
Total number of rectangles = 8 + 5 + 2 + 4 + 2 + 2 + 1 = 24.
```

**Example 3:**

```text
Input: mat = [[1,1,1,1,1,1]]
Output: 21
```

**Example 4:**

```text
Input: mat = [[1,0,1],[0,1,0],[1,0,1]]
Output: 5
```

**Constraints:**

* `1 <= rows <= 150`
* `1 <= columns <= 150`
* `0 <= mat[i][j] <= 1`

### 思路

1）直观暴力枚举

![](../.gitbook/assets/image%20%28162%29.png)

时间复杂度：O\(n^2m\)，其中 nn 为矩阵行数，mm 为矩阵列数。我们预处理 row 数组需要 O\(nm\) 的时间，统计答案的时候一共需要枚举 O\(nm\)个位置，每次枚举的时候需要 O\(n\) 的时间计算，因此时间复杂度为 O\(n^2m\)，故总时间复杂度为 O\(nm+n^2m\)=O\(n^2m\)。 

空间复杂度：O\(nm\)。我们需要 O\(nm\) 的空间来存储 row 数组。

2）单调栈

1. 首先O\(n^2\)的预处理，left\[i\]\[j\]表示\(i,j\)为结束的元素，在一行中，最大能拓展的1的长度。（如其他题解） 
2. 使用单调栈来将O\(m\*n^2\)降低到O\(mn\),可以参考代码中的注释和PPT动图理解。（关键点在与以\(i,j\)为右下角的矩阵构建条件（上一行有效的参与构建的情况，可以模拟一下）） 
3. 为此，首先按列枚举，然后从上到下枚举行，在枚举行的时候，使用单调栈来维护之前已经参与构建矩阵的信息

时间复杂度：O\(nm\)，其中 n 为矩阵行数，m 为矩阵列数。预处理 row 数组需要 O\(nm\) 的时间复杂度，计算答案的时候我们需要对 O\(m\) 列进行统计，每一列统计答案的时候单调栈的时间复杂度为 O\(n\)，因此总时间复杂度为 O\(nm\)。 

空间复杂度：O\(n\)。单调栈最坏情况下需要 O\(n\)的空间。

### 代码

暴力枚举

```java
class Solution {
    public int numSubmat(int[][] mat) {
        int n = mat.length;
        int m = mat[0].length;
        int[][] row = new int[n][m];
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < m; ++j) {
                if (j == 0) {
                    row[i][j] = mat[i][j];
                } else if (mat[i][j] != 0) {
                    row[i][j] = row[i][j - 1] + 1;
                } else {
                    row[i][j] = 0;
                }
            }
        }
        int ans = 0;
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < m; ++j) {
                int col = row[i][j];
                for (int k = i; k >= 0 && col != 0; --k) {
                    col = Math.min(col, row[k][j]);
                    ans += col;
                }
            }
        }
        return ans;
    }
}
```

单调栈

```java
class Solution {
    public int numSubmat(int[][] mat) {
        int n = mat.length;
        int m = mat[0].length;
        int[][] left = new int[n + 1][m + 1];
        int res = 0;
        for (int i = 1; i <= n; i++) {
            int now = 0;
            for (int j = 1; j <= m; j++) {
                if (mat[i - 1][j - 1] == 1) now++;
                else now = 0;
                left[i][j] = now;
            }
        }
        //首先按列枚举
        for (int j = 1; j <= m; j++) {
            int to_sum = 0;//每个顶点(i,j)为矩阵右下角的矩阵个数，动态更新。
            //栈里元素int[]=<当前的栈顶元素的向左能过延伸的最长长度，被push进栈的元素中，大于栈顶元素的个数>
            Stack<int[]> stack = new Stack<>();
            for (int i = 1; i <= n; i++) {
                int cnt = 0;//计算当前要被push进栈的left[i][j]之前，栈中有多少元素大于left[i][j]
                while (!stack.isEmpty() && stack.peek()[0] > left[i][j]) {
                    //把栈中永不会参与到(i,j)为右下角元素的构建那些矩阵单元去掉（参考PPT的动态）
                    //去掉的过程相当于对栈中元素进行了修剪对齐（使之单调）（见ppT动图）
                    to_sum -= (stack.peek()[1] + 1) * (stack.peek()[0] - left[i][j]);
                    cnt +=stack.peek()[1]+1; // 大于当前left[i][j] 的栈中元素个数 计数
                    stack.pop();
                }
                to_sum+=left[i][j];
                res+=to_sum;
                stack.push(new int[]{left[i][j],cnt});
            }
        }
        return res;
        
    }
}
```

## 1231 Divide Chocolate 

### 原题

你有一大块巧克力，它由一些甜度不完全相同的小块组成。我们用数组 sweetness 来表示每一小块的甜度。

你打算和 K 名朋友一起分享这块巧克力，所以你需要将切割 K 次才能得到 K+1 块，每一块都由一些 连续 的小块组成。

为了表现出你的慷慨，你将会吃掉 总甜度最小 的一块，并将其余几块分给你的朋友们。

请找出一个最佳的切割策略，使得你所分得的巧克力 总甜度最大，并返回这个 最大总甜度。

You have one chocolate bar that consists of some chunks. Each chunk has its own sweetness given by the array `sweetness`.

You want to share the chocolate with your `K` friends so you start cutting the chocolate bar into `K+1` pieces using `K` cuts, each piece consists of some **consecutive** chunks.

Being generous, you will eat the piece with the **minimum total sweetness** and give the other pieces to your friends.

Find the **maximum total sweetness** of the piece you can get by cutting the chocolate bar optimally.

**Example 1:**

```text
Input: sweetness = [1,2,3,4,5,6,7,8,9], K = 5
Output: 6
Explanation: You can divide the chocolate to [1,2,3], [4,5], [6], [7], [8], [9]
```

**Example 2:**

```text
Input: sweetness = [5,6,7,8,9,1,2,3,4], K = 8
Output: 1
Explanation: There is only one way to cut the bar into 9 pieces.
```

**Example 3:**

```text
Input: sweetness = [1,2,2,1,2,2,1,2,2], K = 2
Output: 5
Explanation: You can divide the chocolate to [1,2,2], [1,2,2], [1,2,2]
```

**Constraints:**

* `0 <= K < sweetness.length <= 10^4`
* `1 <= sweetness[i] <= 10^5`

### 思路

可以用二分来猜答案，每次试探一个总甜度,假设为x，如果可以找到一种分组方案可以让所有的分组的总甜度都不小于当前二分试探的这个总甜度x，那么就说明当前试探的总甜度x是一个可行解，而且很容易可以想到，所有小于x的甜度肯定也是满足要求的。因此当前x满足要求的话，说明可能还会存在更大的甜度，因此相当于二分中的从大于x的右半部分继续寻找。反之，如果当前总甜度x不满足要求（找不到一种分组方案使得所有的分组的总甜度都不小于x），则说明总甜度x太大了，那么我们就应该往小的找。 是的，这就是二分。

### 代码

```java
class Solution {
    public int maximizeSweetness(int[] arr, int k) {
        // 二分猜答案
        int sum = 0;
        int min = Integer.MAX_VALUE;
        for (int num : arr) {
            sum += num;
            min = Math.min(min, num);
        }

        int low = min;
        int high = sum / (k + 1);
        int ans = 0;
        while (low <= high) {
            int mid = (low + high) >>> 1;
            if (isOk(arr, k + 1, mid)) {
                ans = mid;
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }

        return ans;
    }
    private boolean isOk(int[] arr, int k, int lower) {
        // 划分成k组，每组的和都必须大于lower
        int sum = 0;
        int count = 0;
        for (int num : arr) {
            sum += num;
            if (sum >= lower) {
                count++;
                if (count == k) {
                    return true;
                }
                sum = 0;
            }
        }
        return count >= k;
    }
}
```

## 1345 Jump Game IV

### 原题

Given an array of integers `arr`, you are initially positioned at the first index of the array.

In one step you can jump from index `i` to index:

* `i + 1` where: `i + 1 < arr.length`.
* `i - 1` where: `i - 1 >= 0`.
* `j` where: `arr[i] == arr[j]` and `i != j`.

Return _the minimum number of steps_ to reach the **last index** of the array.

Notice that you can not jump outside of the array at any time.

**Example 1:**

```text
Input: arr = [100,-23,-23,404,100,23,23,23,3,404]
Output: 3
Explanation: You need three jumps from index 0 --> 4 --> 3 --> 9. Note that index 9 is the last index of the array.
```

**Example 2:**

```text
Input: arr = [7]
Output: 0
Explanation: Start index is the last index. You don't need to jump.
```

**Example 3:**

```text
Input: arr = [7,6,9,6,9,6,9,7]
Output: 1
Explanation: You can jump directly from index 0 to index 7 which is last index of the array.
```

**Example 4:**

```text
Input: arr = [6,1,9]
Output: 2
```

**Example 5:**

```text
Input: arr = [11,22,7,7,7,7,7,7,7,22,13]
Output: 3
```

**Constraints:**

* `1 <= arr.length <= 5 * 10^4`
* `-10^8 <= arr[i] <= 10^8`

### 思路

**题意**： 给一个一维的数组，从下标0开始出发，有三种操作：每次可以向左，右移动一位，或者移动到与自己数值相同的位置。

**思路**: 这类搜索求最短步数的解法是**典型的bfs**，注意这题数据的特殊性，**裸bfs会超时**。 这是因为第三个操作枚举所有相同数值的位置时候，如果出现很多 相同值的数值，光枚举相同值时间复杂度会近似O\(n\)，整体就变成O\(n^2\)。

这一步枚举，可以把连续出现相同值的区间只保留左右两个端点，起到搜索剪枝的作用。

### 代码

```java
class Solution {
    final int MAX = Integer.MAX_VALUE;
    public int minJumps(int[] arr) {
        int n = arr.length;
        Queue<Integer> queue = new LinkedList<>();
        Map<Integer, List<Integer>> map = new HashMap<>();
        for(int i=0; i<n; i++){
            int a = arr[i];
            List<Integer> list = map.getOrDefault(a, new ArrayList<>());
            list.add(i);
            map.put(a, list);
        }
        int[] dis = new int[n]; //每个元素到最后一个元素的距离，用来存结果
        Arrays.fill(dis, MAX);
        dis[n-1] = 0;
        queue.offer(n-1);
        boolean[] visited = new boolean[n]; //记录元素是否被访问过，初始都是false
        while(!queue.isEmpty()){
            int x = queue.poll();
            if(x-1>=0 && dis[x-1]==MAX){ //等于MAX说明x左边的元素还没有计算过
                dis[x-1] = dis[x]+1;
                queue.offer(x-1);
            }
            if(x+1<n && dis[x+1]==MAX){
                dis[x+1] = dis[x]+1;
                queue.offer(x+1);
            }
            if(!visited[x]){
                for(int i: map.get(arr[x])){
                    if(dis[i] == MAX){
                        dis[i] = dis[x]+1;
                        queue.offer(i);
                        visited[i] = true;
                    }     
                }
            }         
        }
        return dis[0];
    }
}
```

## 1138 Alphabet Board Path 

### 原题

On an alphabet board, we start at position `(0, 0)`, corresponding to character `board[0][0]`.

Here, `board = ["abcde", "fghij", "klmno", "pqrst", "uvwxy", "z"]`, as shown in the diagram below.

![](https://assets.leetcode.com/uploads/2019/07/28/azboard.png)

We may make the following moves:

* `'U'` moves our position up one row, if the position exists on the board;
* `'D'` moves our position down one row, if the position exists on the board;
* `'L'` moves our position left one column, if the position exists on the board;
* `'R'` moves our position right one column, if the position exists on the board;
* `'!'` adds the character `board[r][c]` at our current position `(r, c)` to the answer.

\(Here, the only positions that exist on the board are positions with letters on them.\)

Return a sequence of moves that makes our answer equal to `target` in the minimum number of moves.  You may return any path that does so.

**Example 1:**

```text
Input: target = "leet"
Output: "DDR!UURRR!!DDD!"
```

**Example 2:**

```text
Input: target = "code"
Output: "RR!DDRR!UUL!R!"
```

**Constraints:**

* `1 <= target.length <= 100`
* `target` consists only of English lowercase letters.

### 思路

坐标计算，左 上 下 右

### 代码

```java
class Solution {

	public String alphabetBoardPath(String target) {
		int x = 0, y = 0, nx = 0, ny = 0;
		StringBuffer ans = new StringBuffer("");
		for (int i = 0; i < target.length(); i++) {
			int temp = target.charAt(i) - 'a' ;
			if (i > 0 && target.charAt(i) == target.charAt(i - 1)) {
				ans.append("!");
			} else {
				nx = temp / 5;
				ny = temp % 5;
				if (ny < y) {
					for (int z = 0; z < y - ny; z++)
						ans.append("L");
				}
				if (nx < x) {
					for (int z = 0; z < x - nx; z++)
						ans.append("U");
				}
				if (nx > x) {
					for (int z = 0; z < nx - x; z++)
						ans.append("D");
				}
				if (ny > y) {
					for (int z = 0; z < ny - y; z++)
						ans.append("R");
				}
				
				ans.append("!");
				x = nx;
				y = ny;
				
			}
		}
		return ans.toString();
	}
}
```

## 562 Longest Line of Consecutive One in Matrix 

### 原题

 Given a 01 matrix **M**, find the longest line of consecutive one in the matrix. The line could be horizontal, vertical, diagonal or anti-diagonal.

**Example:**  


```text
Input:
[[0,1,1,0],
 [0,1,1,0],
 [0,0,0,1]]
Output: 3
```

**Hint:** The number of elements in the given matrix will not exceed 10,000.

### 思路

1）DFS+备忘录

2）二维DP

设 dp\[i\]\[j\] 代表以矩阵第 i 行，第 j 列结尾的连续 1 线段的长度。那么我们需要记录四个 dp 矩阵：横，竖，对角线，以及反对角线分别需要一个 dp 矩阵来记录。

* 横向 dp 矩阵仅需要考虑其左边的连续 1 线段，即 dp\[i\]\[j - 1\]。 
* 纵向 dp 矩阵仅需要考虑其上边的连续 1 线段，即 dp\[i - 1\]\[j\]。 
* 对角线方向 dp 矩阵仅需要考虑其左上的连续 1 线段，即 dp\[i - 1\]\[j - 1\]。 
* 反对角线方向 dp 矩阵仅需要考虑其右上的连续 1 线段，即 dp\[i - 1\]\[j + 1\]。

![](../.gitbook/assets/image%20%28161%29.png)

3\) 状态压缩为以为DP

dp 数组中的每一个位置的值只依赖于上一行。因此不需要将整个矩阵的结果全部存储，只需要保留上一行的结果即可。而对于横向的 dp 数组，由于其不依赖于上一行，上一行的结果也可以不存储。这样可以达到节省空间的效果。

![](../.gitbook/assets/image%20%28173%29.png)

### 代码

二维DP

```java
class Solution {
    public int longestLine(int[][] M) {
        if (M == null || M.length == 0 || M[0].length == 0)
            return 0;
        int ans = 0;
        int[][] horizontal = new int[M.length][M[0].length];
        int[][] vertical = new int[M.length][M[0].length];
        int[][] diagonal = new int[M.length][M[0].length];
        int[][] antidiagonal = new int[M.length][M[0].length];
        for (int i = 0; i != M.length; ++i) {
            for (int j = 0; j != M[0].length; ++j) {
                if (M[i][j] == 0) {
                    horizontal[i][j] = 0;
                    vertical[i][j] = 0;
                    diagonal[i][j] = 0;
                    antidiagonal[i][j] = 0;
                } else {
                    horizontal[i][j] = j > 0 ? horizontal[i][j - 1] + 1 : 1;
                    vertical[i][j] = i > 0 ? vertical[i - 1][j] + 1 : 1;
                    diagonal[i][j] = i > 0 && j > 0 ? diagonal[i - 1][j - 1] + 1 : 1;
                    antidiagonal[i][j] = i > 0 && j < M[0].length - 1 ? antidiagonal[i - 1][j + 1] + 1 : 1;
                    ans = Math.max(ans, horizontal[i][j]);
                    ans = Math.max(ans, vertical[i][j]);
                    ans = Math.max(ans, diagonal[i][j]);
                    ans = Math.max(ans, antidiagonal[i][j]);
                }
            }
        }
        return ans;
    }
}

```

状态压缩为一维

```java
class Solution {
    public int longestLine(int[][] M) {
        if (M == null || M.length == 0 || M[0].length == 0)
            return 0;
        int ans = 0;
        int[] horizontal = new int[M[0].length];
        int[] vertical = new int[M[0].length];
        int[] diagonal = new int[M[0].length];
        int[] antidiagonal = new int[M[0].length];
        for (int i = 0; i != M.length; ++i) {
            int[] vertical_new = new int[M[0].length];
            int[] diagonal_new = new int[M[0].length];
            int[] antidiagonal_new = new int[M[0].length];
            for (int j = 0; j != M[0].length; ++j) {
                if (M[i][j] == 0) {
                    horizontal[j] = 0;
                    vertical_new[j] = 0;
                    diagonal_new[j] = 0;
                    antidiagonal_new[j] = 0;
                } else {
                    horizontal[j] = j > 0 ? horizontal[j - 1] + 1 : 1;
                    vertical_new[j] = i > 0 ? vertical[j] + 1 : 1;
                    diagonal_new[j] = i > 0 && j > 0 ? diagonal[j - 1] + 1 : 1;
                    antidiagonal_new[j] = i > 0 && j < M[0].length - 1 ? antidiagonal[j + 1] + 1 : 1;
                    ans = Math.max(ans, horizontal[j]);
                    ans = Math.max(ans, vertical_new[j]);
                    ans = Math.max(ans, diagonal_new[j]);
                    ans = Math.max(ans, antidiagonal_new[j]);
                }
            }
            vertical = vertical_new;
            diagonal = diagonal_new;
            antidiagonal = antidiagonal_new;
        }
        return ans;
    }
}
```

DFS O（M^2 \* N^2）

```java
public class Solution {
    public int longestLine(int[][] M) {
        if(M == null) return 0;
        int res = 0;
        for(int i =0;i<M.length;i++){
            for(int j = 0;j<M[0].length;j++){
                if(M[i][j] == 1){
                    res = Math.max(res,getMaxOneLine(M, i, j));
                }
            }
        }
        return res;
    }
    final int [][] dirs = new int[][]{{1,0},{0,1},{1,1},{1,-1}};
    private int getMaxOneLine(int [][] M, int x, int y){
        int res = 1;
        for(int [] dir:dirs){
            int i = x+dir[0];
            int j = y+dir[1];
            int count = 1;
            while(isValidPosition(M, i, j) && M[i][j] == 1){
                i+=dir[0];
                j+=dir[1];
                count++;
            }
            res = Math.max(count,res);
        }
        return res;
    }
    
    private boolean isValidPosition(int M[][], int i, int j){
        return (i<M.length && j<M[0].length && i>=0 && j>=0);
    }
}
```

## 690 Employee Importance

### 原题

给定一个保存员工信息的数据结构，它包含了员工唯一的id，重要度 和 直系下属的id。

比如，员工1是员工2的领导，员工2是员工3的领导。他们相应的重要度为15, 10, 5。那么员工1的数据结构是\[1, 15, \[2\]\]，员工2的数据结构是\[2, 10, \[3\]\]，员工3的数据结构是\[3, 5, \[\]\]。注意虽然员工3也是员工1的一个下属，但是由于并不是直系下属，因此没有体现在员工1的数据结构中。

现在输入一个公司的所有员工信息，以及单个员工id，返回这个员工和他所有下属的重要度之和。

You are given a data structure of employee information, which includes the employee's **unique id**, their **importance value** and their **direct** subordinates' id.

For example, employee 1 is the leader of employee 2, and employee 2 is the leader of employee 3. They have importance value 15, 10 and 5, respectively. Then employee 1 has a data structure like \[1, 15, \[2\]\], and employee 2 has \[2, 10, \[3\]\], and employee 3 has \[3, 5, \[\]\]. Note that although employee 3 is also a subordinate of employee 1, the relationship is **not direct**.

Now given the employee information of a company, and an employee id, you need to return the total importance value of this employee and all their subordinates.

**Example 1:**

```text
Input: [[1, 5, [2, 3]], [2, 3, []], [3, 3, []]], 1
Output: 11
Explanation:
Employee 1 has importance value 5, and he has two direct subordinates: employee 2 and employee 3. They both have importance value 3. So the total importance value of employee 1 is 5 + 3 + 3 = 11.
```

**Note:**

1. One employee has at most one **direct** leader and may have several subordinates.
2. The maximum number of employees won't exceed 2000.

### 思路

直观DFS

* 让我们使用 emap = {employee.id -&gt; employee} 快速查询员工。 
* 现在要找出一个员工的总重要性，它将是该员工的重要性，加上该员工每个下属的总重要性。这是一个简单的深度优先搜索。
* 时间复杂度：O\(N\)。其中 N 是员工人数。我们可以在 `DFS` 中查询每个员工。
* 空间复杂度：O\(N\)，计算 `DFS` 时隐式调用堆栈的大小。

### 代码

```java
class Solution {
    Map<Integer, Employee> emap;
    public int getImportance(List<Employee> employees, int queryid) {
        emap = new HashMap();
        for (Employee e: employees) emap.put(e.id, e);
        return dfs(queryid);
    }
    public int dfs(int eid) {
        Employee employee = emap.get(eid);
        int ans = employee.importance;
        for (Integer subid: employee.subordinates)
            ans += dfs(subid);
        return ans;
    }
}
```

## 900 RLE Iterator 

### 原题

编写一个遍历游程编码序列的迭代器。

迭代器由 RLEIterator\(int\[\] A\) 初始化，其中 A 是某个序列的游程编码。更具体地，对于所有偶数 i，A\[i\] 告诉我们在序列中重复非负整数值 A\[i + 1\] 的次数。

迭代器支持一个函数：next\(int n\)，它耗尽接下来的 n 个元素（n &gt;= 1）并返回以这种方式耗去的最后一个元素。如果没有剩余的元素可供耗尽，则 next 返回 -1 。

例如，我们以 A = \[3,8,0,9,2,5\] 开始，这是序列 \[8,8,8,5,5\] 的游程编码。这是因为该序列可以读作 “三个八，零个九，两个五”。

Write an iterator that iterates through a run-length encoded sequence.

The iterator is initialized by `RLEIterator(int[] A)`, where `A` is a run-length encoding of some sequence.  More specifically, for all even `i`, `A[i]` tells us the number of times that the non-negative integer value `A[i+1]` is repeated in the sequence.

The iterator supports one function: `next(int n)`, which exhausts the next `n` elements \(`n >= 1`\) and returns the last element exhausted in this way.  If there is no element left to exhaust, `next` returns `-1` instead.

For example, we start with `A = [3,8,0,9,2,5]`, which is a run-length encoding of the sequence `[8,8,8,5,5]`.  This is because the sequence can be read as "three eights, zero nines, two fives".

**Example 1:**

```text
Input: ["RLEIterator","next","next","next","next"], [[[3,8,0,9,2,5]],[2],[1],[1],[2]]
Output: [null,8,8,5,-1]
Explanation: 
RLEIterator is initialized with RLEIterator([3,8,0,9,2,5]).
This maps to the sequence [8,8,8,5,5].
RLEIterator.next is then called 4 times:

.next(2) exhausts 2 terms of the sequence, returning 8.  The remaining sequence is now [8, 5, 5].

.next(1) exhausts 1 term of the sequence, returning 8.  The remaining sequence is now [5, 5].

.next(1) exhausts 1 term of the sequence, returning 5.  The remaining sequence is now [5].

.next(2) exhausts 2 terms, returning -1.  This is because the first term exhausted was 5,
but the second term did not exist.  Since the last term exhausted does not exist, we return -1.

```

**Note:**

1. `0 <= A.length <= 1000`
2. `A.length` is an even integer.
3. `0 <= A[i] <= 10^9`
4. There are at most `1000` calls to `RLEIterator.next(int n)` per test case.
5. Each call to `RLEIterator.next(int n)` will have `1 <= n <= 10^9`.

### 思路

![](../.gitbook/assets/image%20%28160%29.png)

* 时间复杂度：O\(N + Q\)，其中 NN 是数组 `A` 的长度，QQ 是调用函数 `next()` 的次数。
* 空间复杂度：O\(N\)。

### 代码

```java
class RLEIterator {
    int[] A;
    int i, q;

    public RLEIterator(int[] A) {
        this.A = A;
        i = q = 0;
    }

    public int next(int n) {
        while (i < A.length) {
            if (q + n > A[i]) {
                n -= A[i] - q;
                q = 0;
                i += 2;
            } else {
                q += n;
                return A[i+1];
            }
        }

        return -1;
    }
}

/**
 * Your RLEIterator object will be instantiated and called as such:
 * RLEIterator obj = new RLEIterator(A);
 * int param_1 = obj.next(n);
 */
```

## 551 Student Attendance Record I

### 原题

 You are given a string representing an attendance record for a student. The record only contains the following three characters:

1. **'A'** : Absent.
2. **'L'** : Late.
3. **'P'** : Present.

A student could be rewarded if his attendance record doesn't contain **more than one 'A' \(absent\)** or **more than two continuous 'L' \(late\)**.

You need to return whether the student could be rewarded according to his attendance record.

**Example 1:**  


```text
Input: "PPALLP"
Output: True
```

**Example 2:**  


```text
Input: "PPALLL"
Output: False
```

### 思路

解决这个问题最简单的方法就是同济字符串中 AA 的数目并检查 LLL 是否是给定字符串的一个子串。如果 A 的数目比 2 少且 LLL 不是给定字符串的一个子串，那么返回 true，否则返回 false。

Java 中indexOf 方法可以用来检查一个串是否是另一个串的子串。如果找不到子串，那么返回 -1，否则返回这个字符串第一次出现的位置。

```java
public class Solution {
    public boolean checkRecord(String s) {
        int count=0;
        for(int i=0;i<s.length() && count<2 ;i++)
            if(s.charAt(i)=='A')
                count++;
        return count<2 && s.indexOf("LLL")<0; // 当 A 的数目有 22 个的时候就中断循环
    }
}
```

### 代码

不需要 indexOf 的单遍循环方法，统计 AA 的数目并检查子字符串 LLL 是否是一个子串。

```java
public class Solution {
    public boolean checkRecord(String s) {
        int countA = 0;
        for (int i = 0; i < s.length() && countA < 2; i++) {
            if (s.charAt(i) == 'A')
                countA++;
            if (i <= s.length() - 3 && s.charAt(i) == 'L' && s.charAt(i + 1) == 'L' && s.charAt(i + 2) == 'L')
                return false;
        }
        return countA < 2;
    }
}
```

## 552 Student Attendance Record II

### 原题

Given a positive integer **n**, return the number of all possible attendance records with length n, which will be regarded as rewardable. The answer may be very large, return it after mod 109 + 7.

A student attendance record is a string that only contains the following three characters:

1. **'A'** : Absent.
2. **'L'** : Late.
3. **'P'** : Present.

A record is regarded as rewardable if it doesn't contain **more than one 'A' \(absent\)** or **more than two continuous 'L' \(late\)**.

**Example 1:**  


```text
Input: n = 2
Output: 8 
Explanation:
There are 8 records with length 2 will be regarded as rewardable:
"PP" , "AP", "PA", "LP", "PL", "AL", "LA", "LL"
Only "AA" won't be regarded as rewardable owing to more than one absent times. 
```

**Note:** The value of **n** won't exceed 100,000.

### 思路

![](../.gitbook/assets/image%20%28146%29.png)

![](../.gitbook/assets/image%20%28174%29.png)

### 代码

```java
class Solution {
    public int checkRecord(int n) {
        if(n==0)
            return 0;
        if(n==1)
            return 3;
        if(n==2)
            return 8;
        int max=1000000007;
        long [][][] dp = new long[n+1][2][3];
        //有两个数，且A个数为零，结尾不为L  --PP LP 
        dp[2][0][0]=2;
        //有两个数，且A的个数为1，结果不为L  AP 和PA LA
        dp[2][1][0]=3;
        //有两个数，没有A，且结尾为L  --PL
        dp[2][0][1]=1;
        //有两个数，一个A，且结尾为L -- AL
        dp[2][1][1]=1;
        //有两个数，结尾为两个L   ---LL
        dp[2][0][2]=1;
        //有两个数，结尾两个L，有一个A ---无
        dp[2][1][2]=0;
        for(int i=3;i<=n;i++){
            dp[i][0][0]=(dp[i-1][0][0]+dp[i-1][0][2]+dp[i-1][0][1])%max;
            dp[i][1][0]=(dp[i-1][0][0]+dp[i-1][0][1]+dp[i-1][1][1]+dp[i-1][0][2]+dp[i-1][1][2]+dp[i-1][1][0])%max;
            dp[i][0][1]=dp[i-1][0][0]%max;
            dp[i][1][1]=dp[i-1][1][0]%max;
            dp[i][0][2]=dp[i-1][0][1]%max;
            dp[i][1][2]=dp[i-1][1][1]%max;
        }
        return (int)((dp[n][0][0]+dp[n][1][0]+dp[n][0][1]+dp[n][1][1]+dp[n][0][2]+ dp[n][1][2])%max);
    }
}
```

## 853 Car Fleet

### 原题

`N` cars are going to the same destination along a one lane road.  The destination is `target` miles away.

Each car `i` has a constant speed `speed[i]` \(in miles per hour\), and initial position `position[i]` miles towards the target along the road.

A car can never pass another car ahead of it, but it can catch up to it, and drive bumper to bumper at the same speed.

The distance between these two cars is ignored - they are assumed to have the same position.

A _car fleet_ is some non-empty set of cars driving at the same position and same speed.  Note that a single car is also a car fleet.

If a car catches up to a car fleet right at the destination point, it will still be considered as one car fleet.

  
How many car fleets will arrive at the destination?

**Example 1:**

```text
Input: target = 12, position = [10,8,0,5,3], speed = [2,4,1,1,3]
Output: 3
Explanation:
The cars starting at 10 and 8 become a fleet, meeting each other at 12.
The car starting at 0 doesn't catch up to any other car, so it is a fleet by itself.
The cars starting at 5 and 3 become a fleet, meeting each other at 6.
Note that no other cars meet these fleets before the destination, so the answer is 3.
```

  
**Note:**

1. `0 <= N <= 10 ^ 4`
2. `0 < target <= 10 ^ 6`
3. `0 < speed[i] <= 10 ^ 6`
4. `0 <= position[i] < target`
5. All initial positions are different.

### 思路

做成一个car class，排序后从右往左检查。O\(nlogn\)。

### 代码

```java
class Solution {
    /**
     [0,3,5,8,10]
     [1,3,1,4,2]
     */
    public static int carFleet(int target, int[] position, int[] speed) {
        if (position == null || position.length == 0 || speed == null || speed.length == 0 || position.length != speed.length) {
            return 0;
        }
        int len = position.length;
        Car[] cars = new Car[len];
        for (int i = 0; i < len; i++) {
            cars[i] = new Car(position[i], speed[i], (target - position[i]) * 1.0 / speed[i]);
        }

        // sort by using position
        Arrays.sort(cars, Comparator.comparingLong(a -> a.position));

        int numOfFleets = 1;

        int curCar = len;
        while (--curCar > 0) {
            if (cars[curCar].timeExpected < cars[curCar - 1].timeExpected) {
                numOfFleets++;
            } else {
                cars[curCar - 1] = cars[curCar];
            }
        }
        return numOfFleets;
    }
    static class Car {
        int position;
        int speed;
        double timeExpected;
        public Car(int p, int s, double t) {
            this.position = p;
            this.speed = s;
            this.timeExpected = t;
        }
    }
}
```

## 1240 Tiling a Rectangle with the Fewest Squares

### 原题

Given a rectangle of size `n` x `m`, find the minimum number of integer-sided squares that tile the rectangle.

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/10/17/sample_11_1592.png)

```text
Input: n = 2, m = 3
Output: 3
Explanation: 3 squares are necessary to cover the rectangle.
2 (squares of 1x1)
1 (square of 2x2)
```

**Example 2:**

![](https://assets.leetcode.com/uploads/2019/10/17/sample_22_1592.png)

```text
Input: n = 5, m = 8
Output: 5
```

**Example 3:**

![](https://assets.leetcode.com/uploads/2019/10/17/sample_33_1592.png)

```text
Input: n = 11, m = 13
Output: 6
```

**Constraints:**

* `1 <= n <= 13`
* `1 <= m <= 13`

### 分析

铺瓷砖，房子的客厅大小为 n x m，为保持极简的风格，需要使用尽可能少的 正方形 瓷砖来铺盖地面。

假设正方形瓷砖的规格不限，边长都是整数。最少需要用到多少块方形瓷砖？

![](../.gitbook/assets/image%20%28180%29.png)

时间复杂度： O\(N ^ 4\), 空间复杂度： O\(N ^ 2\)

### 代码

```java
class Solution {
    public int tilingRectangle(int n, int m) {
        int INF = 0x3f3f3f3f; // Integer.MAX_VALUE
        int[][] dp = new int[n + 1][m + 1];
        for(int i = 1; i <= n; i++){
            for(int j = 1; j <= m; j++){
                //如果是正方形
                if(i == j){
                    dp[i][j] = 1;
                    continue;
                }
                dp[i][j] = INF;
                //1.横切
                for(int k = 1; k < i; k++){
                    dp[i][j] = Math.min(dp[i][j], dp[k][j] + dp[i - k][j]);
                }
                //2.竖切
                for(int k = 1; k < j; k++){
                    dp[i][j] = Math.min(dp[i][j], dp[i][k] + dp[i][j - k]);
                }
                
                //3.横竖切
                for(int p = 1; p <= Math.min(i, j); p++){
                    for(int k = 1; k <= p; k++){
                        if(p - k <= 0 || j - p <= 0 || i - p + k <= 0 || j - p - k <= 0) continue;
                        dp[i][j] = Math.min(dp[i][j], dp[p - k][j - p] + 
                                                        dp[i - p + k][j - p - k] +
                                                        dp[i - p][p + k] + 2);
                    }
                }
            }
        }
        return dp[n][m];
    }
}
```

## 840 Magic Squares In Grid

### 原题

3 x 3 的幻方是一个填充有从 1 到 9 的不同数字的 3 x 3 矩阵，其中每行，每列以及两条对角线上的各数之和都相等。给定一个由整数组成的 grid，其中有多少个 3 × 3 的 “幻方” 子矩阵？（每个子矩阵都是连续的）。

A `3 x 3` magic square is a `3 x 3` grid filled with distinct numbers **from** `1` **to** `9` such that each row, column, and both diagonals all have the same sum.

Given a `row x col` `grid` of integers, how many `3 x 3` "magic square" subgrids are there?  \(Each subgrid is contiguous\).

**Example 1:**![](https://assets.leetcode.com/uploads/2020/09/11/magic_main.jpg)

```text
Input: grid = [[4,3,8,4],[9,5,1,9],[2,7,6,2]]
Output: 1
Explanation: 
The following subgrid is a 3 x 3 magic square:

while this one is not:

In total, there is only one magic square inside the given grid.
```

**Example 2:**

```text
Input: grid = [[8]]
Output: 0
```

**Example 3:**

```text
Input: grid = [[4,4],[3,3]]
Output: 0
```

**Example 4:**

```text
Input: grid = [[4,7,8],[9,5,1],[2,3,6]]
Output: 0
```

**Constraints:**

* `row == grid.length`
* `col == grid[i].length`
* `1 <= row, col <= 10`
* `0 <= grid[i][j] <= 15`

### 分析

![](../.gitbook/assets/image%20%28166%29.png)

* 时间复杂度：O\(R\*C\)O\(R∗C\)。其中 R, CR,C 指的是给定 `grid` 的行和列。
* 空间复杂度：O\(1\)O\(1\)。

### 代码

```java
class Solution {
    public int numMagicSquaresInside(int[][] grid) {
        int R = grid.length, C = grid[0].length;
        int ans = 0;
        for (int r = 0; r < R-2; ++r)
            for (int c = 0; c < C-2; ++c) {
                if (grid[r+1][c+1] != 5) continue;  // optional skip
                if (magic(grid[r][c], grid[r][c+1], grid[r][c+2],
                          grid[r+1][c], grid[r+1][c+1], grid[r+1][c+2],
                          grid[r+2][c], grid[r+2][c+1], grid[r+2][c+2]))
                    ans++;
            }

        return ans;
    }

    public boolean magic(int... vals) {
        int[] count = new int[16];
        for (int v: vals) count[v]++;
        for (int v = 1; v <= 9; ++v)
            if (count[v] != 1)
                return false;

        return (vals[0] + vals[1] + vals[2] == 15 &&
                vals[3] + vals[4] + vals[5] == 15 &&
                vals[6] + vals[7] + vals[8] == 15 &&
                vals[0] + vals[3] + vals[6] == 15 &&
                vals[1] + vals[4] + vals[7] == 15 &&
                vals[2] + vals[5] + vals[8] == 15 &&
                vals[0] + vals[4] + vals[8] == 15 &&
                vals[2] + vals[4] + vals[6] == 15);
    }
}
```

## 527 Word Abbreviation

### 原题

给定一个由n个不重复非空字符串组成的数组，你需要按照以下规则为每个单词生成最小的缩写。

1. 初始缩写由起始字母+省略字母的数量+结尾字母组成。 
2. 若存在冲突，亦即多于一个单词有同样的缩写，则使用更长的前缀代替首字母，直到从单词到缩写的映射唯一。换而言之，最终的缩写必须只能映射到一个单词。 
3. 若缩写并不比原单词更短，则保留原样。

输入: \["like", "god", "internal", "me", "internet", "interval", "intension", "face", "intrusion"\] 

输出: \["l2e","god","internal","me","i6t","interval","inte4n","f2e","intr4n"\]

注意:

n和每个单词的长度均不超过 400。 每个单词的长度大于 1。 单词只由英文小写字母组成。 返回的答案需要和原数组保持同一顺序。

Given an array of n distinct non-empty strings, you need to generate **minimal** possible abbreviations for every word following rules below.

1. Begin with the first character and then the number of characters abbreviated, which followed by the last character.
2. If there are any conflict, that is more than one words share the same abbreviation, a longer prefix is used instead of only the first character until making the map from word to abbreviation become unique. In other words, a final abbreviation cannot map to more than one original words.
3. If the abbreviation doesn't make the word shorter, then keep it as original.

**Example:**  


```text
Input: ["like", "god", "internal", "me", "internet", "interval", "intension", "face", "intrusion"]
Output: ["l2e","god","internal","me","i6t","interval","inte4n","f2e","intr4n"]
```

**Note:**

1. Both n and the length of each word will not exceed 400.
2. The length of each word is greater than 1.
3. The words consist of lowercase English letters only.
4. The return answers should be **in the same order** as the original array.

### 分析

单词缩写，这道题求单词的缩写形式，就是首尾字母加上中间字符的个数组成的新字符串，但是要求是不能有重复的缩写字符串，而且说明如果缩写字符串的长度并没有减小的话就保留原来的字符串，比如 god，缩写成 g1d 也没啥用，所以仍是 god。在研究题目中给的例子的时候有些疑惑，虽然知道 internal 和 interval 的缩写形式都是 i6l，会冲突，刚开始不明白的是，为什么不能一个是 i6l，一个是 in5l，这样不就不冲突了么，而题目中的缩写形式居然都是原字符串。后来才搞清楚题目原来是说只要有冲突的都不能用，而 internal 和 interval 是典型的死杠上的一对，i6l，in5l，int4l，inte3l，inter2l，统统冲突，而再往后的缩写长度就和原字符串一样了，所以二者就都保留了原样

1\) 贪心

![](../.gitbook/assets/image%20%28163%29.png)

* 时间复杂度：O\(C^2\)。其中 C 是给定数组中所有字符串的字符总数目。
* 空间复杂度：O\(C\)

2）

![](../.gitbook/assets/image%20%28158%29.png)

* 时间复杂度： O\(ClogC\)，其中 C 是给定数组所有单词的总字符数。该算法时间主要耗费在排序上。
* 空间复杂度： O\(C\)

3\) 

![](../.gitbook/assets/image%20%28147%29.png)

* 时间复杂度：O\(C\)，其中 C 是给定数组中所有单词的字符总数。
* 空间复杂度：O\(C\)。

### 代码

1\) 贪心

```java
class Solution {
    public List<String> wordsAbbreviation(List<String> words) {
        int N = words.size();
        String[] ans = new String[N];
        int[] prefix = new int[N];

        for (int i = 0; i < N; ++i)
            ans[i] = abbrev(words.get(i), 0);

        for (int i = 0; i < N; ++i) {
            while (true) {
                Set<Integer> dupes = new HashSet();
                for (int j = i+1; j < N; ++j)
                    if (ans[i].equals(ans[j]))
                        dupes.add(j);

                if (dupes.isEmpty()) break;
                dupes.add(i);
                for (int k: dupes)
                    ans[k] = abbrev(words.get(k), ++prefix[k]);
            }
        }

        return Arrays.asList(ans);
    }

    public String abbrev(String word, int i) {
        int N = word.length();
        if (N - i <= 3) return word;
        return word.substring(0, i+1) + (N - i - 2) + word.charAt(N-1);
    }
}
```

2） 分组 + 最短公共前缀

```java
class Solution {
    public List<String> wordsAbbreviation(List<String> words) {
        Map<String, List<IndexedWord>> groups = new HashMap();
        String[] ans = new String[words.size()];

        int index = 0;
        for (String word: words) {
            String ab = abbrev(word, 0);
            if (!groups.containsKey(ab))
                groups.put(ab, new ArrayList());
            groups.get(ab).add(new IndexedWord(word, index));
            index++;
        }

        for (List<IndexedWord> group: groups.values()) {
            Collections.sort(group, (a, b) -> a.word.compareTo(b.word));

            int[] lcp = new int[group.size()];
            for (int i = 1; i < group.size(); ++i) {
                int p = longestCommonPrefix(group.get(i-1).word, group.get(i).word);
                lcp[i] = p;
                lcp[i-1] = Math.max(lcp[i-1], p);
            }

            for (int i = 0; i < group.size(); ++i)
                ans[group.get(i).index] = abbrev(group.get(i).word, lcp[i]);
        }

        return Arrays.asList(ans);
    }

    public String abbrev(String word, int i) {
        int N = word.length();
        if (N - i <= 3) return word;
        return word.substring(0, i+1) + (N - i - 2) + word.charAt(N-1);
    }

    public int longestCommonPrefix(String word1, String word2) {
        int i = 0;
        while (i < word1.length() && i < word2.length()
                && word1.charAt(i) == word2.charAt(i))
            i++;
        return i;
    }
}

class IndexedWord {
    String word;
    int index;
    IndexedWord(String w, int i) {
        word = w;
        index = i;
    }
}
```

3） 分组 + Trie 树

```java
class Solution {
    public List<String> wordsAbbreviation(List<String> words) {
        Map<String, List<IndexedWord>> groups = new HashMap();
        String[] ans = new String[words.size()];

        int index = 0;
        for (String word: words) {
            String ab = abbrev(word, 0);
            if (!groups.containsKey(ab))
                groups.put(ab, new ArrayList());
            groups.get(ab).add(new IndexedWord(word, index));
            index++;
        }

        for (List<IndexedWord> group: groups.values()) {
            TrieNode trie = new TrieNode();
            for (IndexedWord iw: group) {
                TrieNode cur = trie;
                for (char letter: iw.word.substring(1).toCharArray()) {
                    if (cur.children[letter - 'a'] == null)
                        cur.children[letter - 'a'] = new TrieNode();
                    cur.count++;
                    cur = cur.children[letter - 'a'];
                }
            }

            for (IndexedWord iw: group) {
                TrieNode cur = trie;
                int i = 1;
                for (char letter: iw.word.substring(1).toCharArray()) {
                    if (cur.count == 1) break;
                    cur = cur.children[letter - 'a'];
                    i++;
                }
                ans[iw.index] = abbrev(iw.word, i-1);
            }
        }

        return Arrays.asList(ans);
    }

    public String abbrev(String word, int i) {
        int N = word.length();
        if (N - i <= 3) return word;
        return word.substring(0, i+1) + (N - i - 2) + word.charAt(N-1);
    }

    public int longestCommonPrefix(String word1, String word2) {
        int i = 0;
        while (i < word1.length() && i < word2.length()
                && word1.charAt(i) == word2.charAt(i))
            i++;
        return i;
    }
}

class TrieNode {
    TrieNode[] children;
    int count;
    TrieNode() {
        children = new TrieNode[26];
        count = 0;
    }
}
class IndexedWord {
    String word;
    int index;
    IndexedWord(String w, int i) {
        word = w;
        index = i;
    }
}

```

## 1368 Minimum Cost to Make at Least One Valid Path in a Grid

### 原题

给你一个 m x n 的网格图 grid 。 grid 中每个格子都有一个数字，对应着从该格子出发下一步走的方向。 grid\[i\]\[j\] 中的数字可能为以下几种情况：

1. 下一步往右走，也就是你会从 grid\[i\]\[j\] 走到 grid\[i\]\[j + 1\] 
2. 下一步往左走，也就是你会从 grid\[i\]\[j\] 走到 grid\[i\]\[j - 1\] 
3. 下一步往下走，也就是你会从 grid\[i\]\[j\] 走到 grid\[i + 1\]\[j\] 
4. 下一步往上走，也就是你会从 grid\[i\]\[j\] 走到 grid\[i - 1\]\[j\]

注意网格图中可能会有 无效数字 ，因为它们可能指向 grid 以外的区域。

一开始，你会从最左上角的格子 \(0,0\) 出发。我们定义一条 有效路径 为从格子 \(0,0\) 出发，每一步都顺着数字对应方向走，最终在最右下角的格子 \(m - 1, n - 1\) 结束的路径。有效路径 不需要是最短路径 。

你可以花费 cost = 1 的代价修改一个格子中的数字，但每个格子中的数字 只能修改一次 。

请你返回让网格图至少有一条有效路径的最小代价。

 Given a _m_ x _n_ `grid`. Each cell of the `grid` has a sign pointing to the next cell you should visit if you are currently in this cell. The sign of `grid[i][j]` can be:

* **1** which means go to the cell to the right. \(i.e go from `grid[i][j]` to `grid[i][j + 1]`\)
* **2** which means go to the cell to the left. \(i.e go from `grid[i][j]` to `grid[i][j - 1]`\)
* **3** which means go to the lower cell. \(i.e go from `grid[i][j]` to `grid[i + 1][j]`\)
* **4** which means go to the upper cell. \(i.e go from `grid[i][j]` to `grid[i - 1][j]`\)

Notice that there could be some **invalid signs** on the cells of the `grid` which points outside the `grid`.

You will initially start at the upper left cell `(0,0)`. A valid path in the grid is a path which starts from the upper left cell `(0,0)` and ends at the bottom-right cell `(m - 1, n - 1)` following the signs on the grid. The valid path **doesn't have to be the shortest**.

You can modify the sign on a cell with `cost = 1`. You can modify the sign on a cell **one time only**.

Return _the minimum cost_ to make the grid have at least one valid path.

**Example 1:**![](https://assets.leetcode.com/uploads/2020/02/13/grid1.png)

```text
Input: grid = [[1,1,1,1],[2,2,2,2],[1,1,1,1],[2,2,2,2]]
Output: 3
Explanation: You will start at point (0, 0).
The path to (3, 3) is as follows. (0, 0) --> (0, 1) --> (0, 2) --> (0, 3) change the arrow to down with cost = 1 --> (1, 3) --> (1, 2) --> (1, 1) --> (1, 0) change the arrow to down with cost = 1 --> (2, 0) --> (2, 1) --> (2, 2) --> (2, 3) change the arrow to down with cost = 1 --> (3, 3)
The total cost = 3.
```

**Example 2:**![](https://assets.leetcode.com/uploads/2020/02/13/grid2.png)

```text
Input: grid = [[1,1,3],[3,2,2],[1,1,4]]
Output: 0
Explanation: You can follow the path from (0, 0) to (2, 2).
```

**Example 3:**![](https://assets.leetcode.com/uploads/2020/02/13/grid3.png)

```text
Input: grid = [[1,2],[4,3]]
Output: 1
```

**Example 4:**

```text
Input: grid = [[2,2,2],[2,2,2]]
Output: 3
```

**Example 5:**

```text
Input: grid = [[4]]
Output: 0
```

**Constraints:**

* `m == grid.length`
* `n == grid[i].length`
* `1 <= m, n <= 100`

### 分析

BFS+记忆搜索 

这题是求最短距离的变种，按最短距离的bfs解法来写。 

在这题中求的最小cost可以当作最短距离，只是这个cost的算法不太一样，当我们使用bfs时向上下左右四个方向扩展，向网络所指方向扩展则cost不变，往其他方向 则cost+1，遍历过程中使用二维数组dst来保存由\(0, 0\)到其他网格的最小花费。

### 代码

```java
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;

class Solution {

    public int minCost(int[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        //由(0, 0)到其他网格的最小花费，为-1则表示待计算
        int dst[][] = new int[n][m];
        //用来保存待扩展的四个方向在纵轴和横轴上的增量，右-左-下-上
        int d[][] = {{}, {0, 1}, {0, -1}, {1, 0}, {-1, 0}};
        for (int i = 0; i < n; i++) {
            Arrays.fill(dst[i], -1);
        }
        //用来执行bfs的队列
        Queue<int[]> queue = new LinkedList<>();
        //int数组三个参数：纵轴、横轴、当前cost
        queue.offer(new int[] {0, 0, 0});
        while (!queue.isEmpty()) {
            int x = queue.size();
            for (int i = 0; i < x; i++) {
                int q[] = queue.poll();
                if (q[0] == n - 1 && q[1] == m - 1) {
                    continue;
                }
                int val = grid[q[0]][q[1]];
                for (int j = 1; j <= 4; j++) {
                    int r = q[0] + d[j][0];
                    int c = q[1] + d[j][1];
                    if (r >= 0 && c >= 0 && r < n && c < m) {
                        int add = j == val ? 0 : 1;
                        if (dst[r][c] == -1 || dst[r][c] > q[2] + add) {
                            dst[r][c] = q[2] + add;
                            queue.offer(new int[] {r, c, dst[r][c]});
                        }
                    }
                }
            }
        }
        return Math.max(0, dst[n - 1][m - 1]);
    }
}
```

## 1444 Number of Ways of Cutting a Pizza

### 原题

给你一个 rows x cols 大小的矩形披萨和一个整数 k ，矩形包含两种字符： 'A' （表示苹果）和 '.' （表示空白格子）。你需要切披萨 k-1 次，得到 k 块披萨并送给别人。

切披萨的每一刀，先要选择是向垂直还是水平方向切，再在矩形的边界上选一个切的位置，将披萨一分为二。如果垂直地切披萨，那么需要把左边的部分送给一个人，如果水平地切，那么需要把上面的部分送给一个人。在切完最后一刀后，需要把剩下来的一块送给最后一个人。

请你返回确保每一块披萨包含 至少 一个苹果的切披萨方案数。由于答案可能是个很大的数字，请你返回它对 10^9 + 7 取余的结果。

Given a rectangular pizza represented as a `rows x cols` matrix containing the following characters: `'A'` \(an apple\) and `'.'` \(empty cell\) and given the integer `k`. You have to cut the pizza into `k` pieces using `k-1` cuts. 

For each cut you choose the direction: vertical or horizontal, then you choose a cut position at the cell boundary and cut the pizza into two pieces. If you cut the pizza vertically, give the left part of the pizza to a person. If you cut the pizza horizontally, give the upper part of the pizza to a person. Give the last piece of pizza to the last person.

_Return the number of ways of cutting the pizza such that each piece contains **at least** one apple._ Since the answer can be a huge number, return this modulo 10^9 + 7.

**Example 1:**

![](https://assets.leetcode.com/uploads/2020/04/23/ways_to_cut_apple_1.png)

```text
Input: pizza = ["A..","AAA","..."], k = 3
Output: 3 
Explanation: The figure above shows the three ways to cut the pizza. Note that pieces must contain at least one apple.
```

**Example 2:**

```text
Input: pizza = ["A..","AA.","..."], k = 3
Output: 1
```

**Example 3:**

```text
Input: pizza = ["A..","A..","..."], k = 1
Output: 1
```

**Constraints:**

* `1 <= rows, cols <= 50`
* `rows == pizza.length`
* `cols == pizza[i].length`
* `1 <= k <= 10`
* `pizza` consists of characters `'A'` and `'.'` only.

### 分析

DP

想着最后总会剩下右下角的区域.. 而且能不能切开,还要判断一下切开的区域有没有水果. 所以先对处理出了一个map数组... map\[i\]\[j\] 表示从i,j开始到最后的区域里面 有多少的水果 map\[i\]\[j\] = map\[i + 1\]\[j\] + map\[i\]\[j+1\] - map\[i+1\]\[j+1\];

然后就定义一个 dp\[k\]\[i\]\[j\] 切了k次 剩下 区域是 i,j 的可行方案有多少种 在方案能行的情况下 dp\[n\]\[i\]\[j\] 有2个来源.. 最后一道横着切 . 或者竖着切 也就是 i 不变 或者 j不变 只变 一个 横着切 dp\[n\]\[i\]\[j\] = \(dp\[n-1\]\[0\]\[j\] + dp\[n-1\]\[1\]\[j\] + dp\[n-1\]\[2\]\[j\] +...dp\[n-1\]\[i-1\]\[j\]\); 如果方案可行的话 同理竖着切....

最后结果就是 dp\[k-1\]\[\]\[\] 的所有值...

### 代码

```java
class Solution {
    public final int MOD = 1000000007;

    public int ways(String[] pizza, int k) {
        int rol = pizza.length;
        int col = pizza[0].length();
        int[][] map = new int[rol + 1][col + 1];
        for (int i = rol - 1; i >= 0; i--) {
            for (int j = col - 1; j >= 0; j--) {
                map[i][j] = map[i + 1][j] + map[i][j+1] - map[i+1][j+1];
                if (pizza[i].charAt(j) == 'A') {
                    map[i][j]++;
                }
            }
        }
        int[][][] dp = new int[k][rol][col];
        dp[0][0][0] = 1;
        for (int n = 1; n < k; n++) {   //切n次
            for (int i = 0; i < rol; i++) {
                for (int j = 0; j < col; j++) {
                    //剩下i , j
                    int count = 0;
                    for (int l = 0; l < i; l++) {   //j不变
                        int flag = map[l][j] - map[i][j];
                        if (flag != 0 && map[i][j] != 0) {
                            count += dp[n - 1][l][j];
                            count %= MOD;
                        }
                    }
                    for (int l = 0; l < j; l++) {
                        int flag = map[i][l] - map[i][j];
                        if (flag != 0 && map[i][j] != 0) {
                            count += dp[n - 1][i][l];
                            count %= MOD;
                        }
                    }
                    dp[n][i][j] = count;
                }
            }
        }
        int res = 0;
        for (int i = 0; i < rol; i++) {
            for (int j = 0; j < col; j++) {
                res += dp[k - 1][i][j];
                res %= MOD;
            }
        }
        return res;
    }
}
```

## 


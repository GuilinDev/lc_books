# DFS



* 万金油的DFS
  * 如何理解枚举型DFS
  * 怎样写DFS的程序比较舒服？
  * Add Operators
* DFS中的剪枝
  * 什么时候要剪枝？
  * 怎样剪枝？
    * Word Squares

## 200 Number of Islands

### 原题概述

Given a 2d grid map of `'1'`s \(land\) and `'0'`s \(water\), count the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

**Example 1:**

```text
Input:
11110
11010
11000
00000

Output: 1
```

**Example 2:**

```text
Input:
11000
11000
00100
00011

Output: 3
```

### 题意和分析

1. DFS，找到'1'后，累加一下结果，然后上下左右四个方向DFS修改'1'成别的值，以免重复计算；
2. BFS
3. Union Find，1D

### 代码

DFS

```java
class Solution {
    public int numIslands(char[][] grid) {
        int result = 0;
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return result;
        }
        int rows = grid.length;
        int cols = grid[0].length;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == '1') {
                    result++;
                    dfs(grid, i, j, rows, cols);
                }
            }
        }
        return result;
    }
    private void dfs(char[][] grid, int i, int j, int rows, int cols) {
        if (i < 0 || i >= rows || j < 0 || j >= cols || grid[i][j] != '1') {
            return;
        }
        grid[i][j] = '0'; //每次找到一块陆地就变为‘0’。以防重复查找
        dfs(grid, i + 1, j, rows, cols);
        dfs(grid, i - 1, j, rows, cols);
        dfs(grid, i, j + 1, rows, cols);
        dfs(grid, i, j - 1, rows, cols);
    }
}

```

BFS，这里用了额外的一个2D数组来存储是否已经访问过，如果可以改变原数组的值，则可以不要visited数组。

```java
class Solution {
    public int numIslands(char[][] grid) {
        int result = 0;
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return result;
        }
        int rows = grid.length;
        int cols = grid[0].length;
        
        // 四个方向扩散
        int[][] directions = {{-1, 0}, {0, -1}, {1, 0}, {0, 1}};
        // 标记已经已经访问过
        boolean[][] visited = new boolean[rows][cols];
        
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                // 如果是岛屿中的一个点，并且没有被访问过
                // 从坐标为 (i,j) 的点开始进行BFS
                if (!visited[i][j] && grid[i][j] == '1') {
                    result++;
                    LinkedList<Integer> queue = new LinkedList<>(); // 扩散中每一圈的元素
                    
                    // 这里在queue中把当前元素的坐标转换成一个数字，不然就得用一个数组来存储当前的元素的坐标
                    // 坐标转换类似于2D数组中的二分查找
                    queue.offerLast(i * cols + j); 
                    
                    visited[i][j] = true; //标记访问过
                    
                    while (!queue.isEmpty()) {
                        int current = queue.pollFirst();
                        // 根据上面存储的数字进行坐标转换
                        int curX = current / cols;
                        int curY = current % cols;
                        
                        // 得到需要扩散的四个方向的x,y坐标
                        for (int k = 0; k < 4; k++) {
                            int newX = curX + directions[k][0];
                            int newY = curY + directions[k][1];
                            
                            // 扩散中如果不越界、没有被访问过、并且还是岛屿，就继续放入队列，同时要记得标记已经访问过
                            if (newX >= 0 && newX < rows && newY >= 0 && newY < cols && 
                               grid[newX][newY] == '1' &&
                               !visited[newX][newY]) {
                                queue.offerLast(newX * cols + newY); //扩散中的元素的坐标仍然转换为数字
                                visited[newX][newY] = true;
                            }
                        }
                    }
                }
            }
        }
        return result;
    }
}
```

Union Find，整体思路：  
    1. 实现Union函数  
    2. 循环元素值 并判断下侧和右侧是否为岛屿  
        如果 是：合并元素值  
        如果 否：不做处理

    3. 将海洋对应的nums数组位置设为-2

```java
class Solution {
    public int numIslands(char[][] grid) {
        int result = 0;
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return result;
        }
        int rows = grid.length;
        int cols = grid[0].length;

        // 1维数组存储连通后剩余岛屿的数量(-1)，以及连通后的水域(0, -2)
        int[] nums = new int[rows * cols];
        // 初始值，不连通或自连通
        Arrays.fill(nums, -1);

        for(int i = 0; i < rows; i++) {
            for(int j = 0; j < cols; j++) {
                if(grid[i][j] == '1') {
                    grid[i][j] = '0'; // 不用额外数组，先把自己修改一下

                    //判断下侧是否有岛屿,同BFS解法中的坐标转换为数字
                    if(i < (rows - 1) && grid[i + 1][j] == '1') {
                        union(nums, i * cols + j, (i + 1) * cols + j);
                    }

                    //判断右侧是否有岛屿,同BFS解法中的坐标转换为数字
                    if(j < (cols - 1) && grid[i][j + 1] == '1') {
                        union(nums, i * cols + j, i * cols + j + 1);
                    }
                } else {
                    nums[i * cols + j] = -2;
                }
            }
        }

        for(int num : nums) { // 检查连通后的岛屿
            if(num == -1) {
                result++;
            }
        }

        return result;
    }

    public int find(int[] parents, int i) {
        if(parents[i] == -1) { // 连通
            return i;
        }

        return find(parents, parents[i]);
    }

    public void union(int[] parents, int x, int y) {
        int xset = find(parents, x);
        int yset = find(parents, y);
        if(xset != yset) {
            parents[xset] = yset;
        }
    }
}
```

## 305 Number of Islands II

### 题目

A 2d grid map of m rows and n columns is initially filled with water. We may perform an addLand operation which turns the water at position \(row, col\) into a land. Given a list of positions to operate, count the number of islands after each addLand operation. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

Example:

```java
Input: m = 3, n = 3, positions = [[0,0], [0,1], [1,2], [2,1]] 
Output: [1,1,2,3]
```

Explanation:

Initially, the 2d grid grid is filled with water. \(Assume 0 represents water and 1 represents land\).

```java
0 0 0 
0 0 0 
0 0 0
```

Operation \#1: addLand\(0, 0\) turns the water at grid\[0\]\[0\] into a land.

```java
1 0 0 
0 0 0 Number of islands = 1 
0 0 0
```

Operation \#2: addLand\(0, 1\) turns the water at grid\[0\]\[1\] into a land.

```java
1 1 0 
0 0 0 Number of islands = 1 
0 0 0
```

peration \#3: addLand\(1, 2\) turns the water at grid\[1\]\[2\] into a land.

```java
1 1 0 
0 0 1 Number of islands = 2 
0 0 0
```

Operation \#4: addLand\(2, 1\) turns the water at grid\[2\]\[1\] into a land.

```java
1 1 0 
0 0 1 Number of islands = 3 
0 1 0
```

Follow up:

Can you do it in time complexity O\(k log mn\), where k is the length of the positions?

### 分析

暴力法是复用第200题Number of Islands的DFS代码，每添加一次就去查一下有多少个岛屿，时间复杂度O\(L \* m \* n\)，其中L是添加岛屿的次数。

这里要满足O\(k logmn\)的时间复杂度，依然用并查集的办法，每次添加岛屿后，求出连通图的个数，思路就是把2D数组当做一个无向图（以邻接矩阵的方式组织），横向或者纵向相邻的节点之间有一条值为 1 的边，那么问题就变成了每次 addLand 操作之后在图中寻找连通部分的问题。

### 代码

```java
class Solution {

    private int[][] dir = {{0, 1}, {0, -1}, {-1, 0}, {1, 0}};

    public List<Integer> numIslands2(int m, int n, int[][] positions) {
        UnionFind2D islands = new UnionFind2D(m, n);
        List<Integer> results = new ArrayList<>();
        
        for (int[] position : positions) {
            int x = position[0], y = position[1];        
            int positionIndex = islands.getID(x, y);

            if (positionIndex == 0) {    
                int p = islands.add(x, y);
                for (int[] d : dir) {
                    int q = islands.getID(x + d[0], y + d[1]);
                    if (q > 0 && !islands.find(p, q)) {
                        islands.unite(p, q);
                    }
                }
            }

            results.add(islands.size());
        }
        return results;
    }
}

// 以下是并查集的模板
class UnionFind2D {
    private int[] id;
    private int[] sz;
    private int m, n, count;

    public UnionFind2D(int m, int n) {
        this.count = 0;
        this.n = n;
        this.m = m;
        this.id = new int[m * n + 1];
        this.sz = new int[m * n + 1];
    }

    public int index(int x, int y) { return x * n + y + 1; }

    public int size() { return this.count; }

    public int getID(int x, int y) {
        if (0 <= x && x < m && 0<= y && y < n)
            return id[index(x, y)];
        return 0;
    }

    public int add(int x, int y) {
        int i = index(x, y);
        id[i] = i; sz[i] = 1;
        ++count;
        return i;
    }

    public boolean find(int p, int q) {
        return root(p) == root(q);
    }

    public void unite(int p, int q) {
        int i = root(p), j = root(q);
        if (sz[i] < sz[j]) { //weighted quick union
            id[i] = j; sz[j] += sz[i];
        } else {
            id[j] = i; sz[i] += sz[j];
        }
        count--;
    }

    private int root(int i) {
        for (;i != id[i]; i = id[i])
            id[i] = id[id[i]]; //并查集的path compression
        return i;
    }
}
```

## 695 Max Area of Islands

### 原题概述

Given a non-empty 2D array `grid` of 0's and 1's, an **island** is a group of `1`'s \(representing land\) connected 4-directionally \(horizontal or vertical.\) You may assume all four edges of the grid are surrounded by water.

Find the maximum area of an island in the given 2D array. \(If there is no island, the maximum area is 0.\)

**Example 1:**

```text
[[0,0,1,0,0,0,0,1,0,0,0,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,1,1,0,1,0,0,0,0,0,0,0,0],
 [0,1,0,0,1,1,0,0,1,0,1,0,0],
 [0,1,0,0,1,1,0,0,1,1,1,0,0],
 [0,0,0,0,0,0,0,0,0,0,1,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,0,0,0,0,0,0,1,1,0,0,0,0]]
```

Given the above grid, return `6`. Note the answer is not 11, because the island must be connected 4-directionally.

**Example 2:**

```text
[[0,0,0,0,0,0,0,0]]
```

Given the above grid, return `0`.

**Note:** The length of each dimension in the given `grid` does not exceed 50.

### 题意和分析

找二维数组中最大的岛的面积，岛为1，这个几乎就是上一道题的翻版，同样也是DFS，BFS，Union Find的做法。

### 代码

DFS

```java
class Solution {
   public int maxAreaOfIsland(int[][] grid) {
      int maxArea = 0;
      for (int i = 0; i < grid.length; i++) {
         for (int j = 0; j < grid[0].length; j++) {
            if (grid[i][j] == 1) {
               // 每个点扩散后取最大
               maxArea = Math.max(maxArea, dfs(grid, i, j));
            }
         }
      }

      return maxArea;
   }
   private int dfs(int[][] grid, int i, int j) {
      if (i >= 0 && i < grid.length && j >= 0 && j < grid[0].length && grid[i][j] == 1) {
         grid[i][j] = 0;//计算过了
         return 1 + dfs(grid, i - 1, j) + dfs(grid, i + 1, j) + dfs(grid, i, j - 1) + dfs(grid, i, j + 1);//记得前面的1是本身，需要加上
      }
      return 0;//当前元素为0，不是island，直接返回
   }
}
```

BFS

```java
class Solution {
    private static int[][] DIRECTIONS = new int[][]{{0, 1}, {0, -1}, {1, 0}, {-1, 0}};

    // BFS
    public int maxAreaOfIsland(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) return 0;
        int M = grid.length;
        int N = grid[0].length;
        boolean[][] visited = new boolean[M][N];
        int res = 0;
        for (int i=0; i<M; i++) {
            for (int j=0; j<N; j++) {
                if (grid[i][j] == 1 && !visited[i][j]) {
                    res = Math.max(res, bfs(grid, visited, i, j));
                }
            }
        }
        return res;
    }

    private int bfs(int[][] grid, boolean[][] visited, int i, int j) {
        Queue<int[]> q = new LinkedList<>();
        q.add(new int[]{i, j});
        visited[i][j] = true;
        int res = 0;
        while (!q.isEmpty()) {
            int[] curr = q.poll();
            res++;
            for (int[] dir: DIRECTIONS) {
                int x = curr[0] + dir[0];
                int y = curr[1] + dir[1];
                if (x < 0 || y < 0 || x >= grid.length || y >= grid[0].length || visited[x][y] || grid[x][y] != 1) continue;
                q.add(new int[]{x, y});
                visited[x][y] = true;
            }
        }
        return res;
    }
}
```

Union Find

```java
class Solution {
    
    private static int[][] DIRECTIONS = new int[][]{{0, 1}, {0, -1}, {1, 0}, {-1, 0}};

    public int maxAreaOfIsland(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) return 0;
        int M = grid.length;
        int N = grid[0].length;
        int res = 0;
        UinionFind uf = new UinionFind(grid);
        for (int i=0; i<M; i++) {
            for (int j=0; j<N; j++) {
                if (grid[i][j] == 1) {
                    res = Math.max(res, link(grid, i, j, uf, M, N));
                }
            }
        }
        return res;
    }

    
    private int link(int[][] grid, int i, int j, UinionFind uf, int M, int N) {
        int pre = i * N + j;
        int res = uf.getSize(pre);
        for (int[] dir: DIRECTIONS) {
            int x = i + dir[0];
            int y = j + dir[1];
            if (x < 0 || y < 0 || x >= M || y >= N || grid[x][y] != 1) continue;
            res = Math.max(res, uf.union(pre, x * N + y));
        }
        return res;
    }

    class UinionFind {
        int[] parent;
        int[] size;
        int[] rank;

        UinionFind(int[][] grid) {
            int M = grid.length;
            int N = grid[0].length;
            this.parent = new int[M * N];
            for (int i=0; i<M*N; i++) this.parent[i] = i;
            this.rank = new int[M * N];
            this.size = new int[M * N];
            Arrays.fill(this.size, 1);
        }

        int find(int x) {
            if (this.parent[x] != x) {
                this.parent[x] = find(this.parent[x]);
            }
            return this.parent[x];
        }

        int union(int x, int y) {
            int px = find(x);
            int py = find(y);

            if (px == py) return this.size[px];
            if (this.rank[px] > this.rank[py]) {
                this.parent[py] = px;
                this.size[px] = this.size[px] + this.size[py];
                return this.size[px];
            } else if (this.rank[px] < this.rank[py]) {
                this.parent[px] = py;
                this.size[py] = this.size[px] + this.size[py];
                return this.size[py];
            } else {
                this.parent[px] = py;
                this.rank[py]++;
                this.size[py] = this.size[px] + this.size[py];
                return this.size[py];
            }
        }

        int getSize(int x) {
            return this.size[find(x)];
        }
    }
}
```

## 98 Validate Binary Search Tree

### 题目

Given a binary tree, determine if it is a valid binary search tree \(BST\).

Assume a BST is defined as follows:

* The left subtree of a node contains only nodes with keys **less than** the node's key.
* The right subtree of a node contains only nodes with keys **greater than** the node's key.
* Both the left and right subtrees must also be binary search trees.

**Example 1:**

```text
    2
   / \
  1   3

Input: [2,1,3]
Output: true
```

**Example 2:**

```text
    5
   / \
  1   4
     / \
    3   6

Input: [5,1,4,null,null,3,6]
Output: false
Explanation: The root node's value is 5 but its right child's value is 4.
```

### 分析

一棵BST定义为：

* 节点的左子树中的值要严格小于该节点的值。
* 节点的右子树中的值要严格大于该节点的值。
* 左右子树也必须是二叉查找树。
* 一个节点的树也是二叉查找树。

如果该二叉树的左子树不为空，则左子树上所有节点的值均小于它的根节点的值； 若它的右子树不空，则右子树上所有节点的值均大于它的根节点的值；它的左右子树也为二叉搜索树。

### 代码

递归前序遍历，每次限定传送参数的范围

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public boolean isValidBST(TreeNode root) {
        if (root == null || (root.left == null && root.right == null)) {
            return true;
        }
        return dfs(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }
    private boolean dfs (TreeNode node, long min, long max) {
        if (node == null) {
            return true;
        }
        if (node.val <= min || node.val >= max) { // 严格大于或小于
            return false;
        }
        return dfs(node.left, min, node.val) && dfs(node.right, node.val, max); // int -> long autobox
    }
}
```

递归中序遍历，仔细想想为什么中序遍历只需判断节点的值一次

```java
class Solution {
    long pre = Long.MIN_VALUE;
    public boolean isValidBST(TreeNode root) {
        if (root == null) {
            return true;
        }
        
        // 按照中序遍历，访问左子树到底
        if (!isValidBST(root.left)) {
            return false;
        }
        
        if (root.val <= pre) { // 递归中“归”的部分进行判断
            return false;
        }
        
        pre = root.val;
        
        // 访问右子树
        return isValidBST(root.right);
    }
}
```

Iterative

1\) Queue的BFS

```java
class Solution {
    Queue<TreeNode> queue = new LinkedList<>();
    Queue<Long> upperList = new LinkedList<>(), lowerList = new LinkedList<>();
        
    public boolean isValidBST(TreeNode root) {
        long lower = Long.MIN_VALUE, upper = Long.MAX_VALUE, val;
        
        update(root, lower, upper);
        
        while (!queue.isEmpty()) {
            // 这个不用size分层了，只需把node往队列里面加就行
            TreeNode node = queue.poll();
            lower = lowerList.poll();
            upper = upperList.poll();
            if (node == null) {
                continue;
                }
            val = node.val;
            if (val <= lower) {
                return false;
                }
            if (val >= upper) {
                return false;
                }
            update(node.left, lower, val);
            update(node.right, val, upper);
        }
        return true;
    }

    void update(TreeNode node, long lower, long upper) {
        queue.offer(node);
        lowerList.offer(lower);
        upperList.offer(upper);
    }
}
```

2\) Stack迭代实现前序遍历

```java
class Solution {
    Stack<TreeNode> st = new Stack<>();
    Stack<Long> upperList = new Stack<>(), 
        lowerList = new Stack<>();
        
    public boolean isValidBST(TreeNode root) {
        long lower = Long.MIN_VALUE, upper = Long.MAX_VALUE, val;
        update(root, lower, upper);
        while (!st.empty()) {
            root = st.pop();
            lower = lowerList.pop();
            upper = upperList.pop();
            if (root == null) continue;
            val = (long)root.val;
            if (val <= lower) return false;
            if (val >= upper) return false;
            update(root.right, val, upper);
            update(root.left, lower, val);
        }
        return true;
    }

    void update(TreeNode node, long lower, long upper) {
        st.push(node);
        lowerList.push(lower);
        upperList.push(upper);
    }
}
```

## 545 Boundary of Binary Tree

### 题目

Given a binary tree, return the values of its boundary in **anti-clockwise** direction starting from root. Boundary includes left boundary, leaves, and right boundary in order without duplicate **nodes**.  \(The values of the nodes may still be duplicates.\)

**Left boundary** is defined as the path from root to the **left-most** node. **Right boundary** is defined as the path from root to the **right-most** node. If the root doesn't have left subtree or right subtree, then the root itself is left boundary or right boundary. Note this definition only applies to the input binary tree, and not applies to any subtrees.

The **left-most** node is defined as a **leaf** node you could reach when you always firstly travel to the left subtree if exists. If not, travel to the right subtree. Repeat until you reach a leaf node.

The **right-most** node is also defined by the same way with left and right exchanged.

**Example 1**

```text
Input:
  1
   \
    2
   / \
  3   4

Ouput:
[1, 3, 4, 2]

Explanation:
The root doesn't have left subtree, so the root itself is left boundary.
The leaves are node 3 and 4.
The right boundary are node 1,2,4. Note the anti-clockwise direction means you should output reversed right boundary.
So order them in anti-clockwise without duplicates and we have [1,3,4,2].
```

**Example 2**

```text
Input:
    ____1_____
   /          \
  2            3
 / \          / 
4   5        6   
   / \      / \
  7   8    9  10  
       
Ouput:
[1,2,4,7,8,9,10,6,3]

Explanation:
The left boundary are node 1,2,4. (4 is the left-most node according to definition)
The leaves are node 4,7,8,9,10.
The right boundary are node 1,3,6,10. (10 is the right-most node).
So order them in anti-clockwise without duplicate nodes we have [1,2,4,7,8,9,10,6,3].
```

### 分析

1\) 方法1，将问题划分成三个子问题：左边界、叶子节点和右边界。

    \* 左边界：沿左边遍历这棵树，不断向 结果数组中添加节点，并保证当前节点不是叶子节点。判断左边界的标准是如果某个节点，发现不存在左孩子，但存在右孩子，我们就将右孩子放入 结果数组中中并重复过程。

    \* 叶子节点：调用递归程序 addLeaves\(res, root\)，每次调用改变根节点。如果当前节点是叶子节点，就会加入 结果数组；否则，递归调用左孩子作为新根节点进行递归，左孩子调用完然后是右孩子。

    \* 和处理左边界同样的步骤。但此时沿着右边遍历。如果不存在右孩子，就向左孩子移动。同时，因为右边界是从下到上的，所以递归处理的顺序需要改变一下，（也可以用一个栈，先把右边界遍历到的节点加入到栈中，在完成遍历之后从栈中弹出元素并加入到结果数组中）。

**时间复杂度**： O\(N\)，一次完整的叶子节点遍历，和左右两次深度的遍历，依然是O\(N\)。

**空间复杂度**： O\(N\)，结果数组和递归栈开销。

2\) 方法2，先序遍历，根左右先序遍历的顺序如下图：

![](../../.gitbook/assets/image%20%28107%29.png)

用一个leftbound和一个rightbound在先序的过程中判断左子树和右子树，叶子节点判断也是没有左右孩子。

### 代码

分成三个边的问题

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {

    public List<Integer> boundaryOfBinaryTree(TreeNode root) {
        List<Integer> result = new LinkedList<>();
        if (root == null) {
            return result;
        }
        
        if (!isLeaf(root)) {
            result.add(root.val);
        }
        
        // 先左边界，再叶子节点，最后右边界
        leftTrunk(root.left, result); // 从root的左孩子开始判断左边界
        addLeaves(root, result); // 从root开始判断叶子节点
        rightTrunk(root.right, result); // 从root的右孩子开始判断右边界

        return result;
    }

    // 左边界，没有左孩子，需要有右孩子，等同于不是叶子节点的情况下，没有左孩子
    private void leftTrunk(TreeNode root, List<Integer> result) {
        if (root == null) {
            return;
        }
        if (!isLeaf(root)) {
            result.add(root.val);
        }
        if (root.left != null) {
            leftTrunk(root.left, result);
        } else {
            leftTrunk(root.right, result);
        }
    }

    // 右边界，没有右孩子，需要有左孩子，等同于不是叶子节点的情况下，没有右孩子
    private void rightTrunk(TreeNode root, List<Integer> result) {
        if (root == null) {
            return;
        }
        if (root.right != null) {
            rightTrunk(root.right, result);
        } else {
            rightTrunk(root.left, result);
        }
        if (!isLeaf(root)) {
            result.add(root.val);
        }
    }

    // 叶子节点，左右孩子都没有
    private void addLeaves(TreeNode node, List<Integer> result) {
        if (node == null) {
            return;
        }
        if (isLeaf(node)) {
            result.add(node.val);
        } else {
            addLeaves(node.left, result);
            addLeaves(node.right, result);
        }
    }

    private boolean isLeaf(TreeNode node) {
        return node.left == null && node.right == null;
    }
}
```

按照先序的顺序标记

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<Integer> boundaryOfBinaryTree(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        dfs(root, true, true, result);
        return result;
    }

    private void dfs(TreeNode node, boolean leftBound, boolean rightBound, List<Integer> result) {
        if (node == null) {
            return;
        }
        if (leftBound) { // 先判断是否是左孩子
            result.add(node.val);
        } else if (node.left == null && node.right == null) { // 判断叶子节点
            result.add(node.val);
            return;
        }
        dfs(node.left, leftBound, !leftBound && rightBound && node.right == null, result);
        dfs(node.right, !rightBound && leftBound && node.left == null, rightBound, result);
        
        if (!leftBound && rightBound) {
            result.add(node.val);
        }
    }
}
```

## 339 Nested List Weight Sum

### 原题

Given a nested list of integers, return the sum of all integers in the list weighted by their depth.

Each element is either an integer, or a list -- whose elements may also be integers or other lists.

Example 1:

```text
Input: [[1,1],2,[1,1]] 
Output: 10 Explanation: Four 1's at depth 2, one 2 at depth 1.
```

Example 2:

```text
Input: [1,[4,[6]]] Output: 27 
Explanation: One 1 at depth 1, one 4 at depth 2, and one 6 at depth 3; 1 + 42 + 63 = 27.
```

### 分析

看到嵌套，会想到用递归或者Stack或者Queue，这道题就是题目给的API需要读一下，面试中可能需要自己设计和封装这些API。

### 代码

递归DFS

```java
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * public interface NestedInteger {
 *     // Constructor initializes an empty nested list.
 *     public NestedInteger();
 *
 *     // Constructor initializes a single integer.
 *     public NestedInteger(int value);
 *
 *     // @return true if this NestedInteger holds a single integer, rather than a nested list.
 *     public boolean isInteger();
 *
 *     // @return the single integer that this NestedInteger holds, if it holds a single integer
 *     // Return null if this NestedInteger holds a nested list
 *     public Integer getInteger();
 *
 *     // Set this NestedInteger to hold a single integer.
 *     public void setInteger(int value);
 *
 *     // Set this NestedInteger to hold a nested list and adds a nested integer to it.
 *     public void add(NestedInteger ni);
 *
 *     // @return the nested list that this NestedInteger holds, if it holds a nested list
 *     // Return null if this NestedInteger holds a single integer
 *     public List<NestedInteger> getList();
 * }
 */
class Solution {
    int result;
    public int depthSum(List<NestedInteger> nestedList) {
        result = 0;
        dfs(nestedList, 1);
        return result;
    }
    private void dfs(List<NestedInteger> nestedList, int depth) {
        for (NestedInteger ni : nestedList) {
            if (ni.isInteger()) {
                result += ni.getInteger() * depth;
            } else {
                dfs(ni.getList(), depth + 1);
            }
        }
    }
}
```

Queue BFS

```java
class Solution {
    public int depthSum(List<NestedInteger> nestedList) {
        if(nestedList == null || nestedList.size() == 0) {
            return 0;
        } 
        Queue<NestedInteger> q = new LinkedList<>();
        for(NestedInteger element : nestedList) {
            q.offer(element);
        }
        int depth = 0, sum = 0;
        // 标准BFS遍历
        while(!q.isEmpty()) {
            depth++;
            int size = q.size();
            for(int i = 0; i < size; i++) {
                NestedInteger cur = q.poll();
                if(cur.isInteger()) {
                    sum += depth * cur.getInteger();
                }else {
                    List<NestedInteger> tmp = cur.getList();
                    for(NestedInteger element : tmp) {
                        q.offer(element);
                    }
                }
            }
        }
        return sum;
    }
}
```


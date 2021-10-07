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
    1. 实现Union Find函数  
    2. 循环元素值 并判断**下侧**和**右侧**是否为岛屿  
        如果 是：合并元素值  
        如果 否：不做处理

    3. 将海洋对应的nums数组位置设为-2

```java
class Solution {
    public int numIslands(char[][] grid) {
        //DFS
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }
        int rows = grid.length;
        int cols = grid[0].length;
        
        int[] parent = new int[rows * cols]; // 存储每个连通分量的最终父节点
        
        Arrays.fill(parent, -1); 
        
        for(int i = 0; i < rows;i++){
            for(int j = 0; j < cols; j++){                
                if(grid[i][j] == '1'){
                    parent[i * cols + j]= i * cols + j; // note, that `parent` was filled witn -1 values
                    if(i > 0 && grid[i - 1][j] == '1') { // 与左边比较
                        union(i * cols + j, (i - 1) * cols + j, parent); // union current+top
                    }
                    if(j > 0 && grid[i][j - 1] == '1') { // 与上面比较
                        union(i * cols + j, i * cols + (j - 1), parent); // union current+left
                    }
                }
                
            }
        }
        
        Set<Integer> set = new HashSet<>();
        for(int k = 0; k < parent.length; k++){
            if(parent[k] != -1) {
                set.add(find(k, parent)); // 寻找多少个圈子
            }
        }
        return set.size();
        
    }
    // 以下为并查集的路径压缩代码
    private int find(int p, int[] parent) {
        if (parent[p] == p) {
            return p;
        }
        parent[p] = find(parent[p], parent); // 路径压缩，使find的时间复杂度为O(1)
        return parent[p];
    }
    private void union(int p1, int p2, int[] parent) {
        int f1 = find(p1, parent);
        int f2 = find(p2, parent);
        if (f1 != f2) {
            parent[f1] = f2;
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

## 323 Number of Connected Components in an Undirected Graph

### 题目

You have a graph of `n` nodes. You are given an integer `n` and an array `edges` where `edges[i] = [ai, bi]` indicates that there is an edge between `ai` and `bi` in the graph.

Return _the number of connected components in the graph_.

**Example 1:**![](https://assets.leetcode.com/uploads/2021/03/14/conn1-graph.jpg)

```text
Input: n = 5, edges = [[0,1],[1,2],[3,4]]
Output: 2
```

**Example 2:**![](https://assets.leetcode.com/uploads/2021/03/14/conn2-graph.jpg)

```text
Input: n = 5, edges = [[0,1],[1,2],[2,3],[3,4]]
Output: 1
```

**Constraints:**

* `1 <= n <= 2000`
* `1 <= edges.length <= 5000`
* `edges[i].length == 2`
* `0 <= ai <= bi < n`
* `ai != bi`
* There are no repeated edges.

### 分析

求无向图中的连通分量。跟number of Islands以及Friends Cycle一样，同样也是三种解法：DFS，BFS和Union Find。

### 代码

DFS，对每一块独立的分量进行深搜

```java
class Solution {
    public int countComponents(int n, int[][] edges) {
        int count = 0;
        // 邻接表
        List<List<Integer>> adjList = new ArrayList<>();
        boolean[] visited = new boolean[n];

        for (int i = 0; i < n; i++) {
            adjList.add(new ArrayList<>());
        }
        for (int[] edge : edges) {
            adjList.get(edge[0]).add(edge[1]);
            adjList.get(edge[1]).add(edge[0]);
        }

        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                count++;
                dfs(visited, i, adjList);
            }
        }
        return count;
    }

    private void dfs(boolean[] visited, int index, List<List<Integer>> adjList) {
        visited[index] = true;
        for (int i : adjList.get(index)) {
            if (!visited[i]) {
                dfs(visited, i, adjList);
            }
        }
    }
}
```

BFS

```java
class Solution {
    public int countComponents(int n, int[][] edges) {
        int count = 0;
        //邻接表
        List<List<Integer>> adjList = new ArrayList<>();
        boolean[] visited = new boolean[n];

        for (int i = 0; i < n; i++) {
            adjList.add(new ArrayList<>());
        }
        for (int[] edge : edges) {
            adjList.get(edge[0]).add(edge[1]);
            adjList.get(edge[1]).add(edge[0]);
        }

        for (int i = 0;  i < n; i++) {
            if (!visited[i]) {
                count++;
                Queue<Integer> queue = new LinkedList<>();
                queue.offer(i);
                while (!queue.isEmpty()) {
                    int index = queue.poll();
                    visited[index] = true;
                    for (int next : adjList.get(index)) {
                        if (!visited[next]) queue.offer(next);
                    }
                }
            }
        }
        return count;
    }
}
```

Union Find

```java
class Solution {
    public int countComponents(int n, int[][] edges) {
        int[] parents = new int[n];
        Arrays.fill(parents, -1);

        for (int[] edge : edges) {
            int root1 = find(parents, edge[0]);
            int root2 = find(parents, edge[1]);
            if (root1 != root2) {
                parents[root1] = root2;
                n--;
            }
        }
        return n;
    }

    // 这里并查集只实现find即可，无需union
    private int find(int[] parents, int x) {
        int root = x;
        while (parents[root] != -1) {
            root = parents[root];
        }
        return root;
    }
}
```

## 130 Surrounded Regions

### 题目

Given a 2D board containing `'X'` and `'O'` \(**the letter O**\), capture all regions surrounded by `'X'`.

A region is captured by flipping all `'O'`s into `'X'`s in that surrounded region.

**Example:**

```text
X X X X
X O O X
X X O X
X O X X
```

After running your function, the board should be:

```text
X X X X
X X X X
X X X X
X O X X
```

**Explanation:**

Surrounded regions shouldn’t be on the border, which means that any `'O'` on the border of the board are not flipped to `'X'`. Any `'O'` that is not on the border and it is not connected to an `'O'` on the border will be flipped to `'X'`. Two cells are connected if they are adjacent cells connected horizontally or vertically.

### 分析

题目中解释说被包围的区间不会存在于边界上，所以我们会想到边界上的 O 要特殊处理，只要把边界上的 O 特殊处理了，那么剩下的 O 替换成 X 就可以了。所以只需要记住从四条边出发。

依然是DFS，BFS和Union Find。

### 代码

1\) DFS

```java
class Solution {
    public void solve(char[][] board) {
        if (board == null || board.length == 0) {
            return;
        }
        int m = board.length;
        int n = board[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                // 从边缘o开始搜索
                boolean isEdge = (i == 0 || j == 0 || i == m - 1 || j == n - 1);
                if (isEdge && board[i][j] == 'O') {
                    dfs(board, i, j);
                }
            }
        }

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 'O') {
                    board[i][j] = 'X';
                }
                if (board[i][j] == '#') {
                    board[i][j] = 'O';
                }
            }
        }
    }

    public void dfs(char[][] board, int i, int j) {
        if (i < 0 || j < 0 || i >= board.length  || j >= board[0].length || board[i][j] == 'X' || board[i][j] == '#') {
            // board[i][j] == '#' 说明已经搜索过了. 
            return;
        }
        board[i][j] = '#'; // 原地修改，或者用一个额外的数组
        
        dfs(board, i - 1, j); // 上
        dfs(board, i + 1, j); // 下
        dfs(board, i, j - 1); // 左
        dfs(board, i, j + 1); // 右
    }
}
```

DFS用stack锻炼下，不太推荐

```java
class Solution {
    public class Pos{
        int i;
        int j;
        Pos(int i, int j) {
            this.i = i;
            this.j = j;
        }
    }
    public void solve(char[][] board) {
        if (board == null || board.length == 0) return;
        int m = board.length;
        int n = board[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                // 从边缘第一个是o的开始搜索
                boolean isEdge = i == 0 || j == 0 || i == m - 1 || j == n - 1;
                if (isEdge && board[i][j] == 'O') {
                    dfs(board, i, j);
                }
            }
        }

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 'O') {
                    board[i][j] = 'X';
                }
                if (board[i][j] == '#') {
                    board[i][j] = 'O';
                }
            }
        }
    }

    public void dfs(char[][] board, int i, int j) {
        Stack<Pos> stack = new Stack<>();
        stack.push(new Pos(i, j));
        board[i][j] = '#';
        while (!stack.isEmpty()) {
            // 取出当前stack 顶, 不弹出.
            Pos current = stack.peek();
            // 上
            if (current.i - 1 >= 0 
                && board[current.i - 1][current.j] == 'O') {
                stack.push(new Pos(current.i - 1, current.j));
                board[current.i - 1][current.j] = '#';
              continue;
            }
            // 下
            if (current.i + 1 <= board.length - 1 
                && board[current.i + 1][current.j] == 'O') {
                stack.push(new Pos(current.i + 1, current.j));
                board[current.i + 1][current.j] = '#';      
                continue;
            }
            // 左
            if (current.j - 1 >= 0 
                && board[current.i][current.j - 1] == 'O') {
                stack.push(new Pos(current.i, current.j - 1));
                board[current.i][current.j - 1] = '#';
                continue;
            }
            // 右
            if (current.j + 1 <= board[0].length - 1 
                && board[current.i][current.j + 1] == 'O') {
                stack.push(new Pos(current.i, current.j + 1));
                board[current.i][current.j + 1] = '#';
                continue;
            }
            // 如果上下左右都搜索不到,本次搜索结束，弹出stack
            stack.pop();
        }
    }
}
```

2\) BFS

```java
class Solution {
    int m = 0;  // 行数
    int n = 0;  // 列数
    // 用作控制4个方向
    int[] dx = {1, -1, 0, 0};
    int[] dy = {0, 0, 1, -1};
    public void solve(char[][] board) {
        // java二维矩阵的行数和列数可能不同，并且每一行的列数可能都不一样
        // 但是肯定是先后行再有列，而且根据题目要求，在这里每一行的列数都是相同的
        m = board.length;       //  行数m
        if (m == 0) {   // 行数为0，board为空
            return;
        }
        n = board[0].length;    // 列数
        Queue<int[]> queue = new LinkedList<int[]>();
        // 搜索上下边界，先标记后入队
        for (int i = 0; i < n; i++) {
            if (board[0][i] == 'O') {   //  第一行
                board[0][i] = 'M';
                queue.offer(new int[]{0, i});
            }
            if (board[m - 1][i] == 'O') {      // 最后一行
                board[m - 1][i] = 'M';
                queue.offer(new int[]{m - 1, i});
            }
        }
        // 搜索左右边界，先标记后入队
        for (int i = 1; i < m - 1; i++) {
            if (board[i][0] == 'O') {   // 第一列
                board[i][0] = 'M';
                queue.offer(new int[]{i, 0});
            }
            if (board[i][n - 1] == 'O') {   // 最后一列
                board[i][n - 1] = 'M';
                queue.offer(new int[]{i, n - 1});
            }
        }
        // 广度优先搜索
        while (!queue.isEmpty()) {
            int[] temp = queue.poll();
            int x = temp[0], y = temp[1];   // 获取行和列
            for (int i = 0; i < 4; i++) {   // 搜索四个方向
                int mx = x + dx[i], my = y + dy[i]; 
                if (mx < 0 || mx >= m || my < 0 || my >= n) {     // 越界
                    continue;
                } else if (board[mx][my] == 'O') {  // 搜索到了'O'，标记后入队
                    board[mx][my] = 'M';
                    queue.offer(new int[]{mx, my});
                }
            }
        }
        // 标记完，再次遍历，现在矩阵中可能有3中字符，'X','M','O'
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 'X') {
                    continue;
                } else if (board[i][j] == 'O'){ // 出现没被标记过的'O'，应该被填充
                    board[i][j] = 'X';
                } else if (board[i][j] == 'M'){ // 被标记过的，不应该被填充，恢复
                    board[i][j] = 'O';
                }
            }
        }
    }
}
```

3\) Union Find，dummy是一个虚拟节点，所有不需要覆盖的都和它相连，最后再遍历一遍，没有和dummy相连的就置为'X'。

```java
class Solution {
    public void solve(char[][] board) {
        int row = board.length;
        if (row == 0) {
            return;
        }
        int col = board[0].length;
        int dummy = row * col;
        int[][] dirs = new int[][]{{1, 0}, {0, 1}, {0, -1}, {-1, 0}};
        UnionFind uf = new UnionFind(dummy);

        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                if (board[i][j] == 'O') {
                    if (i == 0 || i == row - 1 || j == 0 || j == col - 1) {
                        uf.union(i * col + j, dummy);
                    } else {
                        for (int k = 0; k < 4; k++) {
                            int x = i + dirs[k][0];
                            int y = j + dirs[k][1];
                            if (board[x][y] == 'O') {
                                uf.union(x * col + y, i * col + j);
                            }
                        }
                    }
                }
            }
        }
        for (int i = 1; i < row - 1; i++) {
            for (int j = 1; j < col - 1; j++) {
                if (!uf.connect(i * col + j, dummy)) {
                    board[i][j] = 'X';
                }
            }
        }
    }
}

// 以下是并查集的代码
class UnionFind {
    private int[] parent;

    public UnionFind(int dummy) {
        parent = new int[dummy + 1];
        for (int i = 0; i <= dummy; i++) {
            parent[i] = i;
        }
    }

    public void union(int x, int y) {
        int root_x = find(x);
        int root_y = find(y);
        if (root_x != root_y) {
            parent[root_x] = root_y;
        }
    }

    public int find(int x) {
        while (x != parent[x]) {
            parent[x] = parent[parent[x]];
            x = parent[x];
        }
        return x;
    }

    public boolean connect(int x, int y) {
        return find(x) == find(y);
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
        return isValidBST(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }
    private boolean isValidBST(TreeNode node, long min, long max) {
        if (node == null) {
            return true;
        }
        if (node.val <= min || node.val >= max) {
            return false;
        }
        return isValidBST(node.left, min, node.val) && isValidBST(node.right, node.val, max);
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

## 473 Matchsticks to Square

### 原题

Remember the story of Little Match Girl? By now, you know exactly what matchsticks the little match girl has, please find out a way you can make one square by using up all those matchsticks. You should not break any stick, but you can link them up, and each matchstick must be used **exactly** one time.

Your input will be several matchsticks the girl has, represented with their stick length. Your output will either be true or false, to represent whether you could make one square using all the matchsticks the little match girl has.

**Example 1:**  


```text
Input: [1,1,2,2,2]
Output: true

Explanation: You can form a square with length 2, one side of the square came two sticks with length 1.
```

**Example 2:**  


```text
Input: [3,3,3,3,4]
Output: false

Explanation: You cannot find a way to form a square with all the matchsticks.
```

**Note:**  


1. The length sum of the given matchsticks is in the range of `0` to `10^9`.
2. The length of the given matchstick array will not exceed `15`.

### 分析

找出能否使用所有火柴拼成一个正方形的方法。不能折断火柴，可以把火柴连接起来，并且每根火柴都要用到。

1\) DFS，对于所有的火柴，我们需要考虑：

* 将它们分成四组，每一根火柴恰好属于其中的一组；
* 每一组火柴的长度之和都相同，等于所有火柴长度之和的四分之一。

使用DFS枚举出所有的分组情况，并对于每一种情况，判断是否满足上述的两个条件。

具体做法是依次对每一根火柴进行搜索，当搜索到第 i 根火柴时，可以把它放到四组中的任意一种。对于每一种放置方法，我们继续对第 i + 1 根火柴进行递归。当搜索完全部的 N 根火柴后，再判断每一组火柴的长度之和是否都相同。

时间复杂度：O\(4^N\)，每根火柴有四种放法。

空间复杂度：O\(N\)O\(N\)。

2\) DP

### 代码

朴素DFS

```java
class Solution {
    public boolean makesquare(int[] nums) {
        if (null == nums || nums.length < 4) {
            return false;
        }
        int perimeter = 0;
        for (int num : nums) {
            perimeter += num;
        }
        if (perimeter % 4 != 0) {
            return false;
        }

        return dfs(nums, 0, 0, 0, 0, 0, perimeter / 4);
    }

    /**
     * 
     * @param nums
     * @param index 表示当前放置到了第几个元素
     * @param a|b|c|d表示四个边长
     * @param side 表示最终的边长
     */
    private boolean dfs(int[] nums, int index, int a, int b, int c, int d, int side) {
        if (index == nums.length) {
            return a == side && b == side && c == side && d == side;
        }
        
        if (a > side || b > side || c > side || d > side) {
            // 只要有一个边大于边长，则不用进行下一步放置了
            return false;
        }

        // 每根火柴都有四种放置地点；分别将index位置的火柴放到a b c d四个位置，检查是否成功
        return dfs(nums, index + 1, a + nums[index], b, c, d, side)
            || dfs(nums, index + 1, a, b + nums[index], c, d, side)
            || dfs(nums, index + 1, a, b, c + nums[index], d, side)
            || dfs(nums, index + 1, a, b, c, d + nums[index], side);
    }
}

```

剪枝

```java
class Solution {
    public boolean makesquare(int[] nums) {
        if (nums == null || nums.length < 4) {
            return false;
        }
        int perimeter = 0;
        for (int num : nums) {
            perimeter += num;
        }

        if (perimeter % 4 != 0) {
            return false;
        }

        
        Arrays.sort(nums);
        // 倒序的话为了剪枝，先排大的，最后再塞小的
        reverse(nums); // Collections.reverseorder() 和重写comprator都是对non-primitive的

        return dfs(nums, new int[4], 0, perimeter / 4); //合并一下四条边
    }

    private boolean dfs(int[] nums, int[] sides, int index, int target) {
        if (index == nums.length) {
            if (sides[0] == target && sides[1] == target && sides[2] == target) { // 只检查三条边即可
                return true;
            }
            return false;
        }
        for (int i = 0; i < 4; i++) {
            if (sides[i] + nums[index] > target) { // 当前边不考虑，剪枝
                continue;
            }
            sides[i] += nums[index];
            if (dfs(nums, sides, index+1, target)) {
                return true;
            }
            sides[i] -= nums[index]; // 回溯
        }
        return false;
    }

    private void reverse(int[] nums) {
        int i = 0, j = nums.length - 1;
        while (i < j) {
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
            i++;
            j--;
        }
    }
}

```

## 37 Sudoku Solver

Write a program to solve a Sudoku puzzle by filling the empty cells.

A sudoku solution must satisfy **all of the following rules**:

1. Each of the digits `1-9` must occur exactly once in each row.
2. Each of the digits `1-9` must occur exactly once in each column.
3. Each of the digits `1-9` must occur exactly once in each of the 9 `3x3` sub-boxes of the grid.

The `'.'` character indicates empty cells.

**Example 1:**![](https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png)

```text
Input: board = [["5","3",".",".","7",".",".",".","."],["6",".",".","1","9","5",".",".","."],[".","9","8",".",".",".",".","6","."],["8",".",".",".","6",".",".",".","3"],["4",".",".","8",".","3",".",".","1"],["7",".",".",".","2",".",".",".","6"],[".","6",".",".",".",".","2","8","."],[".",".",".","4","1","9",".",".","5"],[".",".",".",".","8",".",".","7","9"]]
Output: [["5","3","4","6","7","8","9","1","2"],["6","7","2","1","9","5","3","4","8"],["1","9","8","3","4","2","5","6","7"],["8","5","9","7","6","1","4","2","3"],["4","2","6","8","5","3","7","9","1"],["7","1","3","9","2","4","8","5","6"],["9","6","1","5","3","7","2","8","4"],["2","8","7","4","1","9","6","3","5"],["3","4","5","2","8","6","1","7","9"]]
Explanation: The input board is shown above and the only valid solution is shown below:


```

**Constraints:**

* `board.length == 9`
* `board[i].length == 9`
* `board[i][j]` is a digit or `'.'`.
* It is **guaranteed** that the input board has only one solution.

### 代码

DFS + 剪枝

```java
class Solution {
    public void solveSudoku(char[][] board) {
        if (board == null || board.length == 0 || board[0].length == 0) {
            return;
        }
        backtrack(board, 0, 0);
    }
    private boolean backtrack(char[][] board, int i, int j) {
        int m = 9, n = 9;
        if (j == n) {
            // 穷举到最后一列的话就换到下一行重新开始。
            return backtrack(board, i + 1, 0);
        }
        if (i == m) {
            // 找到一个可行解，触发 base case
            return true;
        }

        if (board[i][j] != '.') {
            // 如果有预设数字，不用我们穷举
            return backtrack(board, i, j + 1);
        } 

        for (char ch = '1'; ch <= '9'; ch++) {
            // 如果遇到不合法的数字，就跳过
            if (!isValid(board, i, j, ch))
                continue;

            board[i][j] = ch;
            // 如果找到一个可行解，立即结束
            if (backtrack(board, i, j + 1)) {
                return true;
            }
            board[i][j] = '.';
        }
        // 穷举完 1~9，依然没有找到可行解，此路不通
        return false;
    }

    private boolean isValid(char[][] board, int r, int c, char n) {
        for (int i = 0; i < 9; i++) {
            // 判断行是否存在重复
            if (board[r][i] == n) return false;
            // 判断列是否存在重复
            if (board[i][c] == n) return false;
            // 判断 3 x 3 方框是否存在重复
            if (board[(r/3)*3 + i/3][(c/3)*3 + i%3] == n)
                return false;
        }
        return true;
    }
}
```

用三个数据结构来记录行列和小框

```java
class Solution {
  public boolean isValidSudoku(char[][] board) {
    // init data
    HashMap<Integer, Integer> [] rows = new HashMap[9];
    HashMap<Integer, Integer> [] columns = new HashMap[9];
    HashMap<Integer, Integer> [] boxes = new HashMap[9];
    for (int i = 0; i < 9; i++) {
      rows[i] = new HashMap<Integer, Integer>();
      columns[i] = new HashMap<Integer, Integer>();
      boxes[i] = new HashMap<Integer, Integer>();
    }

    // validate a board
    for (int i = 0; i < 9; i++) {
      for (int j = 0; j < 9; j++) {
        char num = board[i][j];
        if (num != '.') {
          int n = (int)num;
          int box_index = (i / 3 ) * 3 + j / 3;

          // keep the current cell value
          rows[i].put(n, rows[i].getOrDefault(n, 0) + 1);
          columns[j].put(n, columns[j].getOrDefault(n, 0) + 1);
          boxes[box_index].put(n, boxes[box_index].getOrDefault(n, 0) + 1);

          // check if this value has been already seen before
          if (rows[i].get(n) > 1 || columns[j].get(n) > 1 || boxes[box_index].get(n) > 1)
            return false;
        }
      }
    }

    return true;
  }
}
```


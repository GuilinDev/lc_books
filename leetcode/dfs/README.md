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

BFS，这里用了额外的一个2D数组来存储是否已经访问过，如果改变原数组的值，则可以不要visited数组。

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

找二维数组中最大的岛的面积，岛为1，这个几乎就是上一道题的翻版，也即是典型DFS的实现。

### 代码

```java
class Solution {
   public int maxAreaOfIsland(int[][] grid) {
      int maxArea = 0;
      for (int i = 0; i < grid.length; i++) {
         for (int j = 0; j < grid[0].length; j++) {
            if (grid[i][j] == 1) {
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


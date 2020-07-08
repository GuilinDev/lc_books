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

DFS典型题，找到'1'后，累加一下结果，然后上下左右四个方向DFS修改'1'成别的值，以免重复计算。

### 代码

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


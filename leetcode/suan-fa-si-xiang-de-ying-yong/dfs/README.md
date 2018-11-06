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



### 代码

```java
class Solution {
    public int numIslands(char[][] grid) {
        int result =0;
        if (grid == null || grid.length == 0 || grid[0] == null || grid[0].length == 0) {
            return result;
        }
        int m = grid.length;
        int n = grid[0].length;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    dfs(grid, i, j, m, n);
                    result++;
                }
            }
        }
        return result;
    }

    private void dfs (char[][] grid, int i, int j, int m, int n) {
        if (i < 0 || j < 0 || i >= m || j >= n || grid[i][j] != '1') {
            return;
        }
        grid[i][j] = '0';//每次找到一块陆地就变为‘0’。以防重复查找
        dfs(grid, i + 1, j, m, n); //找右边
        dfs(grid, i - 1, j, m, n); //找左边
        dfs(grid, i, j + 1, m, n); //找下边
        dfs(grid, i, j - 1, m, n); // 找上边
    }
}
```




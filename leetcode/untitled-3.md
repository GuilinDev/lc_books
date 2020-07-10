---
description: 一般包括二叉树的BFS，Graph的BFS
---

# BFS

## 301 Remove Invalid Parentheses

### 原题概述

Remove the minimum number of invalid parentheses in order to make the input string valid. Return all possible results.

**Note:** The input string may contain letters other than the parentheses `(` and `)`.

**Example 1:**

```text
Input: "()())()"
Output: ["()()()", "(())()"]
```

**Example 2:**

```text
Input: "(a)())()"
Output: ["(a)()()", "(a())()"]
```

**Example 3:**

```text
Input: ")("
Output: [""]
```

### 题意和分析

移除最少的括号使得给定字符串为一个合法的含有括号的字符串，也就是字符串中的左右括号数应该相同，而且每个右括号的左边一定有其对应的左括号，需要找出所有合法的取法。用BFS或者DFS来解：

1）BFS，参考[这里](https://leetcode.com/problems/remove-invalid-parentheses/discuss/75032/Share-my-Java-BFS-solution)，对于字符串s，通过移除一个括号产生所有的状态，检查移除后是否还valid，如果检查到了valid，那就把该状态加入到结果集中，完成；否则把状态加入到queue中并移除两个再检查，以此类推。这样BFS的好处是被移除的括号总是最少的，并且没有递归调用。时间复杂度O\(n\)，空间复杂度O\(n^2\)；

2）DFS，我们知道如何使用stack检查括号中的字符串是否有效，或更简单的使用counter。 counter在遇到"\("时将增加，在遇到“\)"时将减少。每当计数器为负数时，前缀中的'）'就会大于"\("。

为了使prefix有效，我们需要删除“\)”。问题是删除哪个？答案是prefix中的任何一个。但是，如果删除任何一个，将生成重复的结果，例如：s =\(\)\)，我们可以删除s \[1\]或s \[2\]，但结果是相同的"\(\)"。因此，我们限制只删除一系列连续的）中的第一个"\)"。

删除第一个"\)"后，prefix变得valid。然后，我们递归调用该函数以解决字符串的其余部分。但是，我们需要保留其他信息："\)"上一个移除位置。如果没有这个信息，我们通过上面步骤（仅以不同的顺序）删除两个“\)”，就会产生重复项。 为此，我们会跟踪上一个移除"\)"的位置，然后移除现在需移除的“\)”。

那"\("该怎么处理呢呢？如果s ="\(\(\)\(\(\(\)\)"，我们需要删除'\('呢？ 答案是：从右到左遍历做同样的事情，当然也可以reverse字符串然后重用上面的代码。

### 代码

BFS

```java
class Solution {
    public List<String> removeInvalidParentheses(String s) {
        List<String> result = new ArrayList<>();
        if (s == null) {
            return result;
        }

        Set<String> visited = new HashSet<>();
        Queue<String> queue = new LinkedList<>();//存储状态

        //初始化
        visited.add(s);
        queue.add(s);

        boolean found = false;//在某个level是否发现valid的状态

        while (!queue.isEmpty()) {
            s = queue.poll();

            if (isValid(s)) {
                //找到一个答案，加入到结果集
                result.add(s);
                found = true;
            }

            if (found) {//当前状态已加入结果集，跳出当前循环，检查下一个状态
                continue;
            }

            //产生所有的状态
            for (int i = 0; i < s.length(); i++) {
                //只移除左括号或右括号，字母什么的不移除
                if (s.charAt(i) != '(' && s.charAt(i) != ')') {
                    continue;
                }
                //产生一个临时状态
                String temp = s.substring(0, i) + s.substring(i + 1);//为什么这里i + 1不会越界？
                if (!visited.contains(temp)) {
                    queue.add(temp);
                    visited.add(temp);
                }
            }
        }

        return result;
    }

    //检查当前状态是否valid，也可以用stack来做，不过需要额外空间
    private boolean isValid(String s) {
        int count = 0;
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '(') {
                count++;
            }
            //遇到右括号，检查之前是否有左括号，并递减
            if (c == ')' && count-- == 0) {
                return false;
            }
        }
        return count == 0;
    }
}
```

DFS

```java
class Solution {
    public static List<String> removeInvalidParentheses(String s) {
        List<String> result = new ArrayList<>();
        char[] check = new char[]{'(', ')'};
        dfs(s, result, check, 0, 0);
        return result;
    }

    public static void dfs(String s, List<String> result, char[] check, int last_i, int last_j) {
        int count = 0; // 从左到右记录左括号
        int i = last_i;
        while (i < s.length() && count >= 0) {

            if (s.charAt(i) == check[0]) count++; // 左括号，累加
            if (s.charAt(i) == check[1]) count--; // 右括号，累减
            i++;
        }

        if (count >= 0)  { // 到这里没有额外的')'，现在通过翻转字符串开始检查有没有额外的'('  - 翻转过来复用代码
            String reversed = new StringBuffer(s).reverse().toString();
            if (check[0] == '(') {
                dfs(reversed, result, new char[]{')', '('}, 0, 0);
            } else {
                result.add(reversed);
            } 

        } else {  // 有额外的 ')'，进行处理
            i -= 1; // 'i-1'是多出来的')' 让count < 0的地方
            for (int j = last_j; j<= i; j++) {
                if (s.charAt(j) == check[1] && (j == last_j || s.charAt(j-1) != check[1])) {
                    dfs(s.substring(0, j) + s.substring(j + 1, s.length()), result, check, i, j);
                }
            }
        }
    }
}
```

## 994 Rotting Oranges

### 题目

In a given grid, each cell can have one of three values:

* the value `0` representing an empty cell;
* the value `1` representing a fresh orange;
* the value `2` representing a rotten orange.

Every minute, any fresh orange that is adjacent \(4-directionally\) to a rotten orange becomes rotten.

Return the minimum number of minutes that must elapse until no cell has a fresh orange.  If this is impossible, return `-1` instead.

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/02/16/oranges.png)

```text
Input: [[2,1,1],[1,1,0],[0,1,1]]
Output: 4
```

**Example 2:**

```text
Input: [[2,1,1],[0,1,1],[1,0,1]]
Output: -1
Explanation:  The orange in the bottom left corner (row 2, column 0) is never rotten, because rotting only happens 4-directionally.
```

**Example 3:**

```text
Input: [[0,2]]
Output: 0
Explanation:  Since there are already no fresh oranges at minute 0, the answer is just 0.
```

**Note:**

1. `1 <= grid.length <= 10`
2. `1 <= grid[0].length <= 10`
3. `grid[i][j]` is only `0`, `1`, or `2`.

### 分析

这道题计算烂番茄对周围的影响的层数（天数），因为是一层层往外扩散，所以要想到可以用BFS来做，BFS的写法比较固定，使用Queue，首先统计所有烂橘子的坐标放入到队列中，同时计算好橘子的个数；然后将每个烂橘子的坐标取出，将其周围四个方向的好橘子转变为烂橘子，同时将新产生的烂橘子坐标放入queue中，每次从queue中取出一个烂橘子的坐标就代表过了一分钟，如此一直到queue中为空。

### 代码

```java
class Solution {
    public int orangesRotting(int[][] grid) {
        if (grid == null || grid.length == 0) {
            return 0;
        }
        int rows = grid.length;
        int cols = grid[0].length;

        int layers = 0;
        int fresh = 0;
        Queue<int[]> queue = new LinkedList<>();

        // 统计所有烂番茄的坐标 - 接下来会用到这个坐标进行BFS，并计算新鲜番茄的个数
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 2) {
                    //将烂番茄的坐标存入到队列中
                    queue.offer(new int[]{i, j});
                } else if (grid[i][j] == 1) {
                    fresh++;
                }
            }
        }

        if (fresh == 0) {
            return 0;
        }

        //根据每个烂番茄的坐标进行BFS
        int[][] dirs = {{1, 0},{-1, 0},{0, 1},{0, -1}};//上左右下四个方向
        while(!queue.isEmpty()) {
            layers++; //往外扩散一层
            int size = queue.size(); //BFS将烂番茄的坐标去除并计算四周
            for (int i = 0; i < size; i++) {
                int[] position = queue.poll();
                for (int[] dir : dirs) { // 遍历四个方向
                    int nearby_x = position[0] + dir[0];
                    int nearby_y = position[1] + dir[1];

                    if (nearby_x < 0 || nearby_x >= rows || nearby_y < 0 || nearby_y >= cols || //越界
                        grid[nearby_x][nearby_y] == 2 || //往外扩散的时候，本身就是烂番茄
                        grid[nearby_x][nearby_y] == 0) { //空格子
                            continue;
                        }
                    grid[nearby_x][nearby_y] = 2; //将新鲜番茄改成烂番茄

                    queue.offer(new int[]{nearby_x, nearby_y});//将新产生的烂番茄放入队列中

                    fresh--; //新鲜番茄减少
                }
            }
        }

        return fresh == 0 ? layers - 1 : -1; // layers - 1是因为比如共两层但只扩散了一天
    }
}
```


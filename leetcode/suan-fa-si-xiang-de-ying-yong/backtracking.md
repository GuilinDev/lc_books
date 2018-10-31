# Backtracking

## 37 Sudoku Solver

### 原题概述

Write a program to solve a Sudoku puzzle by filling the empty cells.

A sudoku solution must satisfy **all of the following rules**:

1. Each of the digits `1-9` must occur exactly once in each row.
2. Each of the digits `1-9` must occur exactly once in each column.
3. Each of the the digits `1-9` must occur exactly once in each of the 9 `3x3` sub-boxes of the grid.

Empty cells are indicated by the character `'.'`.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png)  
A sudoku puzzle...

![](https://upload.wikimedia.org/wikipedia/commons/thumb/3/31/Sudoku-by-L2G-20050714_solution.svg/250px-Sudoku-by-L2G-20050714_solution.svg.png)  
...and its solution numbers marked in red.

**Note:**

* The given board contain only digits `1-9` and the character `'.'`.
* You may assume that the given Sudoku puzzle will have a single unique solution.
* The given board size is always `9x9`.

### 题意和分析

这道题要求把数独解出来，是回溯法backtracking的应用。关于回溯法，参考了discuss区的[讨论](https://leetcode.com/problems/sudoku-solver/discuss/15752/Straight-Forward-Java-Solution-Using-Backtracking)，并在在网上看到这样的总结：

1 **每个backtracking的题目，最好都有独立判断isValid的程序**，这样架构清楚。同时，valid判断函数在这里可以稍微研究一下。只要当前要判断的位置上的数值和本行没有重复，本列没有重复，cube没有重复就是valid。一旦有重复就立即返回，减少判断次数。

2 **backtracking的递归函数，通常要有返回值呢**。因为要判断递归的方案正确与否，所以这里的递归一定是有返回值的（除非是combination那种没有正确错误概念的backtracking）。

3 可以考虑“先放置，再判断”的方案。比如这里，首先判断当前位置是否为空，如果为空，那么放置一个元素，检查它是否正确。如果正确，就继续进行下面的递归（也就是第29行 isValid&&solveSudoku的作用）。当函数返回错误之后，将刚刚的数值变为空，再进行下一次尝试即可。

4 所有的方案（k从1到9）完毕之后，应该返回错误，这个是不应该被忽略的。

5 最后一点需要注意的是，当i,j循环完毕之后，第27行应该返回true。这里实际上是最终/最底层的一次循环，表明已经解出了sudoku，返回true！切记切记，最终情况！

### 代码

```java
class Solution {
    public void solveSudoku(char[][] board) {
        if (board == null || board.length == 0 || board[0].length == 0) {
            return;
        }
        solve(board);
    }

    private boolean solve(char[][] board) {
        for (int i =0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if (board[i][j] == '.') {//针对空的位置
                    for (char c = '1'; c <= '9'; c++) {//从1到9开始试，只要valid就保留
                        if (isValid(board, i, j, c)) {//传入当前board，行，列和准备加入的数字看是否有效
                            board[i][j] = c;//如果so far是有效的，将数字放入当前cell                                 
                            if (solve(board)) {//递归调用，注意backtracking的递归是有返回值的
                                return true;
                            } else {//回溯过程中发现无效，就让当前cell重新变成default
                                board[i][j] = '.';
                            }
                        }
                    }
                    return false;//中间某一条路走不通
                }
            }
        }
        return true;//这是回溯法里面递归的返回值,最后一层/一次递归
    }

    //判断新加的数字是否有效，返回值为boolean
    private boolean isValid(char[][] board, int row, int col, char c) {
        for (int i =0; i < 9; i++) {
            //检查行
            if (board[i][col] != '.' && board[i][col] == c) {
                return false;
            }
            //检查列
            if (board[row][i] != '.' && board[row][i] == c) {
                return false;
            }
            //检查cube
            int rowIndex = 3 * (row / 3);
            int colIndex = 3 * (col / 3);
            if (board[rowIndex + i / 3][colIndex + i % 3] != '.' && board[rowIndex + i / 3][colIndex + i % 3] == c) {
                return false;
            }
        }
        return true;
    }
}
```

## 46 Permutations

### 原题概述

Given a collection of **distinct** integers, return all possible permutations.

**Example:**

```text
Input: [1,2,3]
Output:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

### 题意和分析

给一个distinct数字元素的集合，返回所有可能的组合，这道题依然用Backtracking的模板来做，注意这不是最快的办法。

### 代码

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        if (nums == null || nums.length == 0) {
            return result;
        }
        //Arrays.sort(nums);//因为没有重复元素，所以这道题不用排序
        backtrack(result, nums, new ArrayList<Integer>());
        return result;
    }
    private void backtrack(List<List<Integer>> result, int[] nums, List<Integer> oneList) {
        if (oneList.size() == nums.length) {
            result.add(new ArrayList<>(oneList));
        } else {
            for (int i = 0; i < nums.length; i++) {
                if (!oneList.contains(nums[i])) {//有重复元素则跳过此次循环
                    oneList.add(nums[i]);
                    backtrack(result, nums, oneList);
                    oneList.remove(oneList.size() - 1);
                }
            }
        }
    }
}
```

## 47 Permutations II

### 原题概述

Given a collection of numbers that might contain duplicates, return all possible unique permutations.

**Example:**

```text
Input: [1,1,2]
Output:
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]
```

### 题意和分析

跟第46题相比较，这道题多了一个条件就是有重复数字，题目要求是unique的组合，多了一个步骤就是在结果中去重，总体解法是类似的。

### 代码

```java
class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        if (nums == null || nums.length == 0) {
            return result;
        }
        Arrays.sort(nums);//排序的作用是防止利用重复相等的数字
        backtrack(result, new ArrayList<>(), nums, new boolean[nums.length]);//用一个一维数组来记录元素是否被用过
        return result;
    }
    private void backtrack(List<List<Integer>> result, List<Integer> oneList, int[] nums, boolean[] used) {
        if (oneList.size() == nums.length) {
            result.add(new ArrayList<>(oneList));
        } else {
            for (int i = 0; i < nums.length; i++) {
                if (used[i] //被用过
                        || (i > 0 && nums[i] == nums[i - 1] && !used[i - 1])) {//有重复数字的情况
                    continue;
                }
                used[i] = true;
                oneList.add(nums[i]);
                backtrack(result, oneList, nums, used);
                used[i] = false;
                oneList.remove(oneList.size() - 1);
            }
        }
    }
}
```

## **78** Subse**ts** 

### **原题概述**

Given a set of **distinct** integers, _nums_, return all possible subsets \(the power set\).

**Note:** The solution set must not contain duplicate subsets.

**Example:**

```text
Input: nums = [1,2,3]
Output:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

### **题意和分析**

同样一道回溯法的题目，解法和分析一模一样。

### **代码**

```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(nums);//需要排序，这样才知道哪些数字已经用过
        backtrack(result, new ArrayList<>(), nums, 0);//初始传入0作为起始点
        return result;
    }
    private void backtrack(List<List<Integer>> result, List<Integer> tempList, int[] nums, int start) {
        result.add(new ArrayList<>(tempList));//注意添加的方式，是新建一个ArrayList对象
        for (int i = start; i < nums.length; i++) {
            tempList.add(nums[i]);
            backtrack(result, tempList, nums, i + 1);
            tempList.remove(tempList.size() - 1);//每一轮需要清空
        }
    }
}
```


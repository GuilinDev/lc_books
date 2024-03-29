# Backtracking

## **79 Word Search** 

### **原题概述**

Given a 2D board and a word, find if the word exists in the grid.

The word can be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once.

**Example:**

```text
board =
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]

Given word = "ABCCED", return true.
Given word = "SEE", return true.
Given word = "ABCB", return false.
```

### **题意和分析**

这道题是比较明显的深搜

递归，回溯和DFS的区别

> 递归是一种算法结构，回溯是一种算法思想 
>
> 一个递归就是在函数中调用函数本身来解决问题 回溯就是通过不同的尝试来生成问题的解，有点类似于穷举，但是和穷举不同的是回溯会“剪枝”，意思就是对已经知道错误的结果没必要再枚举接下来的答案了，比如一个有序数列1,2,3,4,5，我要找和为5的所有集合，从前往后搜索我选了1，然后2，然后选3 的时候发现和已经大于预期，那么4,5肯定也不行，这就是一种对搜索过程的优化。
>
> 深度优先搜索（DFS）对于某一种数据结构来说，一般是树（搜索树是起记录路径和状态判断的作用），对于回溯和DFS，其主要的区别是，回溯法在求解过程中不保留完整的树结构，而深度优先搜索则记下完整的搜索树。
>
> 为了减少存储空间，在深度优先搜索中，用标志的方法记录访问过的状态，这种处理方法使得深度优先搜索法与回溯法没什么区别了。

如同上面的比较，DFS有两种经典的做法，一是用跟原本二维数组同等大小的数组来记录是否visited过，其中元素为boolean， 如果二维数组board的当前字符和目标字符串word对应的字符相等，则对其上下左右四个邻字符分别调用DFS的递归函数，只要有一个返回true，那么就表示可以找到对应的字符串，否则就不能找到；第二是对第一种做法空间上的优化，每次用一个char来记录当前二维数组里面的char，在递归调用前用一个特殊的字符，比如‘\#’，来代替当前字符说明已经检查过了，然后再递归调用后再改回来方便下次检查。

### **代码**

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        if (board == null || board.length == 0 || board[0].length == 0) {
            return false;
        }
        int m = board.length, n = board[0].length;
        boolean[][] visited = new boolean[m][n];
        
        // optional
        for (int i = 0; i < rows; i++) {
            Arrays.fill(visited[i], false);
        }
        
        int index = 0;//字符串的索引
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (dfs(board, word, index, i, j, visited)) {
                    return true;
                }
            }
        }
        return false;
    }

    private boolean dfs(char[][] board, String word, int index, int i, int j, boolean[][] visited) {
        if (index == word.length()) {//找完了
            return true;
        }
        int m = board.length, n = board[0].length;
        if (i < 0 || j < 0 || i >= m || j >= n
                || visited[i][j] //已被访问过
                || board[i][j] != word.charAt(index)) {//两个字符不相等
            return false;
        }
        visited[i][j] = true;//设定当前字符已被访问过
        boolean result = (dfs(board, word, index + 1, i - 1, j, visited)//左
                || dfs(board, word, index + 1, i + 1, j, visited)//右
                || dfs(board, word, index + 1, i, j - 1, visited)//上
                || dfs(board, word, index + 1, i, j + 1, visited));//下
        visited[i][j] = false;//这步不要忘记，让“当前的”位置归为初始值，为别的路径的查找准备
        return result;
    }
}
```

优化空间

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        if (board == null || board.length == 0 || board[0].length == 0) {
            return false;
        }
        int m = board.length, n = board[0].length;
        int index = 0;//字符串的索引
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (dfs(board, word, index, i, j)) {
                    return true;
                }
            }
        }
        return false;
    }

    private boolean dfs(char[][] board, String word, int index, int i, int j) {
        if (index == word.length()) {//找完了
            return true;
        }
        int m = board.length, n = board[0].length;
        if (i < 0 || j < 0 || i >= m || j >= n
                || board[i][j] != word.charAt(index)) {//两个字符不相等
            return false;
        }
        char temp = board[i][j];//临时存一下当前的字符,在当前DFS递归树的路径上
        board[i][j] = '#';
        boolean result = (dfs(board, word, index + 1, i - 1, j)//左
                || dfs(board, word, index + 1, i + 1, j)//右
                || dfs(board, word, index + 1, i, j - 1)//上
                || dfs(board, word, index + 1, i, j + 1));//下
        board[i][j] = temp;//这步不要忘记，让“当前的”位置归为初始值，为别的路径的查找准备
        return result;
    }
}
```

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

## **39 Combination Sum** 

### **原题概述**

Given a **set** of candidate numbers \(`candidates`\) **\(without duplicates\)** and a target number \(`target`\), find all unique combinations in `candidates` where the candidate numbers sums to `target`.

The **same** repeated number may be chosen from `candidates` unlimited number of times.

**Note:**

* All numbers \(including `target`\) will be positive integers.
* The solution set must not contain duplicate combinations.

**Example 1:**

```text
Input: candidates = [2,3,6,7], target = 7,
A solution set is:
[
  [7],
  [2,2,3]
]
```

**Example 2:**

```text
Input: candidates = [2,3,5], target = 8,
A solution set is:
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]
```

### **题意和分析**

给一个没有重复元素的数组，找出里面的元素加起来等于target的所有组合，原数组的元素可以利用多次。这种求所有组合的情况通常都是另外写一个方法来做递归求得（[这里是这类型题的总结](https://leetcode.com/problems/combination-sum/discuss/16502/A-general-approach-to-backtracking-questions-in-Java-%28Subsets-Permutations-Combination-Sum-Palindrome-Partitioning%29)）。

### **代码**

```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<>();
        if (candidates == null || candidates.length == 0) {
            return result;
        }
        Arrays.sort(candidates);
        List<Integer> path = new ArrayList<>();
        dfs(candidates, result, path, target, 0, 0);
        return result;
    }
    private void dfs(int[] candidates, List<List<Integer>> result, List<Integer> path, int target, int sum, int index) {
        if (sum == target) {
            result.add(new ArrayList<>(path));
        }
        
        if (sum > target) {
            return;
        }
        
        for (int i = index; i < candidates.length; i++) {
            path.add(candidates[i]);
            //下一轮还可以选本身，所以是i而非i+1,sum + candidates[i]作为参数而不是赋值
            dfs(candidates, result, path, target, sum + candidates[i], i); 
            path.remove(path.size() - 1);
        }
    }
}
```

## 40 Combination Sum II

### 题目

Given a collection of candidate numbers \(`candidates`\) and a target number \(`target`\), find all unique combinations in `candidates` where the candidate numbers sums to `target`.

Each number in `candidates` may only be used **once** in the combination.

**Note:**

* All numbers \(including `target`\) will be positive integers.
* The solution set must not contain duplicate combinations.

**Example 1:**

```text
Input: candidates = [10,1,2,7,6,1,5], target = 8,
A solution set is:
[
  [1, 7],
  [1, 2, 5],
  [2, 6],
  [1, 1, 6]
]
```

**Example 2:**

```text
Input: candidates = [2,5,2,1,2], target = 5,
A solution set is:
[
  [1,2,2],
  [5]
]
```

### 分析

有重复元素，必须排序和跳过重复元素。这道题需要找unique的组合，例如\[2,2,3\], target = 5，这时候只返回一个\[2, 3\]。

### 代码

```java
class Solution {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<>();
        List<Integer> path = new ArrayList<>();
        Arrays.sort(candidates);
        dfs(candidates, target, result, path, 0, 0);
        return result;
    }
    private void dfs(int[] candidates, int target, List<List<Integer>> result, List<Integer> path, int sum, int index) {
        if (sum == target) {
            result.add(new ArrayList<>(path));
            return;
        }
        if (sum > target) {
            return;
        }
        for (int i = index; i < candidates.length; i++) {
            if (i > index && (candidates[i] == candidates[i - 1])) { //选过了，防止重复combinations
                continue;
            }
            path.add(candidates[i]);
            //跟i比，这里换成i + 1，不使用刚才的重复数字
            dfs(candidates, target, result, path, sum + candidates[i], i + 1);
            path.remove(path.size() - 1);
        }
    }
}
```

## 377 Combination Sum IV

Given an array of **distinct** integers `nums` and a target integer `target`, return _the number of possible combinations that add up to_ `target`.

The answer is **guaranteed** to fit in a **32-bit** integer.

**Example 1:**

```text
Input: nums = [1,2,3], target = 4
Output: 7
Explanation:
The possible combination ways are:
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1)
(2, 2)
(3, 1)
Note that different sequences are counted as different combinations.
```

**Example 2:**

```text
Input: nums = [9], target = 3
Output: 0
```

**Constraints:**

* `1 <= nums.length <= 200`
* `1 <= nums[i] <= 1000`
* All the elements of `nums` are **unique**.
* `1 <= target <= 1000`

**Follow up:** What if negative numbers are allowed in the given array? How does it change the problem? What limitation we need to add to the question to allow negative numbers?

### 分析

可以DP

### 代码

```java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] comb = new int[target + 1];
        comb[0] = 1;
        for (int i = 1; i < comb.length; i++) {
            for (int j = 0; j < nums.length; j++) {
                if (i - nums[j] >= 0) {
                    comb[i] += comb[i - nums[j]];
                }
            }
        }
        return comb[target];
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
        List<List<Integer>> result = new ArrayList<>();
        if (nums == null || nums.length == 0) {
            return result;
        }
        List<Integer> path = new ArrayList<>();
        dfs(nums, result, path);
        return result;
    }
    private void dfs(int[] nums, List<List<Integer>> result, List<Integer> path) {
        if (path.size() == nums.length) {
            result.add(new ArrayList<>(path));
            return;
        }

        for (int num : nums) { // 当前数取了后应该从index = 0开始
            if (path.contains(num)) {
                continue;
            }
            path.add(num);
            dfs(nums, result, path);
            path.remove(path.size() - 1);
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
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(nums);
        dfs(nums, result, new ArrayList<>(), new HashSet<>());// 用一个Set或一维boolean数组来记录元素是否被用过
        return result;
    }

    private void dfs(int[] nums, List<List<Integer>> result, List<Integer> list, Set<Integer> visited) {
        if (visited.size() == nums.length) {
            result.add(new ArrayList<>(list));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (visited.contains(i) || (i > 0 && nums[i] == nums[i - 1] && !visited.contains(i - 1))) {
                continue;
            }
            visited.add(i);
            list.add(nums[i]);
            dfs(nums, result, list, visited);
            visited.remove(i);
            list.remove(list.size() - 1);
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
        List<Integer> path = new ArrayList<>();
        Arrays.sort(nums); // 因为没有重复，不排序也会遍历所有组合，排序会有些小优化路径
        result.add(path);
        dfs(nums, result, path, 0);
        return result;
    }
    private void dfs(int[] nums, List<List<Integer>> result, List<Integer> path, int index) {
        for (int i = index; i < nums.length; i++) {
            if (path.contains(nums[i])) {
                continue;
            }
            path.add(nums[i]);
            result.add(new ArrayList<>(path));
            dfs(nums, result, path, i + 1); // i + 1表示将当前元素的后面所有元素加上
            path.remove(path.size() - 1);
        }
    }
}

```

## 90 Subsets II

### 原题概述

Given a collection of integers that might contain duplicates, _**nums**_, return all possible subsets \(the power set\).

**Note:** The solution set must not contain duplicate subsets.

**Example:**

```text
Input: [1,2,2]
Output:
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]
```

### 题意和分析

同上题的做法，多了一个判断待加入的元素是否是重复的判断。

### 代码

```java
class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        if (nums == null) {
            return result;
        }
        Arrays.sort(nums);
        backtrack(result, nums, new ArrayList<Integer>(), 0);
        return result;
    }
    private void backtrack(List<List<Integer>> result, int[] nums, List<Integer> oneList, int start) {
        result.add(new ArrayList<>(oneList));
        for (int i = start; i < nums.length; i++) {
            if (i > start && nums[i] == nums[i - 1]) {//判断重复值
                continue;
            }
            oneList.add(nums[i]);
            backtrack(result, nums, oneList, i + 1);//i+1是右移一位再判断，跟permutation的题这里不同
            oneList.remove(oneList.size() - 1);
        }
    }
}
```

## 77 Combinations

### 题目

Given two integers _n_ and _k_, return all possible combinations of _k_ numbers out of 1 ... _n_.

You may return the answer in **any order**.

**Example 1:**

```text
Input: n = 4, k = 2
Output:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

**Example 2:**

```text
Input: n = 1, k = 1
Output: [[1]]
```

**Constraints:**

* `1 <= n <= 20`
* `1 <= k <= n`

### 分析

利用数学归纳法的递归写法，和回溯模板。

### 代码

```java
class Solution {
    public static List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> results = new ArrayList<List<Integer>>();
        combine(results, new ArrayList<Integer>(), 1, n, k);
        return results;
    }

    public static void combine(List<List<Integer>> results, List<Integer> oneResult, int start, int n, int k) {
        if (k == 0) {
            results.add(new ArrayList<Integer>(oneResult));
            return;
        }
        for (int i = start; i <= n; i++) {
            oneResult.add(i);
            combine(results, oneResult, i + 1, n, k - 1);
            oneResult.remove(oneResult.size() - 1);
        }
    }
}
```

## 89 Gray Code

### 原题概述

The gray code is a binary numeral system where two successive values differ in only one bit.

Given a non-negative integer _n_ representing the total number of bits in the code, print the sequence of gray code. A gray code sequence must begin with 0.

**Example 1:**

```text
Input: 2
Output: [0,1,3,2]
Explanation:
00 - 0
01 - 1
11 - 3
10 - 2

For a given n, a gray code sequence may not be uniquely defined.
For example, [0,2,3,1] is also a valid gray code sequence.

00 - 0
10 - 2
11 - 3
01 - 1
```

**Example 2:**

```text
Input: 0
Output: [0]
Explanation: We define the gray code sequence to begin with 0.
             A gray code sequence of n has size = 2n, which for n = 0 the size is 20 = 1.
             Therefore, for n = 0 the gray code sequence is [0].
```

### 题意和分析

在一组数的编码中，若任意两个相邻的代码只有一位二进制数不同，则称这种编码为格雷码（Gray Code），另外由于最大数与最小数之间也仅一位数不同，即“首尾相连”，因此又称循环码或反射码。在数字系统中，常要求代码按一定顺序变化。例如，按自然数递增计数，若采用8421码，则数0111变到1000时四位均要变化，而在实际电路中，4位的变化不可能绝对同时发生，则计数中可能出现短暂的其它代码（1100、1111等）。在特定情况下可能导致电路状态错误或输入错误。使用格雷码可以避免这种错误。格雷码有多种编码形式。 

如果有了n-1位的格雷码，想得到n位置的格雷码，首先在n-1位的格雷码前面都添加0作为前2^\(n-1\)个格雷码，它们一定是合法的。因为除了第一位（都是0）其余位都跟n-1的格雷码一致，所以两两之间只相差一位，满足要求。接下来看看如何接上剩下的2^\(n-1\)个，我们把n-1位的格雷码倒序排列，然后在每个前面添加1，然后接到上述的前2^\(n-1\)个就可以了。首先因为是倒序过来，所以中间两个元素除去第一位其他都是一样的（因为原来最后一个，现在倒序过来就是第一个），而他们第一位分别是0和1，满足格雷码的要求。而剩下的元素因为我们是n-1位的格雷码倒序排列，所以两两都是满足要求的，加上前面都一样的位1，仍然满足条件。最后看看这些数字是不是都不一样，前半部分和后半部分肯定不会一样，而因为前半部分都是0开头，后半部分都是1打头，所以互相之间也不会有重复，可以看出覆盖了所有数字，而且依次下来均满足条件。 如此我们提出了格雷码的递推方法，我们只需要做一次位数的循环，每次根据上面结果构造当前位数的结果即可。算法复杂度是O\(2+2^2+...+2^n-1\)=O\(2^n\)，所以是指数量级的，因为是结果数量无法避免。空间复杂度则是结果的大小，也是O\(2^n\)。

另外，第i位置的格雷码还满足G\(i\) = i ^ \(i / 2\);所以用异或来做也是可以的。

### 代码

```java
class Solution {
    public List<Integer> grayCode(int n) {
        ArrayList<Integer> result = new ArrayList<>();
        if (n < 0) {
            return result;
        }
        if (n == 0) {
            result.add(0);
            return result;
        }
        result.add(0);
        result.add(1);
        for (int i = 2; i <= n; i++) {
            int size = result.size();
            for (int j = size -1; j >= 0; j--) {
                result.add(result.get(j) + (1<<(i-1)));
            }
        }
        return result;
    }
}
```

位操作

```java
class Solution {
    public List<Integer> grayCode(int n) {
        List<Integer> result = new ArrayList<>();
        for (int i = 0; i < 1 << n; i++) {
            result.add(i ^ i >> 1);//G(i) = i ^ (i / 2)
        }
        return result;
    }
}
```

## 51 N-Queens

### 原题概述

The _n_-queens puzzle is the problem of placing _n_ queens on an _n_×_n_ chessboard such that no two queens attack each other.

![](https://assets.leetcode.com/uploads/2018/10/12/8-queens.png)

Given an integer _n_, return all distinct solutions to the _n_-queens puzzle.

Each solution contains a distinct board configuration of the _n_-queens' placement, where `'Q'` and `'.'`both indicate a queen and an empty space respectively.

**Example:**

```text
Input: 4
Output: [
 [".Q..",  // Solution 1
  "...Q",
  "Q...",
  "..Q."],

 ["..Q.",  // Solution 2
  "Q...",
  "...Q",
  ".Q.."]
]
Explanation: There exist two distinct solutions to the 4-queens puzzle as shown above.
```

### 题意和分析

N皇后问题，棋盘上每一行每一列和每一斜线都不能都两个皇后；这道题是经典的回溯法，按照每一行遍历，每次加一个皇后，然后利用回溯法检查每一列和每个对角线是否已经有皇后了，有则回溯到前面一个状态，否则继续向下递归，直到最后一行的皇后放好就将结果加入到results里面。时间复杂度O\(n^2\)，空间会创建一个记录每一行皇后所在的列的位置的一维数组，为O\(n\)。

### 代码

```java
class Solution {
    public List<List<String>> solveNQueens(int n) {
        List<List<String>> results = new ArrayList<>();
        if (n <= 0) {
            return results;
        }
        // int[n]是记录每一行皇后所放的位置
        // int[0]是第一行皇后的位置，int[1]是第二行皇后的位置...
        dfs(results, 0, new int[n]);
        return results;
    }
    //传入的参数是结果集，循环的行数，循环到该行-皇后所处的列的位置
    private void dfs(List<List<String>> result, int index, int[] queenPositions) {
        //递归的结束条件，找到一个结果
        if (index == queenPositions.length) {
            addOneSolution(result, queenPositions);
            return;
        }

        // 逐行构建一个有效的放置方法
        for (int i = 0; i < queenPositions.length; i++) {
            queenPositions[index] = i; //每个位置都尝试，找到所有可能的组合
            if (isValid(queenPositions, index)) {
                dfs(result, index + 1, queenPositions);
            }
        }
    }

    private boolean isValid(int[] queenPositions, int index) {
        for (int i = 0; i < index; i++) {
            //剪枝，判断同一列中是否有重复的皇后
            if (queenPositions[i] == queenPositions[index]) {
                return false;
            }
            //判断对角线上是否有重复的皇后
            if (Math.abs(queenPositions[index] - queenPositions[i]) == Math.abs(i - index)) {
                return false;
            }
        }
        return true;
    }

    // 根据每行皇后的位置构建结果并返回
    private void addOneSolution(List<List<String>> result, int[] queens) {
        List<String> oneResult = new ArrayList<>();
        for (int queen : queens) {
            StringBuilder sb = new StringBuilder();
            for (int j = 0; j < queens.length; j++) {
                if (queen == j) {
                    sb.append('Q');
                } else {
                    sb.append('.');
                }
            }
            oneResult.add(sb.toString());
        }
        result.add(oneResult);
    }
}
```

## 306 Additive Number

### 原题概述



### 题意和分析



### 代码

## 93 Restore IP Addresses

### 原题概述

Given a string containing only digits, restore it by returning all possible valid IP address combinations.

**Example:**

```text
Input: "25525511135"
Output: ["255.255.11.135", "255.255.111.35"]
```

### 题意和分析

没什么特殊的，就是典型的回溯法，ip地址的每段的取值是1～255，每次最多取3位，如果是取到2位或者3位，第一个数字不能是0；因为给定的串的长度是固定的，所以时间复杂度是O\(3^4\) = O\(1\)，空间复杂度就是栈空间O\(n\)，同样字符串固定长度，也可以看作是O\(1\)。

### 代码

```java
class Solution {
    public List<String> restoreIpAddresses(String s) {
        List<String> result = new ArrayList<>();
        restoreIp(result, s, 0, "", 0);
        return result;
    }
    private void restoreIp(List<String> result, String s, int index, String oneResult, int count) {
        //传统的回溯法，先写边界条件
        if (count > 4) {
            return;
        }
        if (count == 4 && index == s.length()) {
            result.add(oneResult);
            return;
        }
        for (int i = 1; i < 4; i++) {//取值为1～255，取三位数
            if (index + i > s.length()) {//越界了，后面的substring取不到了，直接break
                break;
            }
            String temp = s.substring(index, index + i);//每次取1位，2位或3位
            //虽然取到1～3位，但依然不合法的情况
            if ((temp.startsWith("0") && temp.length() > 1) || (i == 3 && Integer.parseInt(temp) >= 256)) {
                continue;
            }
            restoreIp(result, s, index + i, oneResult + temp + (count == 3 ? "" : "."), count + 1);
        }
    }
}
```

## 842 Split Array into Fibonacci Sequence

### 题目

Given a string `S` of digits, such as `S = "123456579"`, we can split it into a _Fibonacci-like sequence_ `[123, 456, 579].`

Formally, a Fibonacci-like sequence is a list `F` of non-negative integers such that:

* `0 <= F[i] <= 2^31 - 1`, \(that is, each integer fits a 32-bit signed integer type\);
* `F.length >= 3`;
* and `F[i] + F[i+1] = F[i+2]` for all `0 <= i < F.length - 2`.

Also, note that when splitting the string into pieces, each piece must not have extra leading zeroes, except if the piece is the number 0 itself.

Return any Fibonacci-like sequence split from `S`, or return `[]` if it cannot be done.

**Example 1:**

```text
Input: "123456579"
Output: [123,456,579]
```

**Example 2:**

```text
Input: "11235813"
Output: [1,1,2,3,5,8,13]
```

**Example 3:**

```text
Input: "112358130"
Output: []
Explanation: The task is impossible.
```

**Example 4:**

```text
Input: "0123"
Output: []
Explanation: Leading zeroes are not allowed, so "01", "2", "3" is not valid.
```

**Example 5:**

```text
Input: "1101111"
Output: [110, 1, 111]
Explanation: The output [11, 0, 11, 11] would also be accepted.
```

**Note:**

1. `1 <= S.length <= 200`
2. `S` contains only digits.

### 分析

1. Remove elements with leading zero
2. The element in the sequence should be at most `Integer.MAX_VALUE`
3. The sequence should has at least 3 elements
4. If current number is larger than the sum of previous two elements, stop backtracking
5. If we find a valid sequence, stop backtracking

### 代码

```java

```

```java
class Solution {
    List<Integer> result;

    public List<Integer> splitIntoFibonacci(String S) {
        List<Integer> current = new ArrayList<>();
        backtracking(current, S, 0, 0);
        return result != null ? result : new ArrayList<>();
    }

    public void backtracking(List<Integer> current, String S, int prevIndex, int index) {
        // 3. The sequence should has at least 3 elements
        if( index == S.length() && current.size() > 2)
            result = new ArrayList<>(current);
        else{
            for(int i = index; i < S.length(); i++) {
                // 1. Remove elements with leading zero
                if( S.charAt(prevIndex) == '0' && i + 1 - prevIndex > 1 )
                    break;

                long longValue = Long.parseLong(S.substring(prevIndex, i + 1));
                // 2. The element in the sequence should be at most Integer.MAX_VALUE
                if( longValue > Integer.MAX_VALUE )
                    break;
                int size = current.size();
                if( current.size() < 2 ) {
                    current.add((int) longValue);
                    backtracking(current, S, i + 1, i + 1);
                    // 5. If we find a valid sequence, stop backtracking
                    if( result != null )
                        break;
                    current.remove(current.size() - 1);
                }
                else{
                    long sum = (long)current.get(size - 1) + (long)current.get(size - 2);
                    // 4. If current number is larger than the sum of previous two elements, stop backtracking
                    if( longValue > sum )
                        break;
                    else if( longValue < sum )
                        continue;
                    else {
                        current.add((int) longValue);
                        backtracking(current, S, i + 1, i + 1);
                        if( result != null )
                            break;
                        current.remove(current.size() - 1);
                    }
                }
            }
        }
    }
}
```

另外一个思路

```java
class Solution {
    public List<Integer> splitIntoFibonacci(String S) {
        List<Integer> result = new ArrayList<>();
        backtracking(S, result, 0);
        return result;
    }

    public boolean backtracking(String s, List<Integer> result, int index1) {
        if (index1 == s.length() && result.size() >= 3) { // 结果集中至少3个数
            return true;
        }
        for (int index2 = index1; index2 < s.length(); index2++) { // 从当前index开始直到末尾
            if (s.charAt(index1) == '0' && index2 > index1) { //当前数字为0，且不在第一个位置
                break;
            }
            long num = Long.parseLong(s.substring(index1, index2 + 1));
            if (num > Integer.MAX_VALUE) {
                break;
            }
            int size = result.size();
            // early termination
            if (size >= 2 && num > result.get(size - 1) + result.get(size - 2)) {
                break;
            }
            if (size <= 1 || num == result.get(size - 1) + result.get(size - 2)) {
                result.add((int) num);
                // branch pruning. if one branch has found fib seq, return true to upper call
                if (backtracking(s, result, index2 + 1)) {
                    return true;
                }
                result.remove(result.size() - 1);
            }
        }
        return false;
    }
}
```

## 282 Expression Add Operators

### 题目

Given a string that contains only digits `0-9` and a target value, return all possibilities to add **binary** operators \(not unary\) `+`, `-`, or `*` between the digits so they evaluate to the target value.

**Example 1:**

```text
Input: num = "123", target = 6
Output: ["1+2+3", "1*2*3"] 
```

**Example 2:**

```text
Input: num = "232", target = 8
Output: ["2*3+2", "2+3*2"]
```

**Example 3:**

```text
Input: num = "105", target = 5
Output: ["1*0+5","10-5"]
```

**Example 4:**

```text
Input: num = "00", target = 0
Output: ["0+0", "0-0", "0*0"]
```

**Example 5:**

```text
Input: num = "3456237490", target = 9191
Output: []
```

**Constraints:**

* `0 <= num.length <= 10`
* `num` only contain digits.

### 分析

回溯法，走每一种可能的路径，记录下来作为返回结果。每次都需要注意0，记录下次乘的被乘数。注意溢出情况。同理时间复杂度O\(n!\)。

### 代码

```java
class Solution {
    public List<String> addOperators(String num, int target) {
        List<String> result = new ArrayList<>();
        if (num.isEmpty()) {
            return result;
        }
        helper(num, target, result, "", 0, 0, 0); //这里要是用StringBuilder需要每次清空，比较麻烦
        return result;
    }
    private void helper(String num, int target, List<String> result, String path, long curSum, long lastNum, int index) {
        if (index > num.length()) {
            return;
        }
        if (index == num.length() && curSum == target) { // 把所有字符数字尝试完了,找到一个路径，符合条件
            result.add(path);            
            return;
        }
        for (int i = index; i < num.length(); i++) { // 从当前位置开始遍历
            if (i > index && num.charAt(index) == '0') { // 多位数的情况下，数字不能以0开头，避免01+2这种情况
                break;
            }
            
            //从递归树的锚定位置到当前位置，取出数字，可能是一位数或多位数
            long N = Long.parseLong(num.substring(index, i + 1)); 
            
            
            if (index == 0) { //path中还没有数的时候，不能去把"+","-","*"放到路径最前面（当前path的开头），只能添加数字，避免+1+2这种情况
                helper(num, target, result, path + "" + N, N, N, i + 1);
            } else {
                helper(num, target, result, path + "+" + N, curSum + N, N, i + 1);
                helper(num, target, result, path + "-" + N, curSum - N, -N, i + 1);
                // 乘法比较特殊，需要退出刚才加/减的数字，把退出的该数字算到乘法中去
                helper(num, target, result, path + "*" + N, curSum - lastNum + lastNum * N, lastNum * N, i + 1);
            }
        }
    }
}
```


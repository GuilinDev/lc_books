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

        Set<String> visited = new HashSet<>(); // 记录BFS中产生的状态的字符串
        Queue<String> queue = new LinkedList<>();//存储BFS中每一层的元素

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

## 127 Word Ladder

### 原题

Given two words \(_beginWord_ and _endWord_\), and a dictionary's word list, find the length of shortest transformation sequence from _beginWord_ to _endWord_, such that:

1. Only one letter can be changed at a time.
2. Each transformed word must exist in the word list.

**Note:**

* Return 0 if there is no such transformation sequence.
* All words have the same length.
* All words contain only lowercase alphabetic characters.
* You may assume no duplicates in the word list.
* You may assume _beginWord_ and _endWord_ are non-empty and are not the same.

**Example 1:**

```text
Input:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

Output: 5

Explanation: As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.
```

**Example 2:**

```text
Input:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log"]

Output: 0

Explanation: The endWord "cog" is not in wordList, therefore no possible transformation.
```

### 分析

[无向图的BFS](https://leetcode-cn.com/problems/word-ladder/solution/yan-du-you-xian-bian-li-shuang-xiang-yan-du-you-2/)，前置知识：

* 无向图中两个顶点之间的最短路径的长度，可以通过广度优先遍历得到； 
* 为什么 BFS 得到的路径最短？可以把起点和终点所在的路径拉直来看，两点之间线段最短； 
* 已知目标顶点的情况下，可以分别从起点和目标顶点（终点）执行广度优先遍历，直到遍历的部分有交集，这是双向广度优先遍历的思想。

一种BFS做法是将起始单词跟单词字典（加上终点单词）以此做比较，看是否相差一个字符（因为一次只能变动一个字符），找到相差一个字符的新单词后，再用新单词跟剩余单词字典中单词和终点单词作比较，以此类推，直到找到终点单词，时间复杂度O\(N \* wordLen\)；

![](../.gitbook/assets/image%20%2862%29.png)

上面做法一个优化的地方在于，题目中说了所有单词都是小写字母构成，所以不用每次和单词字典比较，而是从开始单词起，将从左到右每一个字符从a~z都替换以此，检查替换的新单词是否存在与单词字典中（hashset检查为常数时间），比如上图，检查hit，从ait,bit...zit，然后hat,hbt,hct...一直检查到hot（hot后面的hpt...hzt,hia...hiz依然会检查），发现存在，标记访问过，再用hot去检查，以此类推下去，BFS可以保证找到的路径是最短的。

还可以继续优化的地方是，因为知道结束字符串，可以从开始字符串和结束字符串两边同时BFS，如下图，双向BFS检查的字符串数目（蓝色面积）更少，这个知道即可，acm会用到，面试不会考。

![](../.gitbook/assets/image%20%2864%29.png)

### 代码

单向BFS，掌握这个

```java
class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        // 首先将所有字符串放入到set中，方便判断中间和目标字符串是否在字典中
        Set<String> wordDict = new HashSet<>(wordList);
        
        if (wordDict.size() == 0 || !wordDict.contains(endWord)) {
            return 0;
        }
        wordDict.remove(beginWord);
        
        Queue<String> queue = new LinkedList<>();
        queue.offer(beginWord);
        
        // 用一个hashset来判断BFS过程中某个字符串是否访问过
        Set<String> visited = new HashSet<>();
        visited.add(beginWord);
        
        int steps = 1; //包含自己
        
        while (!queue.isEmpty()) {
            // 需要先计算每层BFS起始时候的size，不能放入循环中，因为计算过程总queue的size会发生变化
            int currentSize = queue.size(); 
            
            for (int i = 0; i < currentSize; i++) { // 第一层循环：每层BFS的每个单词
                String word = queue.poll();
                char[] wordArray = word.toCharArray(); // 字符数组方便替换
                for (int j = 0; j < wordArray.length; j++) { // 第二层循环：每个单词的每个字符
                    char originalChar = wordArray[j];
                    for (char k = 'a'; k <= 'z'; k++) { // 第三层循环：每个字符都用a~z替换一遍
                        if (k == originalChar) { // 自己和自己不用判断
                            continue;
                        }
                        wordArray[j] = k; // 替换字符
                        
                        String newWord = String.valueOf(wordArray);
                        if (wordDict.contains(newWord)) {
                            if (newWord.equals(endWord)) { // 找到
                                return steps + 1;
                            } 
                            
                            if (!visited.contains(newWord)) {
                                queue.offer(newWord);
                                // 注意：BFS过程中的单词添加到队列以后，必须马上标记为已经访问
                                visited.add(newWord);
                            }
                        }
                    }
                    wordArray[j] = originalChar; // 恢复之前改变的字符
                }
            }
            steps++; // 每层BFS结束后+1
        }
        return 0; // 没找到
    }
}
```

双向BFS，只了解

```java
class Solution {

    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        // 先将 wordList 放到哈希表里，便于判断某个单词是否在 wordList 里
        Set<String> wordSet = new HashSet<>(wordList);
        if (wordSet.size() == 0 || !wordSet.contains(endWord)) {
            return 0;
        }

        // 标准写法，总的 visited 数组
        Set<String> visited = new HashSet<>();

        // 分别用左边和右边扩散的哈希表代替单向 BFS 里的队列
        Set<String> beginVisited = new HashSet<>();
        beginVisited.add(beginWord);

        Set<String> endVisited = new HashSet<>();
        endVisited.add(endWord);

        int len = beginWord.length();
        int step = 1;
        while (!beginVisited.isEmpty() && !endVisited.isEmpty()) {
            // 打开以方便调试
            // System.out.println("beginVisited => " + beginVisited);
            // System.out.println("  endVisited => " + endVisited + "\n");

            // 优先选择小的哈希表进行扩散，考虑到的情况更少
            if (beginVisited.size() > endVisited.size()) {
                Set<String> temp = beginVisited;
                beginVisited = endVisited;
                endVisited = temp;
            }

            // 逻辑到这里，保证 beginVisited 是相对较小的集合
            // nextLevelVisited 在扩散完成以后，会成为新的 beginVisited
            Set<String> nextLevelVisited = new HashSet<>();
            for (String word : beginVisited) {
                char[] charArray = word.toCharArray();
                for (int i = 0; i < len; i++) {
                    char originChar = charArray[i];
                    for (char c = 'a'; c <= 'z'; c++) {
                        if (originChar == c) {
                            continue;
                        }
                        charArray[i] = c;
                        String nextWord = String.valueOf(charArray);
                        if (wordSet.contains(nextWord)) {
                            if (endVisited.contains(nextWord)) {
                                return step + 1;
                            }
                            if (!visited.contains(nextWord)) {
                                nextLevelVisited.add(nextWord);
                                visited.add(nextWord);
                            }
                        }
                    }
                    // 恢复，下次再用
                    charArray[i] = originChar;
                }
            }

            // 这一行代表表示从 begin 这一侧向外扩散了一层
            beginVisited = nextLevelVisited;
            step++;
        }
        return 0;
    }
}
```

## 199 Binary Tree Right Side View

### 题目

Given a binary tree, imagine yourself standing on the _right_ side of it, return the values of the nodes you can see ordered from top to bottom.

**Example:**

```text
Input: [1,2,3,null,5,null,4]
Output: [1, 3, 4]
Explanation:

   1            <---
 /   \
2     3         <---
 \     \
  5     4       <---
```

### 分析

将二叉树的右视图作为数组打出来，依然是树的的两种遍历方式，BFS和DFS

1\) BFS 

进行层次遍历，记录下每层的最后一个元素。

**时间复杂度：** O\(N\)，每个节点都入队出队了 1 次。  
**空间复杂度：** O\(N\)，这个是最典型的BFS，使用了额外的队列空间。

2\) DFS 

前序遍历稍微改变一下，按照「根结点 -&gt; 右子树 -&gt; 左子树」的顺序来遍历，因为要看右视图，所以确保每层都是最先访问最右边的节点的。

**时间复杂度：** O\(N\)，每个节点都访问了 1 次。  
**空间复杂度：** O\(N\)，因为这不**一定**是一棵平衡二叉树，二叉树的深度最少是 logN, 最坏的情况下会退化成一条链表，深度就是 N，因此递归时使用的栈空间是 O\(N\) 的。

### 代码

BFS

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
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        if (root == null) {
            return result;
        }
        
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        
        while (!queue.isEmpty()) {
            int curSize = queue.size(); //当前层的节点数量
            
            for (int i = 0; i < curSize; i++) {
                TreeNode node = queue.poll();
                
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
                
                if (i == curSize - 1) {
                    result.add(node.val); // 将当前层的最右边的节点加入到结果集中
                }
            }
        }
        return result;
    }
}
```

DFS

```java
class Solution {
    List<Integer> result;
    public List<Integer> rightSideView(TreeNode root) {
        result = new ArrayList<>();
        dfs(root, 0); // 从根节点开始访问，根节点深度是0
        return result;
    }
    private void dfs(TreeNode node, int depth) {
        if (node == null) {
            return;
        }
        
        // 如果当前节点所在深度还没有出现在result里，也就是"每一层"的第一个节点还没有开始计数，
        // 说明在该深度下当前节点是第一个被访问的节点(也就是最右节点)，因此将当前节点加入result中。
        // 注意深搜的遍历顺序，一条道走到底
        if (depth == result.size()) {
            result.add(node.val);
        }
        
        depth++;
        
        // 先访问 当前节点，再递归地访问 右子树 和 左子树。
        dfs(node.right, depth);
        dfs(node.left, depth);
    }
}
```

## 1197 **Minimum Knight Moves**

### **题目**

In an infinite chess board with coordinates from -infinity to +infinity, you have a knight at square \[0, 0\].

A knight has 8 possible moves it can make, as illustrated below. Each move is two squares in a cardinal direction, then one square in an orthogonal direction.

![](../.gitbook/assets/image%20%28113%29.png)

Return the minimum number of steps needed to move the knight to the square \[x, y\]. It is guaranteed the answer exists.

Example 1:

```java
Input: x = 2, y = 1 
Output: 1 
Explanation: [0, 0] → [2, 1] 
```

Example 2:

```java
Input: x = 5, y = 5 
Output: 4 
Explanation: [0, 0] → [2, 1] → [4, 2] → [3, 4] → [5, 5]
```

Constraints:

```java
|x| + |y| <= 300
```

### **分析**

如果用DFS枚举所有走法，然后挑出一个最小的路径，会爆栈。

 其实题目的坐标是-x -y \| -x y \| x -y \| x y \| 四个象限 互为镜像的，也就是 可以只考虑 x y的情况即可，让x = \|x\|, y = \|y\|，使用bfs 宽度优先搜索，搜索中检查是否被访问过和是否在该层边界内，也就是求最短路径的问题。

时间复杂度：O\(V+E\)，V是访问的节点个数，E是边。

空间复杂度：O\(V\)。

### **代码**

```java
class Solution {
    public int minKnightMoves(int x, int y) {
        // 八个方向
        int[][] dirs = {{-2,1},{-1,2},{2,1},{1,2},{-1,2},{-2,1},{-2,-1},{-1,-2}};   
        
        // 小优化，也可不用
        x = Math.abs(x);
        y = Math.abs(y)
        
        Queue<int[]> queue = new LinkedList<>();        
        queue.offer(new int[]{0, 0});        
        HashSet<String> visited = new HashSet();
        int steps = 0;
        
        visited.add("0,0"); // 将坐标转换成string，方便查找
        
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                int[] curPos = queue.poll();
                if (curPos[0] == x && curPos[1] == y) {
                    return steps;
                }
            
                for (int[] dir : dirs) {
                    int newX = curPos[0] + dir[0];
                    int newY = curPos[1] + dir[1];
                    String newPos = newX + "," + newY;
                    if (!visited.contains(newPos)) {
                        queue.offer(new int[]{newX, newY});
                        visited.add(newPos);
                    }
                }
            }
            steps++;
        }
        return -1;
    }
}
```

## 490 The Maze

### 题目

There is a ball in a `maze` with empty spaces \(represented as `0`\) and walls \(represented as `1`\). The ball can go through the empty spaces by rolling **up, down, left or right**, but it won't stop rolling until hitting a wall. When the ball stops, it could choose the next direction.

Given the `maze`, the ball's `start` position and the `destination`, where `start = [startrow, startcol]` and `destination = [destinationrow, destinationcol]`, return `true` if the ball can stop at the destination, otherwise return `false`.

You may assume that **the borders of the maze are all walls** \(see examples\).

**Example 1:** ![](https://assets.leetcode.com/uploads/2020/09/01/maze1.png)

```text
Input: maze = [[0,0,1,0,0],[0,0,0,0,0],[0,0,0,1,0],[1,1,0,1,1],[0,0,0,0,0]], start = [0,4], destination = [4,4]
Output: true
Explanation: One possible way is : left -> down -> left -> down -> right -> down -> right.
```

**Example 2:** ![](https://assets.leetcode.com/uploads/2020/09/01/maze2.png)

```text
Input: maze = [[0,0,1,0,0],[0,0,0,0,0],[0,0,0,1,0],[1,1,0,1,1],[0,0,0,0,0]], start = [0,4], destination = [3,2]
Output: false
Explanation: There is no way for the ball to stop at the destination. Notice that you can pass through the destination but you cannot stop there.
```

**Example 3:**

```text
Input: maze = [[0,0,0,0,0],[1,1,0,0,1],[0,0,0,0,0],[0,1,0,0,1],[0,1,0,0,0]], start = [4,3], destination = [0,1]
Output: false
```

**Constraints:**

* `1 <= maze.length, maze[i].length <= 100`
* `maze[i][j]` is `0` or `1`.
* `start.length == 2`
* `destination.length == 2`
* `0 <= startrow, destinationrow <= maze.length`
* `0 <= startcol, destinationcol <= maze[i].length`
* Both the ball and the destination exist on an empty space, and they will not be at the same position initially.
* The maze contains **at least 2 empty spaces**.

### 分析

BFS或者DFS

1\) BFS，不是每次走一步，而是一直走

2\) DFS

### 代码

BFS

```java
class Solution {
    int[][] dirs = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};
    public boolean hasPath(int[][] maze, int[] start, int[] destination) {
        int rows = maze.length, cols = maze[0].length;
        Queue<Integer> queue = new ArrayDeque<>();
        int startPos = start[0] * 100 + start[1], desPos = destination[0] * 100 + destination[1];
        
        queue.offer(startPos);
        HashSet<Integer> visited = new HashSet<>();
        visited.add(startPos); // BFS的元素刚访问就要标记，而不是等出栈时再标记，否则会重复访问
        
        while (!queue.isEmpty()) {
            int currPos = queue.poll();
            
            if (currPos == desPos) { //能访问到，拉平后的坐标是不会重复的
                return true;
            }
            visited.add(currPos); //立即标记访问过
            
            int currX = currPos / 100;
            int currY = currPos % 100;
            
            for (int[] dir : dirs) {
                int newX = currX;
                int newY = currY;
                // 这里并非只走一步，而是只要为0就一直四个方向走，一直到障碍物停止
                while (newX >= 0 && newX < rows && newY >= 0 && newY < cols && maze[newX][newY] == 0) {
                    newX += dir[0];
                    newY += dir[1];
                }
                // while循环中是到障碍时停下，这里要从障碍中退出来
                newX -= dir[0];
                newY -= dir[1];
                
                int newDest = newX * 100 + newY;
                // 将障碍物前一个的位置判断并加入到队列中，路程中间的不用加，因为可能重复访问
                if (newX >= 0 && newX < rows && newY >= 0 && newY < cols && !visited.contains(newDest)) {
                    queue.offer(newDest);
                }
            }
        }
        return false;
    }
}
```

DFS



## 505 The Maze II

### 题目

There is a **ball** in a maze with empty spaces and walls. The ball can go through empty spaces by rolling **up**, **down**, **left** or **right**, but it won't stop rolling until hitting a wall. When the ball stops, it could choose the next direction.

Given the ball's **start position**, the **destination** and the **maze**, find the shortest distance for the ball to stop at the destination. The distance is defined by the number of **empty spaces** traveled by the ball from the start position \(excluded\) to the destination \(included\). If the ball cannot stop at the destination, return -1.

The maze is represented by a binary 2D array. 1 means the wall and 0 means the empty space. You may assume that the borders of the maze are all walls. The start and destination coordinates are represented by row and column indexes.

**Example 1:**

```text
Input 1: a maze represented by a 2D array

0 0 1 0 0
0 0 0 0 0
0 0 0 1 0
1 1 0 1 1
0 0 0 0 0

Input 2: start coordinate (rowStart, colStart) = (0, 4)
Input 3: destination coordinate (rowDest, colDest) = (4, 4)

Output: 12

Explanation: One shortest way is : left -> down -> left -> down -> right -> down -> right.
             The total distance is 1 + 1 + 3 + 1 + 2 + 2 + 2 = 12.

```

![](../.gitbook/assets/image%20%28123%29.png)

**Example 2:**

```text
Input 1: a maze represented by a 2D array

0 0 1 0 0
0 0 0 0 0
0 0 0 1 0
1 1 0 1 1
0 0 0 0 0

Input 2: start coordinate (rowStart, colStart) = (0, 4)
Input 3: destination coordinate (rowDest, colDest) = (3, 2)

Output: -1

Explanation: There is no way for the ball to stop at the destination.

```

![](../.gitbook/assets/image%20%28124%29.png)

**Note:**

1. There is only one ball and one destination in the maze.
2. Both the ball and the destination exist on an empty space, and they will not be at the same position initially.
3. The given maze does not contain border \(like the red rectangle in the example pictures\), but you could assume the border of the maze are all walls.
4. The maze contains at least 2 empty spaces, and both the width and height of the maze won't exceed 100.

### 分析

1\) BFS，同上题，不用记录visited，用一个二维数组来记录到达\[x,y\]时的需要多少步

2\) DFS

### 代码

BFS

```java
class Solution {
    int[][] dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
    public int shortestDistance(int[][] maze, int[] start, int[] destination) {
        int rows = maze.length, cols = maze[0].length;
        int startPos = start[0] * 100 + start[1], desPos = destination[0] * 100 + destination[1];
        
        // 不用额外用个visited来记录，数组steps可以记录
        //HashSet<Integer> visited = new HashSet<>();        
        Queue<Integer> queue = new ArrayDeque<>();
        queue.offer(startPos);
        
        int[][] steps = new int[rows][cols];
        for (int[] s : steps) { // 到达所有位置的步数初始化为-1
            Arrays.fill(s, -1);
        }
        steps[start[0]][start[1]] = 0; //起点到起点为0步
        
        while(!queue.isEmpty()) {
            int currPos = queue.poll();
            
            //visited.add(currPos);
            
            int currX = currPos / 100;
            int currY = currPos % 100;
            
            for (int[] dir : dirs) {
                int newX = currX;
                int newY = currY;
                int count = steps[currX][currY];
                while (newX >= 0 && newX < rows && newY >= 0 && newY < cols && maze[newX][newY] == 0) {
                    newX += dir[0];
                    newY += dir[1];
                    count++;
                }
                
                newX -= dir[0];
                newY -= dir[1];
                count--;
                
                int newDest = newX * 100 + newY;
                // 没有访问过，或者找到新的路径比刚才所需的步数要少
                if (steps[newX][newY] == -1 || count < steps[newX][newY]) {
                    queue.offer(newDest);
                    steps[newX][newY] = count;
                }
            }
        }
        // 返回终止点的步数，一个值或者-1
        return steps[destination[0]][destination[1]];
    }
}
```

DFS

## 127 Word Ladder

### 题目

Given two words \(_beginWord_ and _endWord_\), and a dictionary's word list, find the length of shortest transformation sequence from _beginWord_ to _endWord_, such that:

1. Only one letter can be changed at a time.
2. Each transformed word must exist in the word list.

**Note:**

* Return 0 if there is no such transformation sequence.
* All words have the same length.
* All words contain only lowercase alphabetic characters.
* You may assume no duplicates in the word list.
* You may assume _beginWord_ and _endWord_ are non-empty and are not the same.

**Example 1:**

```text
Input:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

Output: 5

Explanation: As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.
```

**Example 2:**

```text
Input:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log"]

Output: 0

Explanation: The endWord "cog" is not in wordList, therefore no possible transformation.
```

### 分析

### 代码

BFS

```java
class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        HashSet<String> dict = new HashSet<>(wordList);
        if (!dict.contains(endWord)) {
            return 0;
        }
        Queue<String> queue = new ArrayDeque<>();
        queue.offer(beginWord);
        
        int level = 1;
        HashSet<String> visited = new HashSet<>();
        visited.add(beginWord);
        
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                String currWord = queue.poll();
                if (currWord.equals(endWord)) {
                    return level;
                }
                
                int len = currWord.length();
                for (int j = 0; j < len; j++) {
                    char[] currWordArr = currWord.toCharArray();
                    for (int k = 0; k < 26; k++) {
                        currWordArr[j] = (char)('a' + k);
                        String newCurr = new String(currWordArr);
                        if (dict.contains(newCurr) && !visited.contains(newCurr)) {
                            queue.offer(newCurr);
                            visited.add(newCurr);
                        }
                    }
                }
            }
            level++;
        }
        return 0;
    }
}
```

DFS

Trie


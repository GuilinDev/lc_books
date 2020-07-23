# Graph

## 133 Clone Graph

### 题目

Given a reference of a node in a [**connected**](https://en.wikipedia.org/wiki/Connectivity_%28graph_theory%29#Connected_graph) undirected graph.

Return a [**deep copy**](https://en.wikipedia.org/wiki/Object_copying#Deep_copy) \(clone\) of the graph.

Each node in the graph contains a val \(`int`\) and a list \(`List[Node]`\) of its neighbors.

```text
class Node {
    public int val;
    public List<Node> neighbors;
}
```

**Test case format:**

For simplicity sake, each node's value is the same as the node's index \(1-indexed\). For example, the first node with `val = 1`, the second node with `val = 2`, and so on. The graph is represented in the test case using an adjacency list.

**Adjacency list** is a collection of unordered **lists** used to represent a finite graph. Each list describes the set of neighbors of a node in the graph.

The given node will always be the first node with `val = 1`. You must return the **copy of the given node** as a reference to the cloned graph.

**Example 1:**![](https://assets.leetcode.com/uploads/2019/11/04/133_clone_graph_question.png)

```text
Input: adjList = [[2,4],[1,3],[2,4],[1,3]]
Output: [[2,4],[1,3],[2,4],[1,3]]
Explanation: There are 4 nodes in the graph.
1st node (val = 1)'s neighbors are 2nd node (val = 2) and 4th node (val = 4).
2nd node (val = 2)'s neighbors are 1st node (val = 1) and 3rd node (val = 3).
3rd node (val = 3)'s neighbors are 2nd node (val = 2) and 4th node (val = 4).
4th node (val = 4)'s neighbors are 1st node (val = 1) and 3rd node (val = 3).
```

**Example 2:**![](https://assets.leetcode.com/uploads/2020/01/07/graph.png)

```text
Input: adjList = [[]]
Output: [[]]
Explanation: Note that the input contains one empty list. The graph consists of only one node with val = 1 and it does not have any neighbors.
```

**Example 3:**

```text
Input: adjList = []
Output: []
Explanation: This an empty graph, it does not have any nodes.
```

**Example 4:**![](https://assets.leetcode.com/uploads/2020/01/07/graph-1.png)

```text
Input: adjList = [[2],[1]]
Output: [[2],[1]]
```

**Constraints:**

* `1 <= Node.val <= 100`
* `Node.val` is unique for each node.
* Number of Nodes will not exceed 100.
* There is no repeated edges and no self-loops in the graph.
* The Graph is connected and all nodes can be visited starting from the given node.

### 分析

思路很简单，就是图的DFS和BFS，两个都要重点掌握。

### 代码

DFS

1. 从给定节点开始遍历图
2. 使用一个 HashMap 存储所有已被访问和复制的节点。HashMap 中的 key 是原始图中的节点，value 是克隆图中的对应节点。如果某个节点已经被访问过，则返回其克隆图中的对应节点。
3. 如果当前访问的节点不在 HashMap 中，则创建它的克隆节点存储在 HashMap 中。注意：在进入递归之前，必须先创建克隆节点并保存在 HashMap 中，否则递归会再次遇到同样节点，陷入死循环。
4. 递归调用每个节点的邻接点。每个节点递归调用的次数等于邻接点的数量，每一次调用返回其对应邻接点的克隆节点，最终返回这些克隆邻接点的列表，将其放入对应克隆节点的邻接表中。这样就可以克隆给定的节点和其邻接点。

**时间复杂度**：O\(N\)，每个节点只处理一次。

**空间复杂度**：O\(N\)，存储克隆节点和原节点的 HashMap 需要 O\(N\) 的空间，递归调用栈需要 O\(H\) 的空间，其中 H 是图的深度。总体空间复杂度为 O\(N\)。

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> neighbors;
    
    public Node() {
        val = 0;
        neighbors = new ArrayList<Node>();
    }
    
    public Node(int _val) {
        val = _val;
        neighbors = new ArrayList<Node>();
    }
    
    public Node(int _val, ArrayList<Node> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
}
*/

class Solution {
    public Node cloneGraph(Node node) {
        HashMap<Node, Node> visited = new HashMap<>();
        return dfs(node, visited);
    }
    private Node dfs(Node node, HashMap<Node, Node> visited) {
        if (node == null) {
            return null;
        }
        if (visited.containsKey(node)) {
            return visited.get(node);
        }
        Node clone = new Node(node.val, new ArrayList<>()); //复制的节点和其neighbors
        visited.put(node, clone); // 需要在下面dfs之前添加已被访问和复制
        for (Node n : node.neighbors) {// 递归添加neighbors到复制节点上
            clone.neighbors.add(dfs(n, visited));
        }
        
        return clone;
    }
}
```

BFS，考虑到调用栈的深度，使用 BFS 进行图的遍历比 DFS 更好。BFS也需要借助 HashMap 避免陷入死循环。

1. 使用 HashMap 存储所有访问过的节点和克隆节点。HashMap 的 key 存储原始图的节点，value 存储克隆图中的对应节点。visited 用于防止陷入死循环，和获得克隆图的节点。
2.  将第一个节点添加到队列。克隆第一个节点添加到名为 `visited` 的 HashMap 中。
3. BFS 遍历
   1. 从队列首部取出一个节点。
   2. 遍历该节点的所有邻接点。
   3.  如果某个邻接点已被访问，则该邻接点一定在 `visited` 中，那么从 `visited` 获得该邻接点。
   4.  否则，创建一个新的节点存储在 `visited` 中。
   5. 将克隆的邻接点添加到克隆图对应节点的邻接表中。

**时间复杂度**：O\(N\)，每个节点只处理一次。

**空间复杂度**：O\(N\)。visited 使用 O\(N\) 的空间。BFS 中的队列使用 O\(W\) 的空间，其中 W 是图的宽度（比DFS中的深度要小）。总体空间复杂度为 O\(N\)。

```java
class Solution {
    public Node cloneGraph(Node node) {
        if (node == null) {
            return null;
        }
        HashMap<Node, Node> visited = new HashMap<>(); // 保存访问信息和复制信息
        
        Queue<Node> queue = new LinkedList<>();
        queue.add(node);
        
        visited.put(node, new Node(node.val, new ArrayList<>())); // 先把第一个node标记访问和复制
        
        while (!queue.isEmpty()) {
            Node curNode = queue.poll();
            
            // 遍历当前节点的所有neighbors
            for (Node neighbor : curNode.neighbors) {
                if (!visited.containsKey(neighbor)) {
                    // 把neighbor节点加入到visited中
                    visited.put(neighbor, new Node(neighbor.val, new ArrayList<>()));
                    // 把新节点加入到队列中
                    queue.offer(neighbor);
                }
                
                // 添加当前节点的复制节点的neighbors信息
                visited.get(curNode).neighbors.add(visited.get(neighbor));
            }
        }
        return visited.get(node);
    }
}
```

## 207 Course Schedule

### 原题

There are a total of `numCourses` courses you have to take, labeled from `0` to `numCourses-1`.

Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: `[0,1]`

Given the total number of courses and a list of prerequisite **pairs**, is it possible for you to finish all courses?

**Example 1:**

```text
Input: numCourses = 2, prerequisites = [[1,0]]
Output: true
Explanation: There are a total of 2 courses to take. 
             To take course 1 you should have finished course 0. So it is possible.
```

**Example 2:**

```text
Input: numCourses = 2, prerequisites = [[1,0],[0,1]]
Output: false
Explanation: There are a total of 2 courses to take. 
             To take course 1 you should have finished course 0, and to take course 0 you should
             also have finished course 1. So it is impossible.
```

**Constraints:**

* The input prerequisites is a graph represented by **a list of edges**, not adjacency matrices. Read more about [how a graph is represented](https://www.khanacademy.org/computing/computer-science/algorithms/graph-representation/a/representing-graphs).
* You may assume that there are no duplicate edges in the input prerequisites.
* `1 <= numCourses <= 10^5`

### 分析

这种有前置条件的题目要养成思维，这道题可以简化成： 课程安排图是否可以称为 **有向无环图\(Directed Acyclic Graph, DAG\)**，即课程间规定了前置条件，但不能构成任何环路，否则课程前置条件将不成立。

思路：通过**拓扑排序**判断此课程安排图是否是 有向无环图\(DAG\) 。 **拓扑排序原理**： 对 DAG 的顶点进行排序，使得对每一条有向边 \(u, v\)，均有 u（在排序记录中）比 v 先出现。亦可理解为对某点 v 而言，只有当 v 的所有源点均出现了，v 才能出现。

 通过课程前置条件列表 `prerequisites` 可以得到课程安排图的 **邻接表** `adjacency`，以降低算法时间复杂度，以下两种方法都会用到邻接表。

### 代码

**方法1** - BFS \(in-degree table\)

1.  统计课程安排图中每个节点的入度，生成 **入度表** `indegrees`。
2.  借助一个队列 `queue`，将所有入度为 0 的节点入队（indegree为0说明是没有前置条件的课程）。
3.  当 `queue` 非空时，依次将队首节点出队，在课程安排图中删除此节点 `pre`：
   1.  并不是真正从邻接表中删除此节点 `pre`，而是将此节点对应所有邻接节点 `cur` 的入度 -1，即 `indegrees[cur] -= 1`。
   2.  当入度 -1后邻接节点 `cur` 的入度为 0，说明 `cur` 所有的前驱节点已经被 “删除”，此时将 `cur` 入队
4.  在每次 `pre` 出队时，执行 `numCourses--`
   1. 若整个课程安排图是有向无环图（即可以安排），则所有节点一定都入队并出队过，即完成拓扑排序。换个角度说，若课程安排图中存在环，一定有节点的入度始终不为 0。
   2.  因此，拓扑排序出队次数等于课程个数，返回 `numCourses == 0` 判断课程是否可以成功安排。

时间复杂度 O\(N + M\)： 遍历一个图需要访问所有节点和所有临边，N 和 M 分别为节点数量和临边数量； 

空间复杂度 O\(N + M\)： 为建立邻接表所需额外空间，adjacency 长度为 N ，并存储 MM条临边的数据。

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        int[] indegrees = new int[numCourses]; // 入度表
        List<List<Integer>> adjacency = new ArrayList<>(); //邻接表存储图
        
        //BFS
        Queue<Integer> queue = new LinkedList<>();
        
        for (int i = 0; i < numCourses; i++) {
            adjacency.add(new ArrayList<>());
        }
        
        // 给每门课添加入度，以及将入度信息和课程本身添加到邻接表中
        for (int[] pre : prerequisites) {
            indegrees[pre[0]]++;
            adjacency.get(pre[1]).add(pre[0]);
        }
        
        // 将入度为0（没有前置课程的）的课程索引初始化入队
        for (int i = 0; i < numCourses; i++) {
            if (indegrees[i] == 0) {
                queue.offer(i);
            }
        }
        
        // BFS拓扑排序
        while (!queue.isEmpty()) {
            int pre = queue.poll();
            numCourses--;
            for (int cur : adjacency.get(pre)) { // 当前课程减去入度
                indegrees[cur]--;
                if (indegrees[cur] == 0) {
                    queue.offer(cur);
                }
            }
        }
        return numCourses == 0;
    }
}
```

**方法2** - DFS，通过 DFS 判断图中是否有环。

1. 借助一个标志列表 `flags`，用于判断每个节点 `i` （课程）的状态：
   1.  未被 DFS 访问：`i == 0`
   2.  已被**当前节点启动**的 DFS 访问：`i == 1`
   3.  之前已被**其他节点启动**的 DFS 访问：`i == -1`
2.  对 `numCourses` 个节点依次执行 DFS，判断每个节点起步 DFS 是否存在环，若存在环直接返回 False。DFS 流程:
   1. 终止条件：
      *  当 `flag[i] == -1`，说明当前访问节点已被其他节点启动的 DFS 访问，无需再重复搜索，直接返回true
      *  当 `flag[i] == 1`，说明在本轮 DFS 搜索中节点 `i` 被第 22 次访问，即 **课程安排图有环** ，直接返回false
   2.  将当前访问节点 `i` 对应 `flag[i]` 置为 1，即标记其被本轮 DFS 当前节点访问过
   3.  递归访问当前节点 `i` 的所有邻接节点 `j`，当发现环直接返回false
   4.  当前节点所有邻接节点已被遍历，并没有发现环，则将当前节点 `flag` 置为 -1并返回true （下面的DFS表示已被之前节点访问过）
3. 若整个图 DFS 结束并未发现环，返回 true，否则返回false。

时间复杂度 O\(N + M\)： 遍历一个图需要访问所有节点和所有临边，N和 M分别为节点数量和临边数量；

 空间复杂度 O\(N + M\)： 为建立邻接表所需额外空间，adjacency 长度为 N ，并存储 M 条临边的数据。

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        List<List<Integer>> adjacency = new ArrayList<>();// 邻接表存储图
        for (int i = 0; i < numCourses; i++) {
            adjacency.add(new ArrayList<>());
        }
        
        int[] flags = new int[numCourses];
        
        for (int[] pre : prerequisites) {
            adjacency.get(pre[1]).add(pre[0]);
        }
        
        for (int i = 0; i < numCourses; i++) {
            if (!dfs(adjacency, flags, i)) {
                return false;
            }
        }
        return true;
    }
    
    private boolean dfs(List<List<Integer>> adjacency, int[] flags, int i) {
        if (flags[i] == 1) {
            return false;
        }
        if (flags[i] == -1) {
            return true;
        }
        flags[i] = 1;
        for (int j : adjacency.get(i)) {
            if (!dfs(adjacency, flags, j)) {
                return false;
            }
        }
        flags[i] = -1;
        return true;
    }
} 
```

## 210 Course Schedule II

### 题目

There are a total of _n_ courses you have to take, labeled from `0` to `n-1`.

Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: `[0,1]`

Given the total number of courses and a list of prerequisite **pairs**, return the ordering of courses you should take to finish all courses.

There may be multiple correct orders, you just need to return one of them. If it is impossible to finish all courses, return an empty array.

**Example 1:**

```text
Input: 2, [[1,0]] 
Output: [0,1]
Explanation: There are a total of 2 courses to take. To take course 1 you should have finished   
             course 0. So the correct course order is [0,1] .
```

**Example 2:**

```text
Input: 4, [[1,0],[2,0],[3,1],[3,2]]
Output: [0,1,2,3] or [0,2,1,3]
Explanation: There are a total of 4 courses to take. To take course 3 you should have finished both     
             courses 1 and 2. Both courses 1 and 2 should be taken after you finished course 0. 
             So one correct course order is [0,1,2,3]. Another correct ordering is [0,2,1,3] .
```

**Note:**

1. The input prerequisites is a graph represented by **a list of edges**, not adjacency matrices. Read more about [how a graph is represented](https://www.khanacademy.org/computing/computer-science/algorithms/graph-representation/a/representing-graphs).
2. You may assume that there are no duplicate edges in the input prerequisites.

### 分析

与207相同，只是这道题要求打印出完成课程的顺序，不仅仅是判断是否可以完成。《算法4》中第4.2.4.2小节《寻找有向环》和《拓扑排序》详细解释了做法。

![](../.gitbook/assets/image%20%28105%29.png)

![](../.gitbook/assets/image%20%28104%29.png)

做法依然时BFS（记录入度表）和DFS，可以邻接表或者邻接矩阵，上一道题用的邻接表，这次用邻接矩阵。

### 代码

BFS

1. 建立入度表，入度为 0 的节点先入队 
2. 当队列不为空，节点出队，标记学完课程数量的变量加 1，并记录该课程 
3. 将课程的邻居入度减 1 
4. 若邻居课程入度为 0，加入队列 

用一个变量记录学完的课程数量，一个数组记录最终结果，简洁好理解。

```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        if (numCourses == 0) {
            return new int[0];
        }
        int[] inDegrees = new int[numCourses];
        
        //BFS
        Queue<Integer> queue = new LinkedList<>();
        
        // 建立入度表
        for (int[] pre : prerequisites) {
            inDegrees[pre[0]]++;
        }
        
        // 入度为0的节点的队列
        for (int i = 0; i < inDegrees.length; i++) {
            if (inDegrees[i] == 0) {
                queue.offer(i); // 入度索引
            }
        }
        
        int count = 0; // 可以学完的课程的数量
        int[] result = new int[numCourses];//可以学完的课程
        
        // 根据提供的先修课列表，删除入度为 0 的节点
        while (!queue.isEmpty()){
            int curr = queue.poll();
            
            result[count] = curr;   // 将可以学完的课程加入结果当中
            count++;
            
            for (int[] pre : prerequisites) {
                if (pre[1] == curr){
                    inDegrees[pre[0]]--;
                    if (inDegrees[pre[0]] == 0) {
                        queue.offer(pre[0]);
                    }
                }
            }
        }
        if (count == numCourses) { // 可以全部学完
            return result;
        }
        return new int[0];        
    }
}
```

DFS

1. 建立邻接矩阵
2. DFS 访问每一个课程，若存在环直接返回

1\) 数组作为邻接矩阵，status 保存课程的访问状态，同一个栈保存课程的访问序列。

```java
class Solution {
        // 邻接矩阵 + DFS   由于用的数组，每次都要遍历，效率比较低
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        if (numCourses == 0) {
            return new int[0];
        }
        
        // 建立邻接矩阵
        int[][] graph = new int[numCourses][numCourses];
        for (int[] p : prerequisites) {
            graph[p[1]][p[0]] = 1;
        }
        
        // 记录访问状态的数组，访问过了标记 -1，正在访问标记 1，还未访问标记 0
        int[] status = new int[numCourses];
        Stack<Integer> stack = new Stack<>();  // 用栈保存访问序列
        for (int i = 0; i < numCourses; i++) {
            if (!dfs(graph, status, i, stack)) return new int[0]; // 只要存在环就返回
        }
        
        int[] result = new int[numCourses];
        for (int i = 0; i < numCourses; i++) {
            result[i] = stack.pop();
        }
        return result;
    }

    private boolean dfs(int[][] graph, int[] status, int i, Stack<Integer> stack) {
        if (status[i] == 1) { // 当前节点在此次 dfs 中正在访问，说明存在环
            return false;
        }
        if (status[i] == -1) {
            return true;
        }

        status[i] = 1;
        for (int j = 0; j < graph.length; j++) {
            // dfs 访问当前课程的后续课程，看是否存在环
            if (graph[i][j] == 1 && !dfs(graph, status, j, stack)) return false;
        }
        status[i] = -1;  // 标记为已访问
        stack.push(i);
        return true;
    }
}
```

2\) hashset作为邻接矩阵，推荐

```java
class Solution {
        // 方法 2 升级版：用 HashSet 作为邻接矩阵，加速查找速度
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        if (numCourses == 0) {
            return new int[0];
        }
        
        // HashSet 作为邻接矩阵
        HashSet<Integer>[] graph = new HashSet[numCourses];
        for (int i = 0; i < numCourses; i++) {
            graph[i] = new HashSet<>();
        }
        for (int[] p : prerequisites) {
            graph[p[1]].add(p[0]);
        }
        
        int[] mark = new int[numCourses]; // 标记数组
        Stack<Integer> stack = new Stack<>(); // 结果栈
        for (int i = 0; i < numCourses; i++) {
            if(!isCycle(graph, mark, i, stack)) return new int[0];
        }
        int[] res = new int[numCourses];
        for (int i = 0; i < numCourses; i++) {
            res[i] = stack.pop();
        }
        return res;
    }

    private boolean isCycle(HashSet<Integer>[] graph, int[] mark, int i, Stack<Integer> stack) {
        if (mark[i] == -1) {
            return true;
        }
        if (mark[i] == 1) {
            return false;
        }

        mark[i] = 1;
        for (int neighbor : graph[i]) {
            if (!isCycle(graph, mark, neighbor, stack)) {
                return false;
            }
        }
        mark[i] = -1;
        stack.push(i);
        return true;
    }
}
```

## 269 Alien Dictionary

### 原题概述

There is a new alien language which uses the latin alphabet. However, the order among letters are unknown to you. You receive a list of **non-empty** words from the dictionary, where **words are sorted lexicographically by the rules of this new language**. Derive the order of letters in this language.

**Example 1:**

```text
Input:
[
  "wrt",
  "wrf",
  "er",
  "ett",
  "rftt"
]

Output: "wertf"
```

**Example 2:**

```text
Input:
[
  "z",
  "x"
]

Output: "zx"
```

**Example 3:**

```text
Input:
[
  "z",
  "x",
  "z"
] 

Output: "" 

Explanation: The order is invalid, so return "".
```

**Note:**

1. You may assume all letters are in lowercase.
2. You may assume that if a is a prefix of b, then a must appear before b in the given dictionary.
3. If the order is invalid, return an empty string.
4. There may be multiple valid order of letters, return any one of them is fine.

### 题意和分析

这道题让给了我们一些按“字母顺序”排列的单词，但是这个字母顺序不是我们熟知的顺序，而是另类的顺序，让我们根据这些“有序”的单词来找出新的字母顺序，这实际上是一道有向图的问题。跟207 Couurse Schedule，210 Couurse Schedule II和630 Couurse ScheduleIII类似。

1\)DFS，建立一个二维的boolean数组g，然后把出现过的字母的对应的位置都赋为true，然后我们把可以推出的顺序的对应位置也赋为true，然后我们就开始递归调用，如果递归函数有返回false的，说明有冲突，则返回false，递归调用结束后结果res中存了入度不为0的字母，最后把入度为0的字母加到最后面，最后把结果result翻转一下即可；

2\) BFS

3\) Topological Sorting

### 代码

DFS，参考[这里](https://leetcode.com/problems/alien-dictionary/discuss/70115/3ms-Clean-Java-Solution-%28DFS%29)

```java
class Solution {
    private final int N = 26;
    public String alienOrder(String[] words) {
        boolean[][] adj = new boolean[N][N];
        int[] visited = new int[N];
        buildGraph(words, adj, visited);

        StringBuilder sb = new StringBuilder();
        for(int i = 0; i < N; i++) {
            if(visited[i] == 0) {                 // unvisited
                if(!dfs(adj, visited, sb, i)) return "";
            }
        }
        return sb.reverse().toString();
    }

    public boolean dfs(boolean[][] adj, int[] visited, StringBuilder sb, int i) {
        visited[i] = 1;                            // 1 = visiting
        for(int j = 0; j < N; j++) {
            if(adj[i][j]) {                        // connected
                if(visited[j] == 1) return false;  // 1 => 1, cycle   
                if(visited[j] == 0) {              // 0 = unvisited
                    if(!dfs(adj, visited, sb, j)) return false;
                }
            }
        }
        visited[i] = 2;                           // 2 = visited
        sb.append((char) (i + 'a'));
        return true;
    }

    public void buildGraph(String[] words, boolean[][] adj, int[] visited) {
        Arrays.fill(visited, -1);                 // -1 = not even existed
        for(int i = 0; i < words.length; i++) {
            for(char c : words[i].toCharArray()) visited[c - 'a'] = 0;
            if(i > 0) {
                String w1 = words[i - 1], w2 = words[i];
                int len = Math.min(w1.length(), w2.length());
                for(int j = 0; j < len; j++) {
                    char c1 = w1.charAt(j), c2 = w2.charAt(j);
                    if(c1 != c2) {
                        adj[c1 - 'a'][c2 - 'a'] = true;
                        break;
                    }
                }
            }
        }
    }
}
```

BFS，参考[这里](https://leetcode.com/problems/alien-dictionary/discuss/70119/Java-AC-solution-using-BFS)

```java
class Solution {
    public String alienOrder(String[] words) {
        Map<Character, Set<Character>> map=new HashMap<Character, Set<Character>>();
        Map<Character, Integer> degree=new HashMap<Character, Integer>();
        String result="";
        if(words==null || words.length==0) return result;
        for(String s: words){
            for(char c: s.toCharArray()){
                degree.put(c,0);
            }
        }
        for(int i=0; i<words.length-1; i++){
            String cur=words[i];
            String next=words[i+1];
            int length=Math.min(cur.length(), next.length());
            for(int j=0; j<length; j++){
                char c1=cur.charAt(j);
                char c2=next.charAt(j);
                if(c1!=c2){
                    Set<Character> set=new HashSet<Character>();
                    if(map.containsKey(c1)) set=map.get(c1);
                    if(!set.contains(c2)){
                        set.add(c2);
                        map.put(c1, set);
                        degree.put(c2, degree.get(c2)+1);
                    }
                    break;
                }
            }
        }
        Queue<Character> q=new LinkedList<Character>();
        for(char c: degree.keySet()){
            if(degree.get(c)==0) q.add(c);
        }
        while(!q.isEmpty()){
            char c=q.remove();
            result+=c;
            if(map.containsKey(c)){
                for(char c2: map.get(c)){
                    degree.put(c2,degree.get(c2)-1);
                    if(degree.get(c2)==0) q.add(c2);
                }
            }
        }
        if(result.length()!=degree.size()) return "";
        return result;
    }
}
```

Topological Sorting，参考[这里](https://leetcode.com/problems/alien-dictionary/discuss/70169/My-Concise-JAVA-solution-based-on-Topological-Sorting)

```java
class Solution {
    public String alienOrder(String[] words) {
        List<Point> pairs = new LinkedList<Point>(); // Adjacency list: pair = (node, node's predecessor)
        Set<Character>chs = new HashSet<Character>();// All distinct characters

        // 1. Convert characters to a graph: Adjacency lists 
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            boolean alreadySet = false;// Only set one pair where the characters at the same position differs in two neighbor rows. e.g. "wrtk" < "wrfp"=> 't' < 'f'
            for (int j = 0; j < words[i].length(); j++) {
                if (!alreadySet && i > 0 && j < words[i-1].length() && words[i].charAt(j) != words[i-1].charAt(j)) {// Set dependency of two characters by comparing two neighbor rows. 
                    pairs.add(new Point(words[i].charAt(j), words[i-1].charAt(j)));
                    alreadySet = true;
                }
                chs.add(word.charAt(j));// Add distinct character to chs set
            }
        }

        // 2. Topological sorting: keep adding elements whose in-degree is 0
        String res = "";
        int indegree[] = new int[256];
        Arrays.fill(indegree, Integer.MIN_VALUE);

        for (Character ch : chs) indegree[ch] = 0;// Initialize in-degree of the distinct characters in the words list
        for (int i = 0; i < pairs.size(); i++)// Increase in-degree according to the dependency of pairs list
            indegree[pairs.get(i).x]++;

        Queue<Character> queue = new LinkedList<Character>();
        for (int i = 0; i < 256; i++) {
            if (indegree[i] == 0) {// Add the character whose in-degree = 0, which means it doesn't have any predecessor 
                res += (char) i;
                queue.offer((char) i);
            }
        }

        while (!queue.isEmpty()) {
            Character predecessor = queue.poll(); // Dequeue the character whose in-degree = 0 from queue

            for (int i = 0; i < pairs.size(); i++) {
                if (pairs.get(i).y == predecessor) {// Update in-degree: decrease 1 to the successors of the character whose in-degree = 0
                    indegree[pairs.get(i).x]--;
                    if (indegree[pairs.get(i).x] == 0) {// If in-degree = 0, add the character to the queue, and append it to the result string
                        res += (char) pairs.get(i).x;
                        queue.offer((char) pairs.get(i).x);
                    }
                }
            }
        }
        return res.length() == chs.size() ? res : "";// NOTE: res.length should equal the size of distinct characters, otherwise a cycle must exist 
    }
}
```



```java
class Solution {
    public String alienOrder(String[] words) {
        if (words == null || words.length == 0) {
            return "";
        }
        StringBuilder sb = new StringBuilder();
        HashMap<Character, Set<Character>> suc = new HashMap<Character, Set<Character>>();
        HashMap<Character, Integer> pre = new HashMap<Character, Integer> ();
        HashSet<Character> charSet = new HashSet<Character>();
        String previousWord = new String("");
        for (String currentWord : words) {
            for (Character c : currentWord.toCharArray()) {
                charSet.add(c);
            }
            for (int i = 0; i < Math.min(previousWord.length(), currentWord.length()); ++i) {
                if (previousWord.charAt(i) != currentWord.charAt(i)) {
                    if (!pre.containsKey(currentWord.charAt(i))) { //----section_pre----
                        pre.put(currentWord.charAt(i), 1);
                    } else {
                        //ERROR1: 过滤掉重复的关系 ["za","zb","ca","cb"], 从输入得出的中间结论，"a->b"会重复出现两次，需要滤重。为了实现滤重，重新调整了section_pre和section_suc部分的顺序。
                        if (suc.containsKey(previousWord.charAt(i)) &&
                                suc.get(previousWord.charAt(i)).contains(currentWord.charAt(i))) {
                            continue;
                        }
                        pre.put(currentWord.charAt(i), pre.get(currentWord.charAt(i)) + 1);
                    }
                    if (!suc.containsKey(previousWord.charAt(i))) { //----section_suc----
                        suc.put(previousWord.charAt(i), new HashSet<Character> ());
                    }
                    suc.get(previousWord.charAt(i)).add(currentWord.charAt(i));
                    break; //the BEST part of this question
                }
            }
            previousWord = currentWord;
        }
        Queue<Character> queue = new LinkedList<Character>();
        for (Character each : charSet) {
            if (!pre.containsKey(each)) {
                queue.add(each);
            }
        }


        while (!queue.isEmpty()) {
            Character currentChar = queue.poll();
            sb.append(currentChar);
            //ERROR2 should check containsKey
            if (!suc.containsKey(currentChar)) {
                continue;
            }
            for (Character each : suc.get(currentChar)) {
                pre.put(each, pre.get(each) - 1);
                if (pre.get(each) == 0) {
                    queue.offer(each);
                }
            }
        }
        return sb.length() == charSet.size() ? sb.toString() : "";
    }
}
```

## 


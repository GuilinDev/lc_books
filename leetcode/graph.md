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

另外一种写法，因为课程是数字来表示，所以可以不用邻接表来存储每个入度，直接相加即可

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        int[] results = new int[numCourses];
        int[] indegrees = new int[numCourses]; //入度表也可以用hashmap表示

        // 创建入度表
        for (int[] pre : prerequisites) {
            indegrees[pre[0]]++; // 把每个入度简化为增加1
        }

        Queue<Integer> queue = new ArrayDeque<>();
        // 首先将入度为0的顶点放进来
        for (int i = 0; i < numCourses; i++) { // 课程表用数字表示
            if (indegrees[i] == 0) {
                queue.offer(i);
            } 
        }

        int count = 0; // 记录课程是否全部被修完
        while (!queue.isEmpty()) {
            int currCourse = queue.poll();
            results[count] = currCourse; // 把访问到的顶点（修完的课）加入到结果中
            count++;

            // 更新剩余未访问的顶点（未修的课）的入度，并将入度为0的课加入到队列中准备访问
            for (int[] pre : prerequisites) {
                if (pre[1] == currCourse) { // 只用修改依赖课程是当前课程的课程
                    indegrees[pre[0]]--; // 依赖课程是当前课程的课程的入度--11
                    if (indegrees[pre[0]] == 0) { // 入度为0则将课程加入到BFS的下一层中
                        queue.offer(pre[0]); 
                    }
                }
            }
        }

        // 检查下全部课程是否都被修完
        return count == numCourses;
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

邻接表

```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        int[] results = new int[numCourses];
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
        int count = 0;
        while (!queue.isEmpty()) {
            int pre = queue.poll();
            results[count] = pre;
            count++;

            for (int cur : adjacency.get(pre)) { // 当前课程减去入度
                indegrees[cur]--;
                if (indegrees[cur] == 0) {
                    queue.offer(cur);
                }
            }
        }
        return numCourses == count ? results : new int[]{};
    }
}
```

用一个变量记录学完的课程数量，一个数组记录最终结果，简洁好理解。

```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        int[] results = new int[numCourses];
        int[] indegrees = new int[numCourses]; //入度表也可以用hashmap表示

        // 创建入度表
        for (int[] pre : prerequisites) {
            indegrees[pre[0]]++; // 把每个入度简化为增加1
        }

        Queue<Integer> queue = new ArrayDeque<>();
        // 首先将入度为0的顶点放进来
        for (int i = 0; i < numCourses; i++) { // 课程表用数字表示
            if (indegrees[i] == 0) {
                queue.offer(i);
            } 
        }

        int count = 0; // 记录课程是否全部被修完
        while (!queue.isEmpty()) {
            int currCourse = queue.poll();
            results[count] = currCourse; // 把访问到的顶点（修完的课）加入到结果中
            count++;

            // 更新剩余未访问的顶点（未修的课）的入度，并将入度为0的课加入到队列中准备访问
            for (int[] pre : prerequisites) {
                if (pre[1] == currCourse) { // 只用修改依赖课程是当前课程的课程
                    indegrees[pre[0]]--; // 依赖课程是当前课程的课程的入度--11
                    if (indegrees[pre[0]] == 0) { // 入度为0则将课程加入到BFS的下一层中
                        queue.offer(pre[0]); 
                    }
                }
            }
        }

        // 检查下全部课程是否都被修完
        return count == numCourses ? results : new int[]{};
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

2\) hashset作为邻接矩阵

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

这道题让给了我们一些按“字母顺序”排列的单词，但是这个字母顺序不是我们熟知的顺序，而是另类的顺序，让我们根据这些“有序”的单词来找出新的字母顺序，这实际上是一道有向图的问题。跟207 Course Schedule，210 Course Schedule II和630 Course Schedule III类似。理解题目：

* “abf” -&gt; "abc" 这个意思是 abf 本身之间没有顺序关系， 但是 abf 和 abc 之间有顺序 所以 f 在 c之前； 
* “abc” -&gt; "ab" 这个就是错误的，因为 c -&gt; "没有" ，有问题。

1\)DFS，建立一个二维的boolean数组g，然后把出现过的字母的对应的位置都赋为true，然后我们把可以推出的顺序的对应位置也赋为true，然后我们就开始递归调用，如果递归函数有返回false的，说明有冲突，则返回false，递归调用结束后结果res中存了入度不为0的字母，最后把入度为0的字母加到最后面，最后把结果result翻转一下即可；

2\) BFS

3\) Topological Sorting，将词典中字符串的字符两两对比，只有第一个不同的字符才是正确的排序，如ert和wrf，只能推断出e的优先级高于w，剩余字符的优先级不能推断。将字符串的优先级构建为图，然后进行拓扑排序。如果图中无环，则将拓扑排序输出，否则顺序是非法的。

注意对于输入"za","zb","ca","cb"，字符关系为a-&gt;b、z-&gt;c，输出可以为azbc、zacb，只要输出一种即可。

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

Topological Sorting

```java
class Solution {
    public String alienOrder(String[] words) {
        //1.构建图
        Map<Character, Set<Character>> map = new HashMap<>();
        for (int i = 0; i < words.length - 1; i++) {
            for (int j = 0; j < words[i].length() && j < words[i + 1].length(); j++) {
                //如果字符相同，比较下一个
                if (words[i].charAt(j) == words[i + 1].charAt(j)) continue;
                //保存第一个不同的字符顺序
                Set<Character> set = map.getOrDefault(words[i].charAt(j), new HashSet<>());
                set.add(words[i + 1].charAt(j));
                map.put(words[i].charAt(j), set);
                break;
            }
        }

        //2.拓扑排序
        //创建保存入度的数组
        int[] degrees = new int[26];
        Arrays.fill(degrees, -1);
        //注意，不是26字母都在words中出现，所以出度分为两种情况：没有出现的字母出度为-1，出现了的字母的出度为非负数
        for (String str : words) {
            //将出现过的字符的出度设定为0
            for (char c : str.toCharArray())
                degrees[c - 'a'] = 0;
        }
        for (char key : map.keySet()) {
            for (char val : map.get(key)) {
                degrees[val - 'a']++;
            }
        }
        //创建StringBuilder保存拓扑排序
        StringBuilder sb = new StringBuilder();
        //创建一个Queue保存入度为0的节点
        Queue<Character> list = new LinkedList<>();

        int count = 0;//计算图中节点数
        for (int i = 0; i < 26; i++) {
            if (degrees[i] != -1) count++;
            if (degrees[i] == 0) {
                list.add((char) ('a' + i));
            }
        }

        while (!list.isEmpty()) {
            Character cur = list.poll();
            sb.append(cur);
            //将邻接点出度-1
            if (map.containsKey(cur)) {
                Set<Character> set = map.get(cur);
                for (Character c : set) {
                    degrees[c - 'a']--;
                    if (degrees[c - 'a'] == 0) list.add(c);
                }
            }
        }

        //判断是否有环
        if (sb.length() != count) return "";
        else return sb.toString();

    }
}
```

## 277 Find the Celebrity

### 题目

Suppose you are at a party with `n` people \(labeled from `0` to `n - 1`\) and among them, there may exist one celebrity. The definition of a celebrity is that all the other `n - 1` people know him/her but he/she does not know any of them.

Now you want to find out who the celebrity is or verify that there is not one. The only thing you are allowed to do is to ask questions like: "Hi, A. Do you know B?" to get information of whether A knows B. You need to find out the celebrity \(or verify there is not one\) by asking as few questions as possible \(in the asymptotic sense\).

You are given a helper function `bool knows(a, b)` which tells you whether A knows B. Implement a function `int findCelebrity(n)`. There will be exactly one celebrity if he/she is in the party. Return the celebrity's label if there is a celebrity in the party. If there is no celebrity, return `-1`.

**Example 1:** ![](https://assets.leetcode.com/uploads/2019/02/02/277_example_1_bold.PNG)

```text
Input: graph = [
  [1,1,0],
  [0,1,0],
  [1,1,1]
]
Output: 1
Explanation: There are three persons labeled with 0, 1 and 2. graph[i][j] = 1 means person i knows person j, otherwise graph[i][j] = 0 means person i does not know person j. The celebrity is the person labeled as 1 because both 0 and 2 know him but 1 does not know anybody.
```

**Example 2:** ![](https://assets.leetcode.com/uploads/2019/02/02/277_example_2.PNG)

```text
Input: graph = [
  [1,0,1],
  [1,1,0],
  [0,1,1]
]
Output: -1
Explanation: There is no celebrity.
```

**Note:**

1. The directed graph is represented as an adjacency matrix, which is an `n x n` matrix where `a[i][j] = 1` means person `i` knows person `j` while `a[i][j] = 0` means the contrary.
2. Remember that you won't have direct access to the adjacency matrix.

### 分析

在n个人中\[0, n - 1\]中有个名人，大家都认识他，但他不认识任何别人，找出这个名人，要是没有就返回-1。

判断出度是否为0

先设待定名人candidate为0， 遍历每一个人，如果candidate认识另一个人i，说明他不是名人，把i变成candidate。 最后再判断是不是所有人都认识新的candidate，并且他不认识所有人。

为什么这个能找到可能的名人（出度为0）证明： 反证法： 假如找到candidate的不是名人，说明出度不为0。 但若该candidate出度不为0，则轮到candidate的时候一定会转移，矛盾。 那有没有可能漏掉名人呢？答案是否定的，因为名人一定不认识其他人，并且其他人都是认识他，迭代了n次之后，最后会收敛到一定是名人。

### 代码

用两个数组，一个数组记录认识别人的人数，一个数组记录认识自己的人数，O\(n^2\)

```java
public int findCelebrity(int n) {
    int[] knowOther = new int[n];
    int[] otherKnow = new int[n];
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (knows(i, j)) {
                otherKnow[j]++; // 别人认识他的人数
                knowOther[i]++; // 他认识别人的人数
            }
        }
    }
    for (int i = 0; i < otherKnow.length; i++) {
        // 别人认识他有n-1个（加上他共n个），他只认识他自己
        if (otherKnow[i] == n && knowOther[i] == 1) {
            return i;
        }
    }
    return -1;
}
```

计算出度，O\(n\)

```java
/* The knows API is defined in the parent class Relation.
      boolean knows(int a, int b); */

public class Solution extends Relation {
    /**
    第一个for循环是用来找候选名人的，因为每次调用knows(i,j）至少可以排除一个人，
    假设i认识j，那么i肯定不是名人; 若i不认识j，j肯定不是（如果是名人，那么i肯定会认识他），
    第一次for循环总共调用了n-1次，很明显，每次排除的人都不一样，
    因为j是增加的，当排除一个候选的i或者j后，下次肯定不会再次排除这个人，故总共排除了n-1个人，
    剩下最后一个满足条件的可以作为候选，然后利用这个候选判断其是否符合名人的特性（他不认识其他人，但其他人都认识他）
    */
    public int findCelebrity(int n) {
        if (n < 0) {
            return -1;
        } 
        
        // 找到出度为0的人，作为候选，也就是不认识任何别人的人
        int candidate = 0;
        for (int i = 0; i < n; i++) {
            if (knows(candidate, i)) {
                candidate = i;
            }
        }
        
        // 看看是不是所有人都认识他 和 是否他不认识所有人
        for (int i = 0; i < n; i++) {
            if (i != candidate) { // 自己不和自己比较
                if (!knows(i, candidate)) { // 有人不认识他
                    return -1;
                }
                if (knows(candidate, i)) {// 他认识某人
                    return -1;
                }
            }
        }
        return candidate;
    }
}
```

Follow up: what to do if the call is really expensive? 

We can cache the result in a hashmap.

## 743 Network Delay Time

### 题目

There are `N` network nodes, labelled `1` to `N`.

Given `times`, a list of travel times as **directed** edges `times[i] = (u, v, w)`, where `u` is the source node, `v` is the target node, and `w` is the time it takes for a signal to travel from source to target.

Now, we send a signal from a certain node `K`. How long will it take for all nodes to receive the signal? If it is impossible, return `-1`.

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/05/23/931_example_1.png)

```text
Input: times = [[2,1,1],[2,3,1],[3,4,1]], N = 4, K = 2
Output: 2
```

**Note:**

1. `N` will be in the range `[1, 100]`.
2. `K` will be in the range `[1, N]`.
3. The length of `times` will be in the range `[1, 6000]`.
4. All edges `times[i] = (u, v, w)` will have `1 <= u, v <= N` and `0 <= w <= 100`.

### 分析

根据题目，从一个节点访问所有别的节点，总共需要多长时间。这个是图的BFS或者DFS做法，求从源节点到最远目标节点的最短路径，是Dijkstra类似的问题，Time O\(Nlog\(N\) + E\), Space: O\(N + E\)。

PS另外两种算法： 

Floyd–Warshall算法：Time complexity: O\(N^3\), Space complexity: O\(N^2\)

Bellman-Ford算法：Time complexity: O\(N\*E\), Space complexity: O\(N\)

### 代码

BFS实现Dijkstra，java的API需要熟悉下

```java
class Solution {
    public int networkDelayTime(int[][] times, int N, int K) {
        Map<Integer, Map<Integer, Integer>> map = new HashMap<>(); //分解二维数组，用一个hashmap来存储源节点和带遍历的目标节点+delay时间
        for (int[] time : times) { // key为源节点，value为map，其中key为目标节点，value为delay时间
            map.putIfAbsent(time[0], new HashMap<>());
            map.get(time[0]).put(time[1], time[2]);
        }
        
        // 用一个优先队列来记录从源节点向外扩散的层，每一层的目标节点根据delay time来确定
        // 目标节点-delay time
        Queue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]);
        
        // 先把参数源节点加入到优先队列，它的delay time是0
        pq.offer(new int[]{K, 0});
        
        // 因为节点是以整数表示，所以用一个数组true/false即可表示是否被访问过
        boolean[] visited = new boolean[N + 1]; //从1开始，免得标记时还需要转换
        
        int result = 0;
        
        // BFS
        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            int currNode = curr[0];
            int currDelay = curr[1];
            
            if (visited[currNode]) { // 当前节点已被访问过
                continue;
            }
            
            // 标记访问
            visited[currNode] = true;
            
            result = currDelay; //把结果更新为当前层的delay time
            
            // 处理当前层每个节点的下一层
            if (map.containsKey(currNode)) { // 防止null
                for (int neighbor : map.get(currNode).keySet()) { // 获取当前节点的所有目标节点
                    pq.offer(new int[]{neighbor, currDelay + map.get(currNode).get(neighbor)}); // 源节点到当前层delay time + 当前层到目标节点的delay time
                }
            } 
            
            N--; // 层数-1
        }
        return N == 0 ? result : -1;
    }
}
```


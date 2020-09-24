# 8. 高级数据结构和算法

大部分题面试遇到的概率非常低，了解基本概念，实践上无需死磕。少数几道很容易在面试中遇到的题下面就实现出来。

## 146 LRU Cache

### 原题概述

Design and implement a data structure for [Least Recently Used \(LRU\) cache](https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU). It should support the following operations: `get` and `put`.

`get(key)` - Get the value \(will always be positive\) of the key if the key exists in the cache, otherwise return -1.  
`put(key, value)` - Set or insert the value if the key is not already present. When the cache reached its capacity, it should invalidate the least recently used item before inserting a new item.

**Follow up:**  
Could you do both operations in **O\(1\)** time complexity?

**Example:**

```text
LRUCache cache = new LRUCache( 2 /* capacity */ );

cache.put(1, 1);
cache.put(2, 2);
cache.get(1);       // returns 1
cache.put(3, 3);    // evicts key 2
cache.get(2);       // returns -1 (not found)
cache.put(4, 4);    // evicts key 1
cache.get(1);       // returns -1 (not found)
cache.get(3);       // returns 3
cache.get(4);       // returns 4
```

### 题意和分析

要求实现 Least Recently Used \(LRU\)的缓存器，这里有公众号“程序员小灰”的对[LRU的讲解](https://mp.weixin.qq.com/s?__biz=MzIxMjE5MTE1Nw==&mid=2653195947&idx=1&sn=2954871ed1195dd3ebab0c9691e674b4&chksm=8c99fc71bbee7567c29169a86b4a2133bf87492ce5d2b7c9fdaf7740d2fc01084670b75e976a&scene=0#rd)，非常诙谐，一看就懂，主要是对哈希链表的实现，这里就不多解释了，实现两个方法，get和put， 其中get函数是通过输入key来获得value，如果成功获得，该键值对\(key, value\)就升至缓存器中最常用的位置（顶部），如果key不存在，则返回-1；put函数是插入一对新的对\(key, value\)，如果原缓存器中有该key，则需要先删除掉原有的，将新的插入到缓存器的顶部。如果不存在，则直接插入到顶部。若加入新的值后缓存器超过了容量，则需要删掉一个最不常用的值，也就是底部的值。要求二者的操作均为O\(1\)。

具体实现时创建一个hashmap和一个双向链表，保存关键值key和缓存器各项的迭代器之间映射，从而可以以O\(1\)的时间内找到目标项；另外四个私有变量，capacity是缓存器的容量大小， count是标定目前缓存中键值对个数，和哈希链表的头尾指针。然后再来看get和put如何实现，get相对简单些，我们在hash中查找给定的key，如果存在则将此项移到顶部，并返回value，若不存在返回-1。对于put，查找给定的key，如果存在就删掉原有项，并在顶部插入新来项，然后判断是否溢出，若溢出则删掉底部项\(最不常用项\)。

### 代码

```java
class LRUCache {
        
    /**
     * 1. 创建一个内部类Node，封装需要的key-value键值对的对象
     */
    class Node {
        private int key, value;
        private Node prev, next;
        public Node (int key, int value) {
            this.key = key;
            this.value = value;
        }
}

    /**
     * 2. 依靠创建的 Node 类型构建一个双链表内部类，实现几个需要的 API
     *    这些操作的时间复杂度均为 O(1)，
     *    其中，如同单链表，head和tail分别是链表的头节点和尾节点，不在数据内
     */
    class DoubleList {
        private Node head, tail;
        private int size;

        public DoubleList() {
            // 创建双链表的头尾，头尾不计入双链表的size中
            head = new Node(0, 0);
            tail = new Node(0, 0);
            head.next = tail; // 双链表的head指向tail
            tail.prev = head; // 双链表的tail指向head
            size = 0;
        }

        // 在链表头部添加节点 x，时间 O(1)
        public void addFirst(Node x) {
            // 在head和第一个node之间插入
            x.next = head.next;
            x.prev = head;
            head.next.prev = x; // 插入前的第一个node
            head.next = x; // 插入前的第一个node
            size++;
        }

        // 删除链表中的 x 节点（x 一定存在），由于是双链表且给的是目标 Node 节点，时间 O(1)
        public void remove(Node x){
            x.prev.next = x.next;
            x.next.prev = x.prev;
            size--;
        }

        // 删除链表中最后一个节点，并返回该节点，时间 O(1)
        public Node removeLast(){
            if (tail.prev == head) { // 双链表目前木有元素
                return null;
            }
            Node last = tail.prev;
            remove(last); // 直接实现的调用remove方法移除最后一个元素
            return last;
        }

        // 返回链表长度，时间 O(1)
        public int size(){
            return size;
        }
    }

    /**
     * 3. 创建一个上面创建好的双链表，结合一个新建的HashMap，实现LinkedHashMap的功能
     */
    // k-v: key -> Node(key, val)
    private HashMap<Integer, Node> map;
    // Node(k1, v1) <-> Node(k2, v2)...
    private DoubleList cache;
    // 最大容量
    private int cap;

    public LRUCache(int capacity) {
        this.cap = capacity;
        map = new HashMap<>();
        cache = new DoubleList();
    }

    public int get(int key) {
        // 如果key不存在，直接返回-1
        if (!map.containsKey(key)) {
            return -1;
        }
        // 否则用put将数据(key, value)提到开头，并返回相应的值
        int value = map.get(key).value;
        put(key, value);
        return value;
    }

    public synchronized void put(int key, int value) {
        // 先把新节点做出来
        Node x = new Node(key, value);

        // 如果key已存在，把旧的数据删除，把新的数据x插入到开头
        if (map.containsKey(key)) { 
            // 删除旧的节点，新的插到头部
            cache.remove(map.get(key));
            cache.addFirst(x);
            // 更新map中的数据
            map.put(key, x);
        } else {
            // key不存在，先检查是否有空间
            if (cap == cache.size()) {
                // 删除链表最后一个数据，腾位置
                Node last = cache.removeLast(); //设计的removeLast()返回节点
                map.remove(last.key);
            }
            // 把新节点添加到头部，map 中新建 key 对新节点 x 的映射
            cache.addFirst(x);
            map.put(key, x);
        }
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

如果用Java内置的LinkedHashMap，这个是对HashMap + Double LinkedList进行了封装，不需要自己实现数据结构了，就会简单很多，而且官方API经过各种优化比自己实现的数据结构的速度一般要快，但有时候面试不让用LinkedHashMap。

```java
class LRUCache {
    
    private volatile Map<Integer, Integer> map;
    private int cacheSize;

    public LRUCache(int initSize) {
        this.cacheSize = initSize;
        // 重写removeEldestEntry()方法，只要没有位置就移除最后一个entry
        this.map = new LinkedHashMap<Integer, Integer>(initSize, 0.75f, true) {
            @Override
            protected boolean removeEldestEntry(Map.Entry eldest) {
                return size() > LRUCache.this.cacheSize;
            }
        };
    }
    
    public int get(int inKey) {
        if (!map.containsKey(inKey)) {
            return -1;
        }
        int val = map.get(inKey);
        // 同样利用put方法将数据提前
        put(inKey, val);
        return val;
    }
    
    public synchronized void put(int key, int value) {
        // 直接利用Java中LinkedHashMap实现的put方法
        map.put(key, value);
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

## Union Find

## 547 Friend Circles

### 原题概述

There are **N** students in a class. Some of them are friends, while some are not. Their friendship is transitive in nature. For example, if A is a **direct** friend of B, and B is a **direct** friend of C, then A is an **indirect** friend of C. And we defined a friend circle is a group of students who are direct or indirect friends.

Given a **N\*N** matrix **M** representing the friend relationship between students in the class. If M\[i\]\[j\] = 1, then the ith and jth students are **direct** friends with each other, otherwise not. And you have to output the total number of friend circles among all the students.

**Example 1:**  


```text
Input: 
[[1,1,0],
 [1,1,0],
 [0,0,1]]
Output: 2
Explanation:The 0th and 1st students are direct friends, so they are in a friend circle. 
The 2nd student himself is in a friend circle. So return 2.
```

**Example 2:**  


```text
Input: 
[[1,1,0],
 [1,1,1],
 [0,1,1]]
Output: 1
Explanation:The 0th and 1st students are direct friends, the 1st and 2nd students are direct friends, 
so the 0th and 2nd students are indirect friends. All of them are in the same friend circle, so return 1.
```

**Note:**  


1. N is in range \[1,200\].
2. M\[i\]\[i\] = 1 for all students.
3. If M\[i\]\[j\] = 1, then M\[j\]\[i\] = 1.

### 题意和分析

求朋友圈的个数，题目中对于朋友圈的定义是transitive的，比如A和B是好友，B和C是好友，那么即使A和C不是好友，那么他们三人也属于一个朋友圈。

1）DFS， 对于某个人，遍历其好友，然后再遍历其好友的好友，那么就能把属于同一个朋友圈的人都遍历一遍，同时标记出已经遍历过的人，然后累积朋友圈的个数，再去对于没有遍历到的人在找其朋友圈的人，这样就能求出个数。

2）BFS，思路同DFS，较慢。

3）Union Find，这道题是并查集的典型实现，跟323  [Number of Connected Components in an Undirected Graph](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph) 和 261  [Graph Valid Tree](https://leetcode.com/problems/graph-valid-tree) 解法类似； 初始时给每一个对象都赋上不同的标签，然后对于属于同一类的对象，在root中查找其标签，如果不同，那么将其中一个对象的标签赋值给另一个对象，注意root数组中的数字跟数字的坐标是有很大关系的，root存的是属于同一组的另一个对象的坐标，这样通过getRoot函数可以使同一个组的对象返回相同的值。

### 代码

DFS

```java
class Solution {
    public int findCircleNum(int[][] M) {
        int result = 0;
        if (M == null || M.length == 0 || M[0].length == 0) {
            return result;
        }
        int[] visited = new int[M.length];
        for (int i = 0; i < M.length; i++) {// 按照行来检查，每一行是一个人
            if (visited[i] == 0) { // visited对应的为0，表示未在之前的DFS中未被访问到，自己形成一个新圈子
                dfs(M, visited, i);
                result++;
            }
        }
        return result;
    }
    
    private void dfs(int[][] M, int[] visited, int i) {
        for (int j = 0; j < M[i].length; j++) { // 将当前人物M[i]的朋友通过dfs找出来，并标记
            if (M[i][j] == 1 && visited[j] == 0) {// 是朋友，且没有被访问过
                visited[j] = 1;
                dfs(M, visited, j); // 再根据朋友的朋友继续深搜
            }
        }
    }
}
```

BFS

```java
class Solution {
    public int findCircleNum(int[][] M) {
        int result = 0;
        if (M == null || M.length == 0 || M[0].length == 0) {
            return result;
        }
        for (int i = 0; i < M.length; i++) { // 依然按行检查
            if (M[i][i] == 1) { // BFS的过程中尚未被加入到之前的圈子中，这时候”自成一圈“
                bfs(M, i);
                result++;
            }
        }
        return result;
    }
    
    private void bfs(int[][] M, int oneGuy) {
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(oneGuy);
        
        while (!queue.isEmpty()) {
            int size = queue.size(); //当前的bfs圈
            for (int i = 0; i < size; i++) {
                int anotherGuy = queue.poll();
                M[anotherGuy][anotherGuy] = 2; // 先把找出来的在当前朋友圈中的人标记一下
                
                // 把找出来的人的朋友加入到队列中，以后分析
                for (int x = 0; x < M[anotherGuy].length; x++) {
                    if (M[anotherGuy][x] == 1 && M[x][x] != 2) { //是朋友，并且没有被标记过
                        queue.offer(x);
                    }
                }
            }
        }
    }
}
```

Union Find

```java
class Solution {
    public int findCircleNum(int[][] M) {
        int len = M.length;
        if (len == 0) {
            return 0;
        }
        
        // init，初始各人是各人的朋友圈
        int[] parent = new int[len];
        for (int k = 0; k < len; k++) {
            parent[k] = k;
        }
        
        int result = len; // 初始每个人是一个朋友圈，共len个
        
        for (int i = 0; i < len; i++) { // 遍历每一个人
            for (int j = i + 1; j < len; j++) { // 只检查当前i的斜线右边的横排
                if (M[i][j] == 0) { // i和j不是朋友
                    continue;
                }
                
                if (find(i, parent) == find(j, parent)) { // 已经在一个朋友圈
                    continue;
                }
                
                union(i, j, parent); // M[i][j] == 1，是朋友
                result--; // 能union一个，减少一个最初为len的朋友圈
            }
        }
        return result;
    }
    
    // 以下为Union Find的状态压缩代码
    private int find(int p, int[] parent) {
        if (parent[p] == p) {
            return p;
        }
        parent[p] = find(parent[p], parent); // UF的路径压缩
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

## 1\) 红黑树

## 2\) B-Tree

## 3\) 斐波那契堆

## 4\) 计算几何

## 5\) 数论

## 6\) 傅立叶变换FFT

7\)

8\)


# 7. 高级数据结构和算法

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

    class DLinkedNode {
        int key;
        int value;
        DLinkedNode next;
        DLinkedNode pre;
    }

    /**
     * Always add the new node right after head
     */
    private void addNode(DLinkedNode node) {
        node.pre = head;
        node.next = head.next;
        head.next.pre = node;
        head.next = node;
    }

    /**
     * Removing an existing node from the doulble linked list
     */
    private void removeNode(DLinkedNode node) {
        DLinkedNode pre = node.pre;
        DLinkedNode next = node.next;

        pre.next = next;
        next.pre = pre;
    }

    /**
     * Move certain node in between to the head.
     */
    private void moveToHead(DLinkedNode node){
        this.removeNode(node);
        this.addNode(node);
    }

    /**
     * Removing the tail node
     */
    private DLinkedNode removeTailNode() {
        DLinkedNode node = tail.pre;
        this.removeNode(node);
        return node;
    }

    /**
     * 定义需要的变量
     */
    //线程安全用HashTable
    private HashMap<Integer, DLinkedNode> cache = new HashMap<Integer, DLinkedNode>();
    private int count;
    private int capacity;
    private DLinkedNode head, tail;

    public LRUCache(int capacity) {
        this.count = 0;
        this.capacity = capacity;

        head = new DLinkedNode();
        head.pre = null;

        tail = new DLinkedNode();
        tail.next = null;

        head.next = tail;
        tail.pre = head;
    }

    public int get(int key) {
        DLinkedNode node = cache.get(key);
        if (node == null) {//cache中不存在该key，返回-1
            return -1;
        }

        this.moveToHead(node);

        return node.value;
    }

    public void put(int key, int value) {
        DLinkedNode node = cache.get(key);
        if (node == null) {//插入的时候没有该键值对，新插入

            DLinkedNode newNode = new DLinkedNode();
            newNode.key = key;
            newNode.value = value;

            this.cache.put(key, newNode);
            this.addNode(newNode);

            count++;

            if (count > capacity) {//这时候需要删除tail的值
                DLinkedNode tail = this.removeTailNode();
                this.cache.remove(tail.key);
                count--;
            }
        } else {//还有空位就直接插入
            node.value = value;
            this.moveToHead(node);
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



### 代码

## 1\) 红黑树

## 2\) B-Tree

## 3\) 斐波那契堆

## 4\) 计算几何

## 5\) 数论

## 6\) 傅立叶变换FFT

7\)

8\)


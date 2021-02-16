# Linkedin

## 339 Nested List Weight Sum 75.2% Easy $

嵌套数组深度为权重求和，由外向内 DFS

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/dfs\#339-nested-list-weight-sum" %}

## 364 Nested List Weight Sum II 63.3% Medium $

嵌套数组深度为权重求和II \(与339不同的地方是权重的计算方式，array最里面为1，最外面为最大\) - DFS/BFS

DFS, The idea is to deduct number depth - level times. For example, 1x + 2y + 3z = \(3 + 1\) \* \(x + y + z\) - \(3x + 2y + z\); So we can convert this problem to Nested List Weight Sum I and just record max depth and flat sum at the same time.

```java
class Solution {
    public int depthSumInverse(List<NestedInteger> nestedList) {
        int depthSum = dfs(nestedList, 1);
        return flatSum * (max + 1) - depthSum;
    }

    int flatSum = 0;
    int max = 1;
    private int dfs(List<NestedInteger> input, int depth) {
        if (input == null || input.size() == 0) return 0;
        int sum = 0;
        for (NestedInteger i : input) {
            if (i.isInteger()) {
                max = Math.max(depth, max);
                sum += i.getInteger() * depth;
                flatSum += i.getInteger();
            } else {
                sum += dfs(i.getList(), depth + 1);
            }
        }
        return sum;
    }
}
```

BFS

```java
class Solution {
    public int depthSumInverse(List<NestedInteger> nestedList) {
       Queue<NestedInteger> q = new LinkedList(nestedList);
       int prevSum = 0, total = 0;
       while(!q.isEmpty()){
           int size = q.size();
           for(int i = 0; i < size; ++i){
               NestedInteger ni = q.poll();
               if(ni.isInteger()){
                   prevSum += ni.getInteger();
               }else{
                   q.addAll(ni.getList());
               }
           }
           total += prevSum;
       }
       return total;
    }
}
```

## 243 Shortest Word Distance 61.7% Easy $

Array中找单词距离

```java
class Solution {
    public int shortestDistance(String[] words, String word1, String word2) {
        int res = words.length;
        Map<String, Integer> map = new HashMap<>();
        for (int i = 0; i < words.length; i++) {
            String str = words[i];
            if (str.equals(word1) && map.containsKey(word2)) {
                res = Math.min(res, i - map.get(word2));
            } else if (str.equals(word2) && map.containsKey(word1)) {
                res = Math.min(res, i - map.get(word1));
            }
            map.put(str, i);
        }
        return res;
    }
}
```

不用HashMap，O\(m \* n\)

```java
class Solution {
    public int shortestWordDistance(String[] words, String word1, String word2) {
        int index = -1;
        int min = words.length;
        for (int i = 0; i < words.length; i++) {
            if (words[i].equals(word1) || words[i].equals(word2)) {
                if (index != -1 && !words[index].equals(words[i])) {
                    min = Math.min(i - index, min);
                }
                index = i;
            }
        }
        return min;
    }
}
```

## 244 Shortest Word Distance II 53.3% Medium $

设计一个API，这个API会被调用很多次

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table\#244-shortest-word-distance-ii-usd" %}

## 245 Shortest Word Distance III $

两个单词可能相同，array中也可能有重复的单词，从243中第二个办法修改

```java
class Solution {
    public int shortestWordDistance(String[] words, String word1, String word2) {
        int index = -1;
        int min = words.length;
        for (int i = 0; i < words.length; i++) {
            if (words[i].equals(word1) || words[i].equals(word2)) {
                if (index != -1 && (word1.equals(word2) || !words[index].equals(words[i]))) {
                    min = Math.min(i - index, min);
                }
                index = i;
            }
        }
        return min;
    }
}
```

## 170 Two Sum III - Data structure design 34.6% Easy $

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/array/ksum\#170-two-sum-iii-data-structure-design" %}

## 272 Closest Binary Search Tree Value II 51.6% Hard $

BST中，找到给定的k个离target的最近的结点的数 （270是找一个）

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#272-closest-binary-search-tree-value-ii" %}

## 156 Binary Tree Upside Down 55.9% Medium $

Given the `root` of a binary tree, turn the tree upside down and return _the new root_.

You can turn a binary tree upside down with the following steps:

1. The original left child becomes the new root.
2. The original root becomes the new right child.
3. The original right child becomes the new left child.

![](https://assets.leetcode.com/uploads/2020/08/29/main.jpg)

The mentioned steps are done level by level, it is **guaranteed** that every node in the given tree has either **0 or 2 children**.

**Example 1:**![](https://assets.leetcode.com/uploads/2020/08/29/updown.jpg)

```text
Input: root = [1,2,3,4,5]
Output: [4,5,2,null,null,3,1]
```

**Example 2:**

```text
Input: root = []
Output: []
```

**Example 3:**

```text
Input: root = [1]
Output: [1]
```

**Constraints:**

* The number of nodes in the tree will be in the range `[0, 10]`.
* `1 <= Node.val <= 10`
* `Every node has either 0 or 2 children.`

类似206翻转链表，可以递归和迭代来实现递归

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
    public TreeNode upsideDownBinaryTree(TreeNode root) {
        if(root == null || root.left == null) {
            return root;
        }

        TreeNode newRoot = upsideDownBinaryTree(root.left);
        root.left.left = root.right;   // node 2 left children
        root.left.right = root;         // node 2 right children
        root.left = null;
        root.right = null;
        return newRoot;
    }
    public ListNode reverseList(ListNode head) {
            if (head == null || head.next == null) return head;
            ListNode newHead = reverseList(head.next);
            head.next.next = head;
            head.next = null;
            return newHead;
    }
}
```

迭代

```java
class Solution {
    public TreeNode upsideDownBinaryTree(TreeNode root) {
        TreeNode curr = root;
        TreeNode next = null;
        TreeNode temp = null;
        TreeNode prev = null;

        while(curr != null) {
            next = curr.left;

            // swapping nodes now, need temp to keep the previous right child
            curr.left = temp;
            temp = curr.right;
            curr.right = prev;

            prev = curr;
            curr = next;
        }
        return prev;
    }  

    public ListNode reverseList(ListNode head) {
            ListNode pre = null;
            ListNode cur = head;
            while (cur != null) {
                ListNode next = cur.next;
                cur.next = pre;
                pre = cur;
                cur = next;
            }

            return pre;
    }
}
```

## 716 Max Stack 42.9% Easy $

Design a max stack data structure that supports the stack operations and supports finding the stack's maximum element.

Implement the `MaxStack` class:

* `MaxStack()` Initializes the stack object.
* `void push(int x)` Pushes element `x` onto the stack.
* `int pop()` Removes the element on top of the stack and returns it.
* `int top()` Gets the element on the top of the stack without removing it.
* `int peekMax()` Retrieves the maximum element in the stack without removing it.
* `int popMax()` Retrieves the maximum element in the stack and removes it. If there is more than one maximum element, only remove the **top-most** one.

两个Stacks， Time Complexity: O\(N\) for the `popMax` operation, and O\(1\) for the other operations, where N is the number of operations performed. Space Complexity: O\(N\), the maximum size of the stack

```java
class MaxStack {
    Stack<Integer> stack;
    Stack<Integer> maxStack;

    public MaxStack() {
        stack = new Stack();
        maxStack = new Stack();
    }

    public void push(int x) {
        int max = maxStack.isEmpty() ? x : maxStack.peek();
        maxStack.push(max > x ? max : x);
        stack.push(x);
    }

    public int pop() {
        maxStack.pop();
        return stack.pop();
    }

    public int top() {
        return stack.peek();
    }

    public int peekMax() {
        return maxStack.peek();
    }

    public int popMax() {
        int max = peekMax();
        Stack<Integer> buffer = new Stack();
        while (top() != max) buffer.push(pop());
        pop();
        while (!buffer.isEmpty()) push(buffer.pop());
        return max;
    }
}
```

Double Linked List + TreeMap，Time Complexity: O\(logN\) for all operations except `peek` which is O\(1\), where N is the number of operations performed. Most operations involving `TreeMap` are O\(logN\)； Space Complexity: O\(N\), the size of the data structures used.

```java
class MaxStack {
    TreeMap<Integer, List<Node>> map;
    DoubleLinkedList dll;

    public MaxStack() {
        map = new TreeMap();
        dll = new DoubleLinkedList();
    }

    public void push(int x) {
        Node node = dll.add(x);
        if(!map.containsKey(x))
            map.put(x, new ArrayList<Node>());
        map.get(x).add(node);
    }

    public int pop() {
        int val = dll.pop();
        List<Node> L = map.get(val);
        L.remove(L.size() - 1);
        if (L.isEmpty()) map.remove(val);
        return val;
    }

    public int top() {
        return dll.peek();
    }

    public int peekMax() {
        return map.lastKey();
    }

    public int popMax() {
        int max = peekMax();
        List<Node> L = map.get(max);
        Node node = L.remove(L.size() - 1);
        dll.unlink(node);
        if (L.isEmpty()) map.remove(max);
        return max;
    }
}

class DoubleLinkedList {
    Node head, tail;

    public DoubleLinkedList() {
        head = new Node(0);
        tail = new Node(0);
        head.next = tail;
        tail.prev = head;
    }

    public Node add(int val) {
        Node x = new Node(val);
        x.next = tail;
        x.prev = tail.prev;
        tail.prev = tail.prev.next = x;
        return x;
    }

    public int pop() {
        return unlink(tail.prev).val;
    }

    public int peek() {
        return tail.prev.val;
    }

    public Node unlink(Node node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
        return node;
    }
}

class Node {
    int val;
    Node prev, next;
    public Node(int v) {val = v;}
}
```

## 149 Max Points on a Line 17.2% Hard

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/math\#149-max-point-on-a-line" %}

## 366 Find Leaves of Binary Tree 71.4% Medium $

逐层移除叶子结点

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#366-find-leaves-of-binary-tree" %}

## 254 Factor Combinations 47.1% Medium $

Numbers can be regarded as product of its factors. For example,

```text
8 = 2 x 2 x 2;
  = 2 x 4.
```

Write a function that takes an integer n and return all possible combinations of its factors.

**Note:**

1. You may assume that n is always positive.
2. Factors should be greater than 1 and less than n.

**Example 1:**

```text
Input: 1
Output: []
```

**Example 2:**

```text
Input: 37
Output:[]
```

**Example 3:**

```text
Input: 12
Output:
[
  [2, 6],
  [2, 2, 3],
  [3, 4]
]
```

**Example 4:**

```text
Input: 32
Output:
[
  [2, 16],
  [2, 2, 8],
  [2, 2, 2, 4],
  [2, 2, 2, 2, 2],
  [2, 4, 4],
  [4, 8]
]
```

利用DFS

```java
class Solution {
    public List<List<Integer>> getFactors(int n) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        helper(result, new ArrayList<Integer>(), n, 2);
        return result;
    }

    public void helper(List<List<Integer>> result, List<Integer> item, int n, int start){
        if (n <= 1) {
            if (item.size() > 1) {
                result.add(new ArrayList<Integer>(item));
            }
            return;
        }

        for (int i = start; i <= n; ++i) {
            if (n % i == 0) {
                item.add(i);
                helper(result, item, n/i, i);
                item.remove(item.size()-1);
            }
        }
    }
}
```

## 256 Paint House 53.0% Medium $

三种颜色

There is a row of n houses, where each house can be painted one of three colors: red, blue, or green. The cost of painting each house with a certain color is different. You have to paint all the houses such that no two adjacent houses have the same color.

The cost of painting each house with a certain color is represented by a `n x 3` cost matrix. For example, `costs[0][0]` is the cost of painting house 0 with the color red; `costs[1][2]` is the cost of painting house 1 with color green, and so on... Find the minimum cost to paint all houses.

**Example 1:**

```text
Input: costs = [[17,2,17],[16,16,5],[14,3,19]]
Output: 10
Explanation: Paint house 0 into blue, paint house 1 into green, paint house 2 into blue.
Minimum cost: 2 + 5 + 3 = 10.
```

**Example 2:**

```text
Input: costs = []
Output: 0
```

**Example 3:**

```text
Input: costs = [[7,6,2]]
Output: 2
```

**Constraints:**

* `costs.length == n`
* `costs[i].length == 3`
* `0 <= n <= 100`
* `1 <= costs[i][j] <= 20`

记忆化搜索

```java
class Solution {

    private int[][] costs;
    private Map<String, Integer> memo;

    public int minCost(int[][] costs) {
        if (costs.length == 0) {
            return 0;
        }
        this.costs = costs;
        this.memo = new HashMap<>();
        return Math.min(paintCost(0, 0), Math.min(paintCost(0, 1), paintCost(0, 2)));
    }

    private int paintCost(int n, int color) {
        if (memo.containsKey(getKey(n, color))) {
            return memo.get(getKey(n, color));   
        }
        int totalCost = costs[n][color];
        if (n == costs.length - 1) {
        } else if (color == 0) { // Red
            totalCost += Math.min(paintCost(n + 1, 1), paintCost(n + 1, 2));
        } else if (color == 1) { // Green
            totalCost += Math.min(paintCost(n + 1, 0), paintCost(n + 1, 2));
        } else { // Blue
            totalCost += Math.min(paintCost(n + 1, 0), paintCost(n + 1, 1));
        }        
        memo.put(getKey(n, color), totalCost);

        return totalCost;
    }

    private String getKey(int n, int color) {
        return String.valueOf(n) + " " + String.valueOf(color);
    }
}
```

DP

```java
class Solution {
    public int minCost(int[][] costs) {

        for (int n = costs.length - 2; n >= 0; n--) {
            // Total cost of painting the nth house red.
            costs[n][0] += Math.min(costs[n + 1][1], costs[n + 1][2]);
            // Total cost of painting the nth house green.
            costs[n][1] += Math.min(costs[n + 1][0], costs[n + 1][2]);
            // Total cost of painting the nth house blue.
            costs[n][2] += Math.min(costs[n + 1][0], costs[n + 1][1]);
        }

        if (costs.length == 0) return 0;   

        return Math.min(Math.min(costs[0][0], costs[0][1]), costs[0][2]);
    }
}
```

DP + 状态压缩

```java

/* This code OVERWRITES the input array! */

class Solution {
    public int minCost(int[][] costs) {

        if (costs.length == 0) return 0;

        int[] previousRow = costs[costs.length -1];

        for (int n = costs.length - 2; n >= 0; n--) {

            /* PROBLEMATIC CODE IS HERE
             * This line here is NOT making a copy of the original, it's simply
             * making a reference to it Therefore, any writes into currentRow
             * will also be written into "costs". This is not what we wanted!
             */
            int[] currentRow = costs[n];

            // Total cost of painting the nth house red.
            currentRow[0] += Math.min(previousRow[1], previousRow[2]);
            // Total cost of painting the nth house green.
            currentRow[1] += Math.min(previousRow[0], previousRow[2]);
            // Total cost of painting the nth house blue.
            currentRow[2] += Math.min(previousRow[0], previousRow[1]);
            previousRow = currentRow;
        }  

        return Math.min(Math.min(previousRow[0], previousRow[1]), previousRow[2]);
    }
}
```

## 265 Paint House II 45.3% Hard $

k种颜色

There are a row of n houses, each house can be painted with one of the k colors. The cost of painting each house with a certain color is different. You have to paint all the houses such that no two adjacent houses have the same color.

The cost of painting each house with a certain color is represented by a `n x k` cost matrix. For example, `costs[0][0]` is the cost of painting house 0 with color 0; `costs[1][2]` is the cost of painting house 1 with color 2, and so on... Find the minimum cost to paint all houses.

**Note:**  
All costs are positive integers.

**Example:**

```text
Input: [[1,5,3],[2,9,4]]
Output: 5
Explanation: Paint house 0 into color 0, paint house 1 into color 2. Minimum cost: 1 + 4 = 5; 
             Or paint house 0 into color 2, paint house 1 into color 0. Minimum cost: 3 + 2 = 5. 
```

**Follow up:**  
Could you solve it in O\(nk\) runtime?

记忆化搜索

```java
class Solution {

    private int n;
    private int k;
    private int[][] costs;
    private Map<String, Integer> memo;

    public int minCostII(int[][] costs) {
        if (costs.length == 0) return 0;
        this.k = costs[0].length;
        this.n = costs.length;
        this.costs = costs;
        this.memo = new HashMap<>();
        int minCost = Integer.MAX_VALUE;
        for (int color = 0; color < k; color++) {
            minCost = Math.min(minCost, memoSolve(0, color));
        }
        return minCost;
    }

    private int memoSolve(int houseNumber, int color) {

        // Base case: There are no more houses after this one.
        if (houseNumber == n - 1) {
            return costs[houseNumber][color];
        }

        // Memoization lookup case: Have we already solved this subproblem?
        if (memo.containsKey(getKey(houseNumber, color))) {
            return memo.get(getKey(houseNumber, color));
        }

        // Recursive case: Determine the minimum cost for the remainder.
        int minRemainingCost = Integer.MAX_VALUE;
        for (int nextColor = 0; nextColor < k; nextColor++) {
            if (color == nextColor) continue;
            int currentRemainingCost = memoSolve(houseNumber + 1, nextColor);
            minRemainingCost = Math.min(currentRemainingCost, minRemainingCost);
        }
        int totalCost = costs[houseNumber][color] + minRemainingCost;
        memo.put(getKey(houseNumber, color), totalCost);
        return totalCost;
    }

    // Convert a house number and color into a simple string key for the memo.
    private String getKey(int n, int color) {
        return String.valueOf(n) + " " + String.valueOf(color);
    }
}
```

朴素DP

```java
class Solution {

    public int minCostII(int[][] costs) {

        if (costs.length == 0) return 0;
        int k = costs[0].length;
        int n = costs.length;

        for (int house = 1; house < n; house++) {
            for (int color = 0; color < k; color++) {
                int min = Integer.MAX_VALUE;
                for (int previousColor = 0; previousColor < k; previousColor++) {
                    if (color == previousColor) continue;
                    min = Math.min(min, costs[house - 1][previousColor]);
                }
                costs[houseNumber][color] += min;
            }
        }

        // Find the minimum in the last row.
        int min = Integer.MAX_VALUE;
        for (int c : costs[n - 1]) {
            min = Math.min(min, c);
        }
        return min;
    }
}
```

DP + 时间空间优化

```java
class Solution {

    public int minCostII(int[][] costs) {

        if (costs.length == 0) return 0;
        int k = costs[0].length;
        int n = costs.length;


        /* Firstly, we need to determine the 2 lowest costs of
         * the first row. We also need to remember the color of
         * the lowest. */
        int prevMin = -1; int prevSecondMin = -1; int prevMinColor = -1;
        for (int color = 0; color < k; color++) {
            int cost = costs[0][color];
            if (prevMin == -1 || cost < prevMin) {
                prevSecondMin = prevMin;
                prevMinColor = color;
                prevMin = cost;
            } else if (prevSecondMin == -1 || cost < prevSecondMin) {
                prevSecondMin = cost;
            }
        }

        // And now, we need to work our way down, keeping track of the minimums.
        for (int house = 1; house < n; house++) {
            int min = -1; int secondMin = -1; int minColor = -1;
            for (int color = 0; color < k; color++) {
                // Determine the cost for this cell (without writing it in).
                int cost = costs[house][color];
                if (color == prevMinColor) {
                    cost += prevSecondMin;
                } else {
                    cost += prevMin;
                }
                // Determine whether or not this current cost is also a minimum.
                if (min == -1 || cost < min) {
                    secondMin = min;
                    minColor = color;
                    min = cost;
                } else if (secondMin == -1 || cost < secondMin) {
                    secondMin = cost;
                }
            }
            // Transfer current mins to be previous mins.
            prevMin = min;
            prevSecondMin = secondMin;
            prevMinColor = minColor;
        }

        return prevMin;
    }
}
```

## 65 Valid Number 15.6% Hard

字符串判断是否是decimal

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#65-valid-number" %}

## 432 All O\`one Data Structure 33.0% Hard

操作的APIs严格时间为O\(1\)的数据结构

1. Inc\(Key\) - Inserts a new key with value 1. Or increments an existing key by 1. Key is guaranteed to be a **non-empty** string.
2. Dec\(Key\) - If Key's value is 1, remove it from the data structure. Otherwise decrements an existing key by 1. If the key does not exist, this function does nothing. Key is guaranteed to be a **non-empty** string.
3. GetMaxKey\(\) - Returns one of the keys with maximal value. If no element exists, return an empty string `""`.
4. GetMinKey\(\) - Returns one of the keys with minimal value. If no element exists, return an empty string `""`.

Java双向链表 + HashMap

```java
package util;

import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;

/**
 * @author qiminghao
 * @version 1.0.0
 * @ClassName AllOne.java
 * @Description 432. All O`one Data Structure
 * @createTime 2020/1/8 4:51
 */
public class AllOne {

    // map1保存key-.value 的映射
    private Map<String, Integer> map1;
    // map2保存val->keys 的映射， DLinkedNode为双向链表节点
    // map2的作用是为了O(1)时间拿到统计次数对应的链表节点
    // 链表中的所有操作只会涉及到前一个节点或者后一个节点，时间也为O(1)
    private Map<Integer, DLinkedNode> map2;
    // 双向链表的头， 双向链表从head到tail的value值依次减小
    private DLinkedNode head;
    // 双向链表的尾
    private DLinkedNode tail;

    /** Initialize your data structure here. */
    public AllOne() {
        map1 = new HashMap<>();
        map2 = new HashMap<>();
        head = new DLinkedNode(0);
        tail = new DLinkedNode(0);
        head.next = tail;
        tail.pre = head;
    }

    /** Inserts a new key <Key> with value 1. Or increments an existing key by 1. */
    public void inc(String key) {
        // 如果map1中包含key
        if (map1.containsKey(key)) {
            int val = map1.get(key);
            map1.put(key, val + 1);
            // 根据value拿到次数更新前的node
            DLinkedNode node = map2.get(val);
            // value加一后，从原node的Set中删除key
            node.keys.remove(key);
            DLinkedNode preNode = node.pre;
            // 当前一个node为head或前一个node的次数统计大于val+1时，
            // 表示还目前没有统计次数为val+1的key，
            // 此时应该新建一个DLinkedNode，将newNode插入到preNode和node之间，并把key加入到newNode的保存key的Set中
            // 同时，将新的统计次数（val+1）和新节点newNode的映射加入到map2中
            if (preNode == head || preNode.val > val + 1) {
                DLinkedNode newNode = new DLinkedNode(val + 1);
                newNode.keys.add(key);
                newNode.next = node;
                newNode.pre = preNode;
                preNode.next = newNode;
                node.pre = newNode;
                map2.put(val + 1, newNode);
                preNode = newNode;
            } else {    // 如果当前已经有统计次数为val+1的节点，只需key加入到Set中即可
                preNode.keys.add(key);
            }
            // 如果原节点在移除key后size为0，则删除该节点，并在map2中删除val->node的映射
            if (node.keys.size() == 0) {
                preNode.next = node.next;
                node.next.pre = preNode;
                map2.remove(val);
            }
        } else {    // map1中不包含key
            map1.put(key, 1);
            DLinkedNode node = map2.get(1);
            // 如果当前没有统计次数为1的节点，则新建节点并插入到双向链表的尾部，因为统计次数最小为1
            // 并将1->newNode的映射加入到map2中
            if (node == null) {
                DLinkedNode newNode = new DLinkedNode(1);
                newNode.keys.add(key);
                newNode.next = tail;
                newNode.pre = tail.pre;
                tail.pre.next = newNode;
                tail.pre = newNode;
                map2.put(1, newNode);
            } else {
                node.keys.add(key);
            }
        }
    }

    /** Decrements an existing key by 1. If Key's value is 1, remove it from the data structure. */
    public void dec(String key) {
        // 如果map1中包含key,进行处理，否则不做任何操作
        if (map1.containsKey(key)) {
            // 获取当前统计次数
            int val = map1.get(key);
            // 当前统计次数对应的节点
            DLinkedNode node = map2.get(val);
            // 从节点的keys set中移除当前key
            node.keys.remove(key);
            // 如果原统计次数为1，从map1中移除当前key
            if (val == 1) {
                map1.remove(key);
            } else {
                // 更新map1中的统计次数
                map1.put(key, val - 1);
                // 拿到当前节点的下一个节点
                DLinkedNode nextNode = node.next;
                // 如果下一个节点为链表尾部或下一个节点的统计次数小于val-1
                // 则新建一个节点，统计次数为val-1，将当前key加入到keys Set中
                // 并将新节点插入到当前节点的后面，同时更新map2
                if (nextNode == tail || nextNode.val < val - 1) {
                    DLinkedNode newNode = new DLinkedNode(val - 1);
                    newNode.keys.add(key);
                    newNode.pre = node;
                    newNode.next = nextNode;
                    node.next = newNode;
                    nextNode.pre = newNode;
                    map2.put(val - 1, newNode);
                } else {    // 下一个节点的统计次数为val-1，将key加到下一节点的keys Set中
                    nextNode.keys.add(key);
                }
            }
            // 如果当前节点只包含这一个key，删除后size为0，则将当前节点删除，并更新map2
            if (node.keys.size() == 0) {
                node.pre.next = node.next;
                node.next.pre = node.pre;
                map2.remove(val);
            }
        }
    }

    /** Returns one of the keys with maximal value. */
    public String getMaxKey() {
        // 按照双向链表的定义，如果链表中存在节点（head和tail不算，dummy节点），则对应最大value的keys为head的下一个节点
        if (head.next == tail) {
            return "";
        } else {
            return head.next.keys.iterator().next();
        }
    }

    /** Returns one of the keys with Minimal value. */
    public String getMinKey() {
        // 按照双向链表的定义，如果链表中存在节点（head和tail不算，dummy节点），则对应最小value的keys为tail的前一个节点
        if (tail.pre == head) {
            return "";
        } else {
            return tail.pre.keys.iterator().next();
        }
    }

    private class DLinkedNode {
        int val;
        Set<String> keys;
        DLinkedNode pre, next;
        public DLinkedNode(int val) {
            this.val = val;
            this.keys = new HashSet<>();
        }
    }
}

/**
 * Your AllOne object will be instantiated and called as such:
 * AllOne obj = new AllOne();
 * obj.inc(key);
 * obj.dec(key);
 * String param_3 = obj.getMaxKey();
 * String param_4 = obj.getMinKey();
 */
```

## 380 Insert Delete GetRandom O\(1\) 48.4% Medium

所有操作平均时间复杂度为O\(1\)

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/design\#380-insert-delete-getrandom-o-1" %}

## 381 Insert Delete GetRandom O\(1\) - Duplicates allowed 34.6% Hard

所有操作平均时间复杂度为O\(1\)，并且有重复数，用HashMap

{% embed url="https://leetcode-cn.com/problems/insert-delete-getrandom-o1-duplicates-allowed/solution/o1-shi-jian-cha-ru-shan-chu-he-huo-qu-sui-ji-yua-5/" %}

## 68 Text Justification 29.0% Hard

文本左右对齐

{% embed url="https://leetcode-cn.com/problems/text-justification/solution/68-wen-ben-zuo-you-dui-qi-by-acw\_weian/" %}

## 341 Flatten Nested List Iterator 54.0% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/stack\#341-flatten-nested-list-iterator" %}

## 150 Evaluate Reverse Polish Notation 37.4% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/stack\#150-evaluate-reverse-polish-notation" %}

## 205 Isomorphic Strings 40.3% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table\#205-isomorphic-strings" %}

## 730 Count Different Palindromic Subsequences 43.2% Hard

 给定一个字符串 S，找出 S 中不同的非空回文子序列个数，并**返回该数字与 `10^9 + 7` 的模。**DP

{% embed url="https://leetcode-cn.com/problems/count-different-palindromic-subsequences/solution/tong-ji-bu-tong-hui-wen-zi-zi-fu-chuan-by-leetcode/" %}

## 297 Serialize and Deserialize Binary Tree 49.1% Hard

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#297-serialize-and-deserialize-binary-tree" %}

## 50 Pow\(x, n\) 30.7% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/er-fen-sou-suo\#50-power-x-n" %}

## 611 Valid Triangle Number 48.9% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/array/array-zhong-de-shu-zi-zu-cheng-san-jiao-xing\#611-valid-triangle-number" %}

## 53 Maximum Subarray 47.4% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/v/master/leetcode/array/zi-shu-zu-subarray\#53-maximum-subarray" %}

##  187 Repeated DNA Sequences 41.1% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled\#187-repeated-dna-sequence" %}

## 152 Maximum Product Subarray 32.5% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zi-shu-zu-subarray\#152-maximum-product-subarray" %}

## 261 Graph Valid Tree 42.8% Medium $

Given `n` nodes labeled from `0` to `n-1` and a list of undirected edges \(each edge is a pair of nodes\), write a function to check whether these edges make up a valid tree.

**Example 1:**

```text
Input: n = 5, and edges = [[0,1], [0,2], [0,3], [1,4]]
Output: true
```

**Example 2:**

```text
Input: n = 5, and edges = [[0,1], [1,2], [2,3], [1,3], [1,4]]
Output: false
```

**Note**: you can assume that no duplicate edges will appear in `edges`. Since all edges are undirected, `[0,1]` is the same as `[1,0]` and thus will not appear together in `edges`.

图的DFS

```java
class Solution {
    public boolean validTree(int n, int[][] edges) {
        // initialize adjacency list
        List<List<Integer>> adjList = new ArrayList<List<Integer>>(n);
        
        // initialize vertices
        for (int i = 0; i < n; i++)
            adjList.add(i, new ArrayList<Integer>());
        
        // add edges    
        for (int i = 0; i < edges.length; i++) {
            int u = edges[i][0], v = edges[i][1];
            adjList.get(u).add(v);
            adjList.get(v).add(u);
        }
        
        boolean[] visited = new boolean[n];
        
        // make sure there's no cycle
        if (hasCycle(adjList, 0, visited, -1))
            return false;
        
        // make sure all vertices are connected
        for (int i = 0; i < n; i++) {
            if (!visited[i]) 
                return false;
        }
        
        return true;
    }
    
    // check if an undirected graph has cycle started from vertex u
    boolean hasCycle(List<List<Integer>> adjList, int u, boolean[] visited, int parent) {
        visited[u] = true;
        
        for (int i = 0; i < adjList.get(u).size(); i++) {
            int v = adjList.get(u).get(i);
            
            if ((visited[v] && parent != v) || (!visited[v] && hasCycle(adjList, v, visited, u)))
                return true;
        }
        
        return false;
    }
}
```

BFS

```java
class Solution {
    public boolean validTree(int n, int[][] edges) {
        int[] visited = new int[n];
        List<List<Integer>> adjList = new ArrayList<>();
        for (int i=0; i<n; ++i) { adjList.add(new ArrayList<Integer>()); }
        for (int[] edge: edges) {
            adjList.get(edge[0]).add(edge[1]);
            adjList.get(edge[1]).add(edge[0]);
        }
        if (hasCycle(-1, 0, visited, adjList)) { return false; }  // has cycle
        for (int v: visited) { if (v == 0) { return false; } }  // not 1 single connected component
        return true;
    }
    
    private boolean hasCycle(int pred, int vertex, int[] visited, List<List<Integer>> adjList) {
        visited[vertex] = 1;  // current vertex is being visited
        for (Integer succ: adjList.get(vertex)) {  // successors of current vertex
            if (succ != pred) {  // exclude current vertex's predecessor
                if (visited[succ] == 1) { return true; }  // back edge/loop detected!
                else if (visited[succ] == 0) {
                    if (hasCycle(vertex, succ, visited, adjList)) { return true; }
                }
            }
        }
        visited[vertex] = 2;
        return false;
    }
}
```

Union-Find with path compression and merge by rank

```java
class Solution {
    
    class UnionFind {
        
        int[] parent;
        int[] rank;
        int count;
        
        UnionFind(int n) {
            parent = new int[n];
            rank = new int[n];
            count = n;  // number of components
            for (int i=0; i<n; ++i) { parent[i] = i; }  // initially, each node's parent is itself.
        }
        
        int find(int x) {
            if (x != parent[x]) {
                parent[x] = find(parent[x]);  // find root with path compression
            }
            return parent[x];
        }
        
        boolean union(int x, int y) {
            int X = find(x), Y = find(y);
            if (X == Y) { return false; }
            if (rank[X] > rank[Y]) { parent[Y] = X; }  // tree Y is lower
            else if (rank[X] < rank[Y]) { parent[X] = Y; }  // tree X is lower
            else {  // same height
                parent[Y] = X;
                ++rank[X];
            }
            --count;
            return true;
        }
    }
    
    public boolean validTree(int n, int[][] edges) {
        UnionFind uf = new UnionFind(n);
        for (int[] edge: edges) {
            int x = edge[0], y = edge[1];
            if (!uf.union(x, y)) { return false; }  // loop detected
        }
        return uf.count == 1;
    }
}
```

## 698 Partition to K Equal Sum Subsets 45.5% Medium

 给定一个整数数组  `nums` 和一个正整数 `k`，找出是否有可能把这个数组分成 `k` 个非空子集，其总和都相等。

{% embed url="https://leetcode-cn.com/problems/partition-to-k-equal-sum-subsets/solution/javadai-fan-hui-zhi-de-hui-su-fa-by-caipengbo/" %}

## 72 Edit Distance 46.2% Hard

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#72-edit-distance" %}

## 671 Second Minimum Node In a Binary Tree 42.7% Easy

给定一个非空特殊的二叉树，每个节点都是正数，并且每个节点的子节点数量只能为 2 或 0。如果一个节点有两个子节点的话，那么该节点的值等于两个子节点中较小的一个。

更正式地说，root.val = min\(root.left.val, root.right.val\) 总成立。

给出这样的一个二叉树，你需要输出所有节点中的第二小的值。如果第二小的值不存在的话，输出 -1 。

根据题目中所给条件，根节点root的值一定是最小值。求大于roo.val的最小值即可。 注意一个测试用例：\[2,2,2147483647\] 2147483647是Integer的最大值。

```java
class Solution {
    public long second_min = Long.MAX_VALUE;   //直接用long

    public int findSecondMinimumValue(TreeNode root) {
        if(root==null){
            return -1;
        }
        long x = Long.MAX_VALUE;
        second_min = getMin(root,root.val,x);
        return (int)(second_min==Long.MAX_VALUE?-1:second_min);
    }

    //求大于num的最小值
    public long getMin(TreeNode root,long num,long cur){
        if (root != null) {
            if(root.val>num){
                cur = Math.min(cur,root.val);
            }
            return Math.min(getMin(root.left, num, cur),getMin(root.right, num, cur));
        }
        return cur;
    }
}
```

671的延申，Tournament/winner tree，特点是父结点比左右子树都小，所有结点不一样 - 时空log\(n\)

```java
/** 
* A tournament tree is a binary tree 
* where the parent is the minimum of the two children. 
* Given a tournament tree find the second minimum value in the tree. 
* A node in the tree will always have 2 or 0 children. 
* Also all leaves will have distinct and unique values. 
*         2 
*      /    \ 
*     2      3 
*    / \    /  \    
*   4   2   5   3 
* 
* In this given tree the answer is 3. 
*/


class Node {
  Integer value;
  Node left, right;
  Node(Integer value, Node left, Node right) {
    this.value = value;
    this.left = left;
    this.right = right;
  }
}
class Solution {
  /**
  * This should return the second minimum
  * int value in the given tournament tree
  * Basically, you are comparing all the winners except the first winner. 
  That is why you need to go down it's direction and compare with the other members 
  who haven't faced each other in the process.
  */
  
   public static Integer secondMin(Node root) {
        if(root.left == null || root.right == null) return Integer.MAX_VALUE;
        int min;
        if(root.left.val == root.val) { //直接跳过最上面的root，自顶向下找到的结点就是第二小结点
            min = Math.min(root.right.val, secondMin(root.left));
        } else {
            min = Math.min(root.left.val, secondMin(root.right));
        }
        
        return min;
    } 
}
```

## 516 Longest Palindromic Subsequence 54.7% Medium

 给定一个字符串 `s` ，找到其中最长的回文子序列，并返回该序列的长度。可以假设 `s` 的最大长度为 `1000` 。

Given a string s, find the longest palindromic subsequence's length in s. You may assume that the maximum length of s is 1000.

**Example 1:**  
Input:

```text
"bbbab"
```

Output:

```text
4
```

One possible longest palindromic subsequence is "bbbb".

```java
class Solution {
    /*
    dp[i][j]: the longest palindromic subsequence’s length of substring(i, j)
    状态转移方程: dp[i][j] = dp[i+1][j-1] + 2 if s.charAt(i) == s.charAt(j)
    otherwise, dp[i][j] = Math.max(dp[i+1][j], dp[i][j-1])
    初始化: dp[i][i] = 1
    
    https://leetcode.com/problems/longest-palindromic-subsequence/discuss/99101/Straight-forward-Java-DP-solution
    同时还有自顶向下的例子
    */
    public int longestPalindromeSubseq(String s) {
        int[][] dp = new int[s.length()][s.length()];
        
        for (int i = s.length() - 1; i >= 0; i--) {
            dp[i][i] = 1;
            for (int j = i+1; j < s.length(); j++) {
                if (s.charAt(i) == s.charAt(j)) {
                    dp[i][j] = dp[i+1][j-1] +2;
                } else {
                    dp[i][j] = Math.max(dp[i+1][j], dp[i][j-1]);
                }
            }
        }
        return dp[0][s.length()-1];
    }
}
```

## 200 Number of Islands 48.3% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/dfs\#200-number-of-islands" %}

##  277 Find the Celebrity 42.9% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/graph\#277-find-the-celebrity" %}

## 76 Minimum Window Substring 35.5% Hard

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-2\#76-minimum-window-substring" %}

## 373 Find K Pairs with Smallest Sums 37.3% Medium

两个升序排序好的整数数组，各取一个组成pair使其和最小。

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/heap\#373-find-k-pairs-with-smallest-sums" %}

## 56 Merge Intervals 40.4% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou\#56-merge-intervals" %}

## 236 Lowest Common Ancestor of a Binary Tree 47.8% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#236-lowest-common-ancestor-of-binary-tree" %}

## 464 Can I Win 29.5% Medium

从一个范围（例如1到10）中两个人轮流抽取整数，抽取后不放回，累加和先达到100或者超过为赢。回溯or DP

{% embed url="https://leetcode-cn.com/problems/can-i-win/solution/hui-su-zhuang-ya-dp-by-8bun/" %}

## 235 Lowest Common Ancestor of a Binary Search Tree 51.2% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#235-lowest-common-ancestor-of-a-binary-search-tree" %}

## 605 Can Place Flowers 32.0% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/array\#605-can-place-flowers" %}

## 12 Integer to Roman 55.8% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#12-integer-to-roman" %}

## 102 Binary Tree Level Order Traversal 56.0% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#102-binary-tree-level-order-traversal" %}

## 273 Integer to English Words 27.8% Hard

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#273-integer-to-english-words" %}

## 636  Exclusive Time of Functions

[抢占CPU资源的函数独占时间，关键是单线程非抢占cpu，就是说父函数调用子函数，子函数结束之后、父函数才有可能结束。不会出现父函数结束了，子函数继续运行的情况 - 栈](https://leetcode-cn.com/problems/exclusive-time-of-functions/solution/han-shu-de-du-zhan-shi-jian-by-leetcode/)

## 33 Search in Rotated Sorted Array 35.5% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zai-array-zhong-cha-xun-yuan-su\#33-search-in-rotated-sorted-array" %}

## 1 Two Sum 46.0% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/array/ksum\#1-2sum" %}

## 127 Word Ladder 30.9% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-3\#127-word-ladder" %}

## 34 Find First and Last Position of Element in Sorted Array 36.9% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zai-array-zhong-cha-xun-yuan-su\#34-find-first-and-last-position-of-element-in-sorted-array-search-for-a-range" %}

## 20 Valid Parentheses 39.5% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/stack\#20-valid-parentheses" %}

## 57 Insert Interval 34.7% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/tag4\#57-insert-interval" %}

## 69 Sqrt\(x\) 34.7% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/er-fen-sou-suo\#69-sqrt-x" %}

## 319 Bulb Switcher 45.3% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/math\#319-bulb-switcher" %}

## 744 Find Smallest Letter Greater Than Target 45.6% Easy

给一个排序后的字符列表 letters ，列表中只包含小写英文字母。另给出一个目标字母 target，请你寻找在这一有序列表里比目标字母大的最小字母。在比较时，字母是依序循环出现的。举个例子：

如果目标字母 target = 'z' 并且字符列表为 letters = \['a', 'b'\]，则答案返回 'a'

二分法

```java
class Solution {
    // 1. 扫描法  
    // 2. 二分法  实际上我们需要找到最小一个比目标元素大的 即从有序数组中找到一个最小的数字比目标元素大
    public char nextGreatestLetter(char[] letters, char target) {
        int i = 0;
        int j = letters.length - 1;
        char ans = letters[0];
        while(i <= j) {
            int mid = i + (j - i) / 2;
            if(letters[mid] > target) {  
                ans = letters[mid];
                j = mid - 1;
            } else {
                i = mid + 1;
            }
        }
        return ans;
    }
}
```

## 46 Permutations 65.7% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking\#46-permutations" %}

## 13 Roman to Integer 56.3% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#13-roman-to-integer" %}

## 1117 Building H2O 53.1% Medium

氢分子和氧分子两个线程生成水H2O，用Semaphore信号量

{% embed url="https://leetcode-cn.com/problems/building-h2o/solution/semaphor-cyclicbarrier-zhi-guan-shi-xian-by-luca-z/" %}

## 449 Serialize and Deserialize BST 53.7% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#449-serialize-and-deserialize-bst" %}

## 146 LRU Cache 34.9% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/gao-ji-de-shu-ju-jie-gou-he-suan-fa\#146-lru-cache" %}

## 151 Reverse Words in a String 23.1% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#151-reverse-words-in-a-string" %}

## 215 Kth Largest Element in an Array 57.2% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer-1\#215-kth-largest-element-in-an-array" %}

##  126 Word Ladder II 23.2% Hard

单词接龙II，需要找出所有路径，单向或双向BFS

{% embed url="https://leetcode-cn.com/problems/word-ladder-ii/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-3-3/" %}

## 655 Print Binary Tree 55.6% Medium

在一个 m\*n 的二维字符串数组中输出二叉树，并遵守以下规则：

1. The row number `m` should be equal to the height of the given binary tree.
2. The column number `n` should always be an odd number.
3. The root node's value \(in string format\) should be put in the exactly middle of the first row it can be put. The column and the row where the root node belongs will separate the rest space into two parts \(**left-bottom part and right-bottom part**\). You should print the left subtree in the left-bottom part and print the right subtree in the right-bottom part. The left-bottom part and the right-bottom part should have the same size. Even if one subtree is none while the other is not, you don't need to print anything for the none subtree but still need to leave the space as large as that for the other subtree. However, if two subtrees are none, then you don't need to leave space for both of them.
4. Each unused space should contain an empty string `""`.
5. Print the subtrees following the same rules.

* 行数 m 应当等于给定二叉树的高度。 
* 列数 n 应当总是奇数。 
* 根节点的值（以字符串格式给出）应当放在可放置的第一行正中间。根节点所在的行与列会将剩余空间划分为两部分（左下部分和右下部分）。你应该将左子树输出在左下部分，右子树输出在右下部分。左下和右下部分应当有相同的大小。即使一个子树为空而另一个非空，你不需要为空的子树输出任何东西，但仍需要为另一个子树留出足够的空间。然而，如果两个子树都为空则不需要为它们留出任何空间。
* 每个未使用的空间应包含一个空的字符串""。 
* 使用相同的规则输出子树。

DFS或BFS

{% embed url="https://leetcode-cn.com/problems/print-binary-tree/solution/shu-chu-er-cha-shu-by-leetcode/" %}

## 160 Intersection of Two Linked Lists 42.3% Easy

注意确定一下是否有环

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist\#160-intersection-of-two-linked-lists" %}

## 296 Best Meeting Point 58.0% Hard $

最佳碰头地点

A group of two or more people wants to meet and minimize the total travel distance. You are given a 2D grid of values 0 or 1, where each 1 marks the home of someone in the group. The distance is calculated using [Manhattan Distance](http://en.wikipedia.org/wiki/Taxicab_geometry), where distance\(p1, p2\) = `|p2.x - p1.x| + |p2.y - p1.y|`.

**Example:**

```text
Input: 

1 - 0 - 0 - 0 - 1
|   |   |   |   |
0 - 0 - 0 - 0 - 0
|   |   |   |   |
0 - 0 - 1 - 0 - 0

Output: 6 

Explanation: Given three people living at (0,0), (0,4), and (2,2):
             The point (0,2) is an ideal meeting point, as the total travel distance 
             of 2+2+2=6 is minimal. So return 6.
```

朴素BFS（超时）- Time O\(m^2 \* n^2 \), Space O\(mn\)

```java
class Solution {
    public int minTotalDistance(int[][] grid) {
        int minDistance = Integer.MAX_VALUE;
        for (int row = 0; row < grid.length; row++) {
            for (int col = 0; col < grid[0].length; col++) {
                int distance = search(grid, row, col);
                minDistance = Math.min(distance, minDistance);
            }
        }
        return minDistance;
    }

    private int search(int[][] grid, int row, int col) {
        Queue<Point> q = new LinkedList<>();
        int m = grid.length;
        int n = grid[0].length;
        boolean[][] visited = new boolean[m][n];
        q.add(new Point(row, col, 0));
        int totalDistance = 0;
        while (!q.isEmpty()) {
            Point point = q.poll();
            int r = point.row;
            int c = point.col;
            int d = point.distance;
            if (r < 0 || c < 0 || r >= m || c >= n || visited[r][c]) {
                continue;
            }
            if (grid[r][c] == 1) {
                totalDistance += d;
            }
            visited[r][c] = true;
            q.add(new Point(r + 1, c, d + 1));
            q.add(new Point(r - 1, c, d + 1));
            q.add(new Point(r, c + 1, d + 1));
            q.add(new Point(r, c - 1, d + 1));
        }
        return totalDistance;
    }

    public class Point {
        int row;
        int col;
        int distance;
        public Point(int row, int col, int distance) {
            this.row = row;
            this.col = col;
            this.distance = distance;
        }
    }
}
```

We could use the [Selection algorithm](https://en.wikipedia.org/wiki/Selection_algorithm) to select the median in O\(mn\) time, but there is an easier way. Notice that we can collect both the row and column coordinates in sorted order.

```java
class Solution {
    public int minTotalDistance(int[][] grid) {
        List<Integer> rows = collectRows(grid);
        List<Integer> cols = collectCols(grid);
        int row = rows.get(rows.size() / 2);
        int col = cols.get(cols.size() / 2);
        return minDistance1D(rows, row) + minDistance1D(cols, col);
    }

    private int minDistance1D(List<Integer> points, int origin) {
        int distance = 0;
        for (int point : points) {
            distance += Math.abs(point - origin);
        }
        return distance;
    }

    private List<Integer> collectRows(int[][] grid) {
        List<Integer> rows = new ArrayList<>();
        for (int row = 0; row < grid.length; row++) {
            for (int col = 0; col < grid[0].length; col++) {
                if (grid[row][col] == 1) {
                    rows.add(row);
                }
            }
        }
        return rows;
    }

    private List<Integer> collectCols(int[][] grid) {
        List<Integer> cols = new ArrayList<>();
        for (int col = 0; col < grid[0].length; col++) {
            for (int row = 0; row < grid.length; row++) {
                if (grid[row][col] == 1) {
                    cols.add(col);
                }
            }
        }
        return cols;
    }
}
```

## 1188 Design Bounded Blocking Queue 72.4% Medium $

Implement a thread safe bounded blocking queue that has the following methods:

* `BoundedBlockingQueue(int capacity)` The constructor initializes the queue with a maximum `capacity`.
* `void enqueue(int element)` Adds an `element` to the front of the queue. If the queue is full, the calling thread is blocked until the queue is no longer full.
* `int dequeue()` Returns the element at the rear of the queue and removes it. If the queue is empty, the calling thread is blocked until the queue is no longer empty.
* `int size()` Returns the number of elements currently in the queue.

Your implementation will be tested using multiple threads at the same time. Each thread will either be a producer thread that only makes calls to the `enqueue` method or a consumer thread that only makes calls to the `dequeue` method. The `size` method will be called after every test case.

Please do not use built-in implementations of bounded blocking queue as this will not be accepted in an interview.

**Example 1:**

```text
Input:
1
1
["BoundedBlockingQueue","enqueue","dequeue","dequeue","enqueue","enqueue","enqueue","enqueue","dequeue"]
[[2],[1],[],[],[0],[2],[3],[4],[]]

Output:
[1,0,2,2]

Explanation:
Number of producer threads = 1
Number of consumer threads = 1

BoundedBlockingQueue queue = new BoundedBlockingQueue(2);   // initialize the queue with capacity = 2.

queue.enqueue(1);   // The producer thread enqueues 1 to the queue.
queue.dequeue();    // The consumer thread calls dequeue and returns 1 from the queue.
queue.dequeue();    // Since the queue is empty, the consumer thread is blocked.
queue.enqueue(0);   // The producer thread enqueues 0 to the queue. The consumer thread is unblocked and returns 0 from the queue.
queue.enqueue(2);   // The producer thread enqueues 2 to the queue.
queue.enqueue(3);   // The producer thread enqueues 3 to the queue.
queue.enqueue(4);   // The producer thread is blocked because the queue's capacity (2) is reached.
queue.dequeue();    // The consumer thread returns 2 from the queue. The producer thread is unblocked and enqueues 4 to the queue.
queue.size();       // 2 elements remaining in the queue. size() is always called at the end of each test case.
```

**ReentrantLock + Condition Solution**

ReentrantLock = Voltaile + CAS + Condition  
Condition = LinkedNode which contains Waiting Thread  
In same ReentrantLock, we can create multiple Conditions  
For this question, blockingqueue need 2 waiting list \( full / empty\)  
When queue is empty, we block dequeue thread, and add thread to empty waiting list  
When queue is full, block enqueue thread, add thread to full waiting list

Lock --- --- &gt; empty condition -&gt; blocked Thread1 -&gt; blocked Thread2  
\|  
\| --- &gt; full condition -&gt; blocked Thread 3 -&gt; blocked Thread4

Signal = Notify  
SignalAll = NotifyAll  
Await = Wait

Same as BlockingQueue implmentation in Java source code.

```java
import java.util.concurrent.locks.ReentrantLock; 
import java.util.concurrent.locks.Condition; 

class BoundedBlockingQueue {
    private ReentrantLock lock = new ReentrantLock();
    private Condition full = lock.newCondition();
    private Condition empty = lock.newCondition();
    private int[] queue;
    private int tail = 0;
    private int head = 0;
    private int size = 0;
    public BoundedBlockingQueue(int capacity) {
        queue = new int[capacity];
    }
    
    public void enqueue(int element) throws InterruptedException {
		lock.lock();
        try {
            while(size == queue.length) {
                full.await();
            }
            queue[tail++] = element;
            tail %= queue.length;
            size++;
            empty.signal();
        } finally {
            lock.unlock();
        }
    }
    
    public int dequeue() throws InterruptedException {
    		lock.lock();
            try {
                while(size == 0) {
                    empty.await();
                }
                int res = queue[head++];
                head %= queue.length;
                size--;
                full.signal();
                return res;
            } finally {
                lock.unlock();
            }
        }
        
        public int size() throws InterruptedException {
            lock.lock();
    		try {
    			return this.size;
    		} finally {
    			lock.unlock();
    		}
    }
}
```

## 101 Symmetric Tree 47.7% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#101-symmetric-tree" %}

## 412 Fizz Buzz 63.4% Easy

模拟或字符串连接

{% embed url="https://leetcode-cn.com/problems/fizz-buzz/solution/fizz-buzz-by-leetcode/" %}

## 460 LFU Cache 35.4% Hard

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/gao-ji-de-shu-ju-jie-gou-he-suan-fa\#460-lfu" %}

## 47 Permutations II 48.7% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking\#47-permutations-ii" %}

## 311 Sparse Matrix Multiplication 63.3% Medium $

稀疏矩阵相乘Given two [sparse matrices](https://en.wikipedia.org/wiki/Sparse_matrix) **A** and **B**, return the result of **AB**.

You may assume that **A**'s column number is equal to **B**'s row number.

**Example:**

```text
Input:

A = [
  [ 1, 0, 0],
  [-1, 0, 3]
]

B = [
  [ 7, 0, 0 ],
  [ 0, 0, 0 ],
  [ 0, 0, 1 ]
]

Output:

     |  1 0 0 |   | 7 0 0 |   |  7 0 0 |
AB = | -1 0 3 | x | 0 0 0 | = | -7 0 3 |
                  | 0 0 1 |
```

**Constraints:**

* `1 <= A.length, B.length <= 100`
* `1 <= A[i].length, B[i].length <= 100`
* `-100 <= A[i][j], B[i][j] <= 100`

直接解法

```java
class Solution {
    public int[][] multiply(int[][] A, int[][] B) {
        int m = A.length, n = A[0].length, nB = B[0].length;
        int[][] C = new int[m][nB];

        for(int i = 0; i < m; i++) {
            for(int k = 0; k < n; k++) {
                if (A[i][k] != 0) {
                    for (int j = 0; j < nB; j++) {
                        if (B[k][j] != 0) C[i][j] += A[i][k] * B[k][j];
                    }
                }
            }
        }
        return C;   
    }
}
```

## 706 Design HashMap 62.3% Easy

不使用build in的API实现HashMap的put，get和remove

取模 + 用数组模拟

{% embed url="https://leetcode-cn.com/problems/design-hashmap/solution/she-ji-ha-xi-biao-by-leetcode/" %}

## 367 Valid Perfect Square 42.0% Easy

判断是否是有效的完全平方数，使用二分法或者牛顿迭代法

{% embed url="https://leetcode-cn.com/problems/valid-perfect-square/solution/you-xiao-de-wan-quan-ping-fang-shu-by-leetcode/" %}

## 8 String to Integer \(atoi\) 15.5% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#8-string-to-integer-atoi" %}

## 739 Daily Temperatures 64.2% Medium

单调栈

```java
class Solution {
    public int[] dailyTemperatures(int[] T) {
        int length = T.length;
        int[] ans = new int[length];
        Deque<Integer> stack = new LinkedList<Integer>();
        for (int i = 0; i < length; i++) {
            int temperature = T[i];
            while (!stack.isEmpty() && temperature > T[stack.peek()]) {
                int prevIndex = stack.pop();
                ans[prevIndex] = i - prevIndex;
            }
            stack.push(i);
        }
        return ans;
    }
}
```

## 715 Range Module 39.8% Hard

实现跟踪数字范围的模块

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/tag1\#715-range-module" %}

## 103 Binary Tree Zigzag Level Order Traversal 49.6% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#103-binary-tree-zigzag-level-order-traversal" %}

## 1042 Flower Planting With No Adjacent 48.5% Medium

不邻接种花，

You have `n` gardens, labeled from `1` to `n`, and an array `paths` where `paths[i] = [xi, yi]` describes a bidirectional path between garden `xi` to garden `yi`. In each garden, you want to plant one of 4 types of flowers.

All gardens have **at most 3** paths coming into or leaving it.

Your task is to choose a flower type for each garden such that, for any two gardens connected by a path, they have different types of flowers.

Return _**any** such a choice as an array_ `answer`_, where_ `answer[i]` _is the type of flower planted in the_ `(i+1)th` _garden. The flower types are denoted_ `1`_,_ `2`_,_ `3`_, or_ `4`_. It is guaranteed an answer exists._

**Example 1:**

```text
Input: n = 3, paths = [[1,2],[2,3],[3,1]]
Output: [1,2,3]
Explanation:
Gardens 1 and 2 have different types.
Gardens 2 and 3 have different types.
Gardens 3 and 1 have different types.
Hence, [1,2,3] is a valid answer. Other valid answers include [1,2,4], [1,4,2], and [3,2,1].
```

**Example 2:**

```text
Input: n = 4, paths = [[1,2],[3,4]]
Output: [1,2,1,2]
```

**Example 3:**

```text
Input: n = 4, paths = [[1,2],[2,3],[3,4],[4,1],[1,3],[2,4]]
Output: [1,2,3,4]
```

**Constraints:**

* `1 <= n <= 104`
* `0 <= paths.length <= 2 * 104`
* `paths[i].length == 2`
* `1 <= xi, yi <= n`
* `xi != yi`
* Every garden has **at most 3** paths coming into or leaving it.

染色问题

{% embed url="https://leetcode-cn.com/problems/flower-planting-with-no-adjacent/solution/jian-dan-de-ran-se-wen-ti-bu-xu-yao-kao-lu-hui-su-/" %}

## 349 Intersection of Two Arrays 64.1% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table\#349-intersection-of-two-arrays" %}

## 350 Intersection of Two Arrays II 51.8% Easy

有重复数字

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table\#350-intersection-of-two-arrays-ii" %}

## 61 Rotate List 31.4% Medium

Rotate就是向右滑动

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist\#61-rotate-list" %}

## 1235 Maximum Profit in Job Scheduling 46.4% Hard

根据空闲时间规划兼职工作，使利润最大

{% embed url="https://leetcode-cn.com/problems/maximum-profit-in-job-scheduling/solution/java-dong-tai-gui-hua-by-zhi-94/" %}

## 323 Number of Connected Components in an Undirected Graph 57.2% Medium $

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/dfs\#323-number-of-connected-components-in-an-undirected-graph" %}

## 23 Merge k Sorted Lists 41.7% Hard

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist\#23-merge-k-sorted-lists" %}

## 100 Same Tree 53.9% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#100-same-tree" %}

## 104 Maximum Depth of Binary Tree 67.5% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#104-maximum-depth-of-binary-tree" %}

## 528 Random Pick with Weight 44.4% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/tag0\#528-random-pick-with-weight" %}

## 470 Implement Rand10\(\) Using Rand7\(\) 45.9% Medium

Given the **API** `rand7()` that generates a uniform random integer in the range `[1, 7]`, write a function `rand10()` that generates a uniform random integer in the range `[1, 10]`. You can only call the API `rand7()`, and you shouldn't call any other API. Please **do not** use a language's built-in random API.

Each test case will have one **internal** argument `n`, the number of times that your implemented function `rand10()` will be called while testing. Note that this is **not an argument** passed to `rand10()`.

**Follow up:**

* What is the [expected value](https://en.wikipedia.org/wiki/Expected_value) for the number of calls to `rand7()` function?
* Could you minimize the number of calls to `rand7()`?

**Example 1:**

```text
Input: n = 1
Output: [2]
```

**Example 2:**

```text
Input: n = 2
Output: [2,8]
```

**Example 3:**

```text
Input: n = 3
Output: [3,8,10]
```

**Constraints:**

* `1 <= n <= 105`

 不要使用系统的 `Math.random()` 方法。

{% embed url="https://leetcode-cn.com/problems/implement-rand10-using-rand7/solution/xiang-xi-si-lu-ji-you-hua-si-lu-fen-xi-zhu-xing-ji/" %}

## 796 Rotate String 49.4% Easy

通过旋转（把左边字符移到右边）确定是否可以由字符串A变为B

可以KMP

{% embed url="https://leetcode-cn.com/problems/rotate-string/solution/xuan-zhuan-zi-fu-chuan-by-leetcode/" %}

## 721 Accounts Merge 50.8% Medium

[根据邮件和用户名合并账户 - DFS](https://leetcode-cn.com/problems/accounts-merge/solution/zhang-hu-he-bing-by-leetcode/)

## 40 Combination Sum II 49.6% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking\#40-combination-sum-ii" %}

## 88 Merge Sorted Array 40.1% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/array/he-bing-you-xu-de-arrays\#88-merge-sorted-array" %}

## 39 Combination Sum 58.4% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking\#39-combination-sum" %}

## 1148 Article Views I 76.7% Easy $

浏览文章SQL

If with distinct keyword,

```sql
SELECT DISTINCT author_id AS id FROM Views 
where author_id = viewer_id 
ORDER BY id
```

If order by first, we need another Select and alias

```sql
SELECT id from (SELECT author_id AS id FROM Views 
where author_id = viewer_id 
ORDER BY id)a
GROUP BY id
```

If just with group by \(automatically sorted by id\)

```sql
SELECT author_id AS id FROM Views 
where author_id = viewer_id 
GROUP BY id
```

## 198 House Robber 42.6% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#198-house-robber" %}

## 973 K Closest Points to Origin 64.4% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer-1\#973-k-closest-points-from-origin" %}

## 21 Merge Two Sorted Lists 55.1% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist\#21-merge-two-sorted-lists" %}

## 75 Sort Colors 48.7% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou\#75-sort-colors" %}

## 1028 Recover a Tree From Preorder Traversal 70.6% Hard

输入短划线，仅从先序还原树，迭代方法更好

{% embed url="https://leetcode-cn.com/problems/recover-a-tree-from-preorder-traversal/solution/cong-xian-xu-bian-li-huan-yuan-er-cha-shu-by-leetc/" %}

## 54 Spiral Matrix 35.2% Medium

由外层向内层顺时针螺旋打印数组

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/array\#54-spiral-matrix" %}

## 173 Binary Search Tree Iterator 59.3% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#173-binary-search-tree-iterator" %}

## 238 Product of Array Except Self 61.1% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/array\#238-product-of-array-except-self" %}

## 230 Kth Smallest Element in a BST 61.9% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#230-kth-smallest-element-in-bst" %}

## 70 Climbing Stairs 48.4% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#70-climb-stairs" %}

## 362 Design Hit Counter 64.8% Medium $

Design a hit counter which counts the number of hits received in the past 5 minutes.

Each function accepts a timestamp parameter \(in seconds granularity\) and you may assume that calls are being made to the system in chronological order \(ie, the timestamp is monotonically increasing\). You may assume that the earliest timestamp starts at 1.

It is possible that several hits arrive roughly at the same time.

**Example:**

```text
HitCounter counter = new HitCounter();

// hit at timestamp 1.
counter.hit(1);

// hit at timestamp 2.
counter.hit(2);

// hit at timestamp 3.
counter.hit(3);

// get hits at timestamp 4, should return 3.
counter.getHits(4);

// hit at timestamp 300.
counter.hit(300);

// get hits at timestamp 300, should return 4.
counter.getHits(300);

// get hits at timestamp 301, should return 3.
counter.getHits(301); 
```

**Follow up:**  
What if the number of hits per second could be very large? Does your design scale?

use a queue to record the information of all the hits. Each time we call the function getHits\( \), we have to delete the elements which hits beyond 5 mins \(300\). The result would be the length of the queue

```java
class HitCounter {
    Queue<Integer> q = null;
    /** Initialize your data structure here. */
    public HitCounter() {
        q = new LinkedList<Integer>();
    }
    
    /** Record a hit.
        @param timestamp - The current timestamp (in seconds granularity). */
    public void hit(int timestamp) {
        q.offer(timestamp);
    }
    
    /** Return the number of hits in the past 5 minutes.
        @param timestamp - The current timestamp (in seconds granularity). */
    public int getHits(int timestamp) {
        while(!q.isEmpty() && timestamp - q.peek() >= 300) {
            q.poll();
        }
        return q.size();
    }
}
```

## 981 Time Based Key-Value Store 53.8% Medium

设计基于时间的键值存储

Create a timebased key-value store class `TimeMap`, that supports two operations.

1. `set(string key, string value, int timestamp)`

* Stores the `key` and `value`, along with the given `timestamp`.

2. `get(string key, int timestamp)`

* Returns a value such that `set(key, value, timestamp_prev)` was called previously, with `timestamp_prev <= timestamp`.
* If there are multiple such values, it returns the one with the largest `timestamp_prev`.
* If there are no values, it returns the empty string \(`""`\).

**Example 1:**

```text
Input: inputs = ["TimeMap","set","get","get","set","get","get"], inputs = [[],["foo","bar",1],["foo",1],["foo",3],["foo","bar2",4],["foo",4],["foo",5]]
Output: [null,null,"bar","bar",null,"bar2","bar2"]
Explanation:   
TimeMap kv;   
kv.set("foo", "bar", 1); // store the key "foo" and value "bar" along with timestamp = 1   
kv.get("foo", 1);  // output "bar"   
kv.get("foo", 3); // output "bar" since there is no value corresponding to foo at timestamp 3 and timestamp 2, then the only value is at timestamp 1 ie "bar"   
kv.set("foo", "bar2", 4);   
kv.get("foo", 4); // output "bar2"   
kv.get("foo", 5); //output "bar2"   
```

{% embed url="https://leetcode-cn.com/problems/time-based-key-value-store/solution/ji-yu-shi-jian-de-jian-zhi-cun-chu-by-leetcode/" %}

## 647 Palindromic Substrings 61.6% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#647-palindromic-substrings" %}

## 113 Path Sum II 48.3% Medium

返回所有符合条件的路径

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#113-path-sum-ii" %}

## 121 Best Time to Buy and Sell Stock 51.2% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#121-best-time-to-buy-and-sell-stock" %}

## 141 Linked List Cycle 42.0% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist\#141-linked-list-cycle" %}

## 785 Is Graph Bipartite? 48.1% Medium

Recall that a graph is bipartite if we can split its set of nodes into two independent subsets A and B, such that every edge in the graph has one node in A and another node in B. 若无向图 G=\(V,E\) 的顶点集 V 可以分割为两个互不相交的子集，且图中每条边的两个顶点分别属于不同的子集，则称图 G 为一个二分图。

[判断是否二分图 DFS/BFS/UF](https://leetcode-cn.com/problems/is-graph-bipartite/solution/bfs-dfs-bing-cha-ji-san-chong-fang-fa-pan-duan-er-/)

## 240 Search a 2D Matrix II 43.8% Medium

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zai-array-zhong-cha-xun-yuan-su\#240-search-a-2d-matrix-ii" %}

## 987 Vertical Order Traversal of a Binary Tree 37.4% Medium

这道题跟314的不同之处在于，这道题要计算左右子树的坐标方式，314只管BFS打印出来。

[垂序遍历 DFS记录坐标+排序](https://leetcode-cn.com/problems/vertical-order-traversal-of-a-binary-tree/solution/er-cha-shu-de-chui-xu-bian-li-by-leetcode-2/)

##  387 First Unique Character in a String 53.7% Easy

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#387-first-unique-character-in-a-string" %}

## 713 Subarray Product Less Than K 40.3% Medium

找出正整数数组中乘积小于k的所有子数组

二分或双指针

{% embed url="https://leetcode-cn.com/problems/subarray-product-less-than-k/solution/cheng-ji-xiao-yu-kde-zi-shu-zu-by-leetcode/" %}

## 279 Perfect Squares 48.4% Medium

给一个正整数，求最少由几个完全平方数组成，考察[四平方和定理](https://zh.wikipedia.org/wiki/%E5%9B%9B%E5%B9%B3%E6%96%B9%E5%92%8C%E5%AE%9A%E7%90%86)（没听说过）

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#279-perfect-squares" %}

## 515 Find Largest Value in Each Tree Row 61.9% Medium

在二叉树中找每一行的最大值

```java
/**
 * 深度优先搜索(DFS) -- 
 */
class Solution {
    public List<Integer> largestValues(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        dfs(root, list, 0);
        return list;
    }

    public void dfs(TreeNode node, List<Integer> list, int level) {
        if (node == null) return;

        if (list.size() == level) {
            list.add(node.val);
        } else {
            list.set(level, Math.max(list.get(level), node.val));
        }
        dfs(node.left, list, level+1);
        dfs(node.right, list, level+1);
    }
}

/**
* 广度优先搜索(BFS) -- 按层级遍历
*/
class Solution {
    public List<Integer> largestValues(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        if (root == null) return list;

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while(!queue.isEmpty()) {
            int size = queue.size();
            int max = Integer.MIN_VALUE;
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                max = Math.max(node.val, max);
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
            list.add(max);
        }
        return list;
    }
}
```

## 633 Sum of Square Numbers 32.4% Medium

平方数之和， 给定一个非负整数 `c` ，判断是否存在两个整数 `a` 和 `b`，使得 `a^2 + b^2 = c` 

{% embed url="https://leetcode-cn.com/problems/sum-of-square-numbers/solution/ping-fang-shu-zhi-he-by-leetcode/" %}

## 946 Validate Stack Sequences 63.3% Medium

验证栈序列，给定 pushed 和 popped 两个序列，每个序列中的 值都不重复，只有当它们可能是在最初空栈上进行的推入 push 和弹出 pop 操作序列的结果时，返回 true；否则，返回 false 。

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/stack\#946-validate-stack-sequence" %}

## 1343 Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold 64.5% Medium

size大小为 K 且平均值大于等于阈值的所有子数组数目，滑动窗口或前缀和

{% embed url="https://leetcode-cn.com/problems/number-of-sub-arrays-of-size-k-and-average-greater-than-or-equal-to-threshold/solution/bao-li-qiu-jie-hua-dong-chuang-kou-qiu-jie-qian-zh/" %}

## 1588 Sum of All Odd Length Subarrays 81.5% Easy

正整数数组中，计算所有可能的奇数长度子数组（连续子序列）的和。

求元素出现次数，而不是直接求子数组个数，否则复杂度很高

{% embed url="https://leetcode-cn.com/problems/sum-of-all-odd-length-subarrays/solution/onshi-jian-fu-za-du-o1shi-jian-fu-za-du-by-crj1998/" %}

## 1644 Lowest Common Ancestor of a Binary Tree II 58.0% Medium $

树结点自己可作为自己的后裔，236则是严格找上面的祖先。

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#1644-lowest-common-ancestor-of-a-binary-tree-ii-usd" %}

## 1650 Lowest Common Ancestor of a Binary Tree III 78.6% Medium $

树结点上有parent索引指向父结点

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#1650-lowest-common-ancestor-of-a-binary-tree-iii-usd" %}

## 360 Sort Tramsformed Array

有序转化数组，给你一个已经 排好序 的整数数组 nums 和整数 a、b、c。对于数组中的每一个数 x，计算函数值 f\(x\) = ax2 + bx + c，请将函数值产生的数组返回。

要注意，返回的这个数组必须按照 升序排列，并且我们所期望的解法时间复杂度为 O\(n\)

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/tag5\#360-sort-tramsformed-array" %}

## 918  [Maximum Sum Circular Subarray](https://leetcode.com/problems/maximum-sum-circular-subarray)

给定一个由整数数组 A 表示的环形数组 C，求 C 的非空子数组的最大可能和。在此处，环形数组意味着数组的末端将会与开头相连呈环状。（形式上，当0 &lt;= i &lt; A.length 时 C\[i\] = A\[i\]，且当 i &gt;= 0 时 C\[i+A.length\] = C\[i\]）此外，子数组最多只能包含固定缓冲区 A 中的每个元素一次。（形式上，对于子数组 C\[i\], C\[i+1\], ..., C\[j\]，不存在 i &lt;= k1, k2 &lt;= j 其中 k1 % A.length = k2 % A.length）

```text
输入：[1,-2,3,-2]
输出：3
解释：从子数组 [3] 得到最大和 3
```

前缀和 + 单调队列，展开成链处理

{% embed url="https://leetcode-cn.com/problems/maximum-sum-circular-subarray/solution/java-qian-zhui-he-dan-diao-dui-lie-dai-zhu-shi-by-/" %}




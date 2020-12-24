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

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table\#244-shortest-word-distance-ii" %}

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

##  149 Max Points on a Line 17.2% Hard

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

## 516 Longest Palindromic Subsequence 54.7% Medium

 给定一个字符串 `s` ，找到其中最长的回文子序列，并返回该序列的长度。可以假设 `s` 的最大长度为 `1000` 。

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

## 636 Exclusive Time of Functions 53.6% Medium



##  33 Search in Rotated Sorted Array 35.5% Medium



##  1 Two Sum 46.0% Easy



##  127 Word Ladder 30.9% Medium



##  34 Find First and Last Position of Element in Sorted Array 36.9% Medium



##  20 Valid Parentheses 39.5% Easy

##  57 Insert Interval 34.7% Medium

##  69 Sqrt\(x\) 34.7% Easy

##  319 Bulb Switcher 45.3% Medium

##  744 Find Smallest Letter Greater Than Target 45.6% Easy

##  46 Permutations 65.7% Medium

##  13 Roman to Integer 56.3% Easy

##  1117 Building H2O 53.1% Medium

##  449 Serialize and Deserialize BST 53.7% Medium

##  146 LRU Cache 34.9% Medium

##  151 Reverse Words in a String 23.1% Medium

##  215 Kth Largest Element in an Array 57.2% Medium

##  126 Word Ladder II 23.2% Hard

##  655 Print Binary Tree 55.6% Medium

##  160 Intersection of Two Linked Lists 42.3% Easy

##  296 Best Meeting Point 58.0% Hard

##  1188 Design Bounded Blocking Queue 72.4% Medium

##  101 Symmetric Tree 47.7% Easy

##  412 Fizz Buzz 63.4% Easy

##  460 LFU Cache 35.4% Hard

##  47 Permutations II 48.7% Medium

##  311 Sparse Matrix Multiplication 63.3% Medium

##  706 Design HashMap 62.3% Easy

##  367 Valid Perfect Square 42.0% Easy

##  8 String to Integer \(atoi\) 15.5% Medium

##  739 Daily Temperatures 64.2% Medium

##  715 Range Module 39.8% Hard

##  103 Binary Tree Zigzag Level Order Traversal 49.6% Medium

##  1042 Flower Planting With No Adjacent 48.5% Medium

##  349 Intersection of Two Arrays 64.1% Easy

##  61 Rotate List 31.4% Medium

##  1235 Maximum Profit in Job Scheduling 46.4% Hard

##  323 Number of Connected Components in an Undirected Graph 57.2% Medium

##  23 Merge k Sorted Lists 41.7% Hard

##  100 Same Tree 53.9% Easy

##  104 Maximum Depth of Binary Tree 67.5% Easy

##  528 Random Pick with Weight 44.4% Medium

##  470 Implement Rand10\(\) Using Rand7\(\) 45.9% Medium

##  796 Rotate String 49.4% Easy

##  721 Accounts Merge 50.8% Medium

##  40 Combination Sum II 49.6% Medium

##  88 Merge Sorted Array 40.1% Easy

##  39 Combination Sum 58.4% Medium

##  1148 Article Views I 76.7% Easy



##  198 House Robber 42.6% Medium

##  973 K Closest Points to Origin 64.4% Medium

##  21 Merge Two Sorted Lists 55.1% Easy

##  75 Sort Colors 48.7% Medium

##  1028 Recover a Tree From Preorder Traversal 70.6% Hard

##  54 Spiral Matrix 35.2% Medium

##  350 Intersection of Two Arrays II 51.8% Easy

##  173 Binary Search Tree Iterator 59.3% Medium

##  238 Product of Array Except Self 61.1% Medium

##  230 Kth Smallest Element in a BST 61.9% Medium

##  70 Climbing Stairs 48.4% Easy

##  362 Design Hit Counter 64.8% Medium

##  981 Time Based Key-Value Store 53.8% Medium

##  647 Palindromic Substrings 61.6% Medium

##  113 Path Sum II 48.3% Medium

##  121 Best Time to Buy and Sell Stock 51.2% Easy

##  141 Linked List Cycle 42.0% Easy

##  785 Is Graph Bipartite? 48.1% Medium

##  240 Search a 2D Matrix II 43.8% Medium

##  987 Vertical Order Traversal of a Binary Tree 37.4% Medium

##  387 First Unique Character in a String 53.7% Easy

##  713 Subarray Product Less Than K 40.3% Medium

##  207 Course Schedule 44.1% Medium

##  142 Linked List Cycle II 39.1% Medium

##  279 Perfect Squares 48.4% Medium

##  125 Valid Palindrome 37.7% Easy

##  206 Reverse Linked List 64.5% Easy

##  515 Find Largest Value in Each Tree Row 61.9% Medium

##  633 Sum of Square Numbers 32.4% Medium

##  946 Validate Stack Sequences 63.3% Medium

##  1107 New Users Daily Count 45.7% Medium

##  1127 User Purchase Platform 50.3% Hard

##  1149 Article Views II 48.3% Medium

##  1343 Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold 64.5% Medium

##  1588 Sum of All Odd Length Subarrays 81.5% Easy

##  1644 Lowest Common Ancestor of a Binary Tree II 58.0% Medium

##  1650 Lowest Common Ancestor of a Binary Tree III 78.6% Medium




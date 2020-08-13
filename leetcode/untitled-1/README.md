# Tree

## 144 Binary Tree Preorder Traversal

### 原题概述

Given a binary tree, return the _preorder_ traversal of its nodes' values.

**Example:**

```text
Input: [1,null,2,3]
   1
    \
     2
    /
   3

Output: [1,2,3]
```

**Follow up:** Recursive solution is trivial, could you do it iteratively?

### 题意和分析

前序遍历，根左右。

### 代码

递归

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        preorderTraversalHelper(root, result);
        return result;
    }
    private void preorderTraversalHelper(TreeNode root, List<Integer> result) {
        if (root == null) {
            return;
        }
        result.add(root.val);
        preorderTraversalHelper(root.left, result);
        preorderTraversalHelper(root.right, result);
    }
}
```

迭代

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();

        TreeNode current = root;
        while (current != null) {
            result.add(current.val);
            if (current.right != null) {//按照从上到下，逐次放入右儿子
                stack.push(current.right);
            }
            current = current.left;
            if (current == null && !stack.isEmpty()) {//说明左儿子已经放完了
                current = stack.pop();
            }
        }
        return result;
    }
}
```

## 145 Binary Tree Postorder Traversal

### 原题概述

Given a binary tree, return the _postorder_ traversal of its nodes' values.

**Example:**

```text
Input: [1,null,2,3]
   1
    \
     2
    /
   3

Output: [3,2,1]
```

**Follow up:** Recursive solution is trivial, could you do it iteratively?

### 题意和分析

后序遍历，左右根。

### 代码

递归

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();
        postorderTraversal (root, result);
        return result;
    }
    private void postorderTraversal(TreeNode root, List<Integer> result) {
        if (root == null) {
            return;
        }
        postorderTraversal (root.left, result);
        postorderTraversal (root.right, result);
        result.add(root.val);
    }
}
```

迭代，可以用不同的数据结构，从后往前插入比较方便

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        LinkedList<Integer> result = new LinkedList<>();
        Stack<TreeNode> stack = new Stack<>();

        if (root == null) {
            return result;
        }

        stack.push(root);

        while (!stack.isEmpty()) {
            TreeNode current = stack.pop();
            result.addFirst(current.val);
            if (current.left != null) {
                stack.push(current.left);
            }
            if (current.right != null) {
                stack.push(current.right);
            }
        }
        return result;
    }
}
```

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        if (root == null) {
            return result;
        }
        Stack<TreeNode> stack = new Stack<>();

        stack.push(root);

        while (!stack.isEmpty()) {
            TreeNode current = stack.pop();
            result.add(0, current.val);
            if (current.left != null) {
                stack.push(current.left);
            }
            if (current.right != null) {
                stack.push(current.right);
            }
        }
        return result;
    }
}
```

## 94 Binary Tree Inorder Traversal

### 原题概述

Given a binary tree, return the _inorder_ traversal of its nodes' values.

**Example:**

```text
Input: [1,null,2,3]
   1
    \
     2
    /
   3

Output: [1,3,2]
```

**Follow up:** Recursive solution is trivial, could you do it iteratively?

### 题意和分析

中序遍历，左根右。

### 代码

递归

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        inorderTraversalHelper(root, result);
        return result;
    }
    private void inorderTraversalHelper(TreeNode root, List<Integer> result) {
        if (root == null) {
            return;
        }
        inorderTraversalHelper(root.left, result);
        result.add(root.val);
        inorderTraversalHelper(root.right, result);
    }
}
```

迭代

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();

        TreeNode current = root;
        
        // 由于遍历顺序，不能像后序遍历那样先把root加入stack中，注意whle退出条件
        while (current != null || !stack.isEmpty()) {
            while (current != null) {
                stack.add(current);
                current = current.left;//先移动指针去左儿子
            }
            //左儿子没了开始从二叉树的最底层弹，同时考虑每个结点的右儿子
            current = stack.pop();
            result.add(current.val);//这时候可以add了
            current = current.right;
        }
        return result;
    }
}
```

## 230 Kth Smallest Element in BST

### 原题概述

Given a binary search tree, write a function `kthSmallest` to find the **k**th smallest element in it.

**Note:**  
You may assume k is always valid, 1 ≤ k ≤ BST's total elements.

**Example 1:**

```text
Input: root = [3,1,4,null,2], k = 1
   3
  / \
 1   4
  \
   2
Output: 1
```

**Example 2:**

```text
Input: root = [5,3,6,2,4,null,null,1], k = 3
       5
      / \
     3   6
    / \
   2   4
  /
 1
Output: 3
```

**Follow up:**  
What if the BST is modified \(insert/delete operations\) often and you need to find the kth smallest frequently? How would you optimize the kthSmallest routine?

### 题意和分析

使用中序遍历，遍历到第k个元素就是第k小的，作为结果返回，因为BST的中序遍历的结果刚好是从小到大排列，同样，也可以通过递归和迭代来做。

### 代码

迭代解法，将k左子树的元素放入到stack中，如果不够k再放入右子树的元素，然后到k后就返回当前值

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
  public int kthSmallest(TreeNode root, int k) {
    Stack<TreeNode> stack = new Stack<>();

    TreeNode current = root;
    while(current != null || !stack.isEmpty()) {
      while (current != null) {
        stack.push(current);
        current = current.left;
      }
      current = stack.pop();
      k--;
      if (k == 0) {
        return current.val;
      }
      current = current.right; // here no need to push to stack
    }
    throw new IllegalArgumentException("There is no kth smallest element.");
  }
}
```

递归解法，先递归左子树，不够再递归右子树，到k个时返回即可

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    int result = 0;
    int count = 0;
    public int kthSmallest(TreeNode root, int k) {
        count = k;
        inOrder(root);
        return result;
    }
    private void inOrder(TreeNode node) {
        if (node == null) {
            return;
        }
        
        inOrder(node.left); //中序先traverse先到leftmost
        
        count--;
        if (count == 0) {
            result = node.val;
            return;
        }
        inOrder(node.right);// 不夠再traverse右sub tree
    }
}
```

## 102 Binary Tree Level Order Traversal

### 原题概述

Given a binary tree, return the level order traversal of its nodes' values. \(ie, from left to right, level by level\).

For example:  
Given binary tree `[3,9,20,null,null,15,7]`,  


```text
    3
   / \
  9  20
    /  \
   15   7
```

return its level order traversal as:  


```text
[
  [3],
  [9,20],
  [15,7]
]
```

### 题意和分析

层序遍历，掌握BFS和递归的写法。

### 代码

BFS

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        if (root == null) {
            return result;
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);

        while (!queue.isEmpty()) {
            int size = queue.size();// 每一层的元素个数
            List<Integer> level = new ArrayList();
            while (size > 0) {//BFS
                TreeNode node = queue.poll();
                level.add(node.val);
                if (node.left != null) {
                    queue.add(node.left);
                }
                if (node.right != null) {
                    queue.add(node.right);
                }
                size--;
            }
            result.add(level);
        }
        return result;
    }
}
```

递归\([层序遍历各种题总结](https://leetcode.com/problems/binary-tree-level-order-traversal/discuss/114449/A-general-approach-to-level-order-traversal-questions-in-Java)\)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        if (root == null) {
            return result;
        }
        levelOrderHelper(result, root, 0);
        return result;
    }
    private void levelOrderHelper(List<List<Integer>> result, TreeNode current, int level) {
        if (current == null) {
            return;
        }
        if (result.size() == level) {//先DFS一条路走到底把每一层的arraylist创建好，然后回溯的时候在call stack中取到level的值，把对应的层的值传进去
            result.add(new ArrayList<Integer>());
        }
        result.get(level).add(current.val);

        levelOrderHelper(result, current.left, level + 1);
        levelOrderHelper(result, current.right, level + 1);
    }
}
```

## 104 - Maximum Depth of Binary Tree

### 原题概述

Given a binary tree, find its maximum depth.

The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

**Note:** A leaf is a node with no children.

**Example:**

Given binary tree `[3,9,20,null,null,15,7]`,

```text
    3
   / \
  9  20
    /  \
   15   7
```

return its depth = 3.

### 题意和分析

给一颗二叉树，找到其最大的深度并返回。可以用递归，DFS和BFS来做。

### 代码

递归做法

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        return 1 + Math.max(maxDepth(root.left), maxDepth(root.right));//只要root不为null，深度至少为1
    }
}
```

DFS，用两个stack，一个用来记录Tree的结点，另一个用来记录最大深度，不推荐，了解下即可。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        Stack<TreeNode> stack = new Stack<>();//存储树节点
        Stack<Integer> depth = new Stack<>();//存储树的深度
        stack.push(root);//初始化
        depth.push(1);//root不为null，则深度至少为1
        int maxDepth = 0;
        while (!stack.isEmpty()) {//只要stack中的元素还没有弹完
            int temp = depth.pop();//每轮先弹出上一轮的“最大深度“进行比较
            maxDepth = Math.max(temp, maxDepth);

            TreeNode node = stack.pop();
            if (node.left != null) {
                stack.push(node.left);
                depth.push(temp + 1);//在上一轮最大值的基础上，DFS多了一层
            }
            if (node.right != null) {
                stack.push(node.right);
                depth.push(temp + 1);
            }
        }
        return maxDepth;
    }
}
```

BFS，用Queue来实现，推荐的BFS方式

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
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        
        int depth = 0;
        
        while (!queue.isEmpty()) {
            int currentSize = queue.size();
            for (int i = 0; i < currentSize; i++) {
                TreeNode node = queue.poll();//node本身不用做任何检查
                
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
            depth++;
        }
        return depth;
    }
}
```

## 107 Binary Tree Level Order Traversal II

### 原题概述

Given a binary tree, return the bottom-up level order traversal of its nodes' values. \(ie, from left to right, level by level from leaf to root\).

For example:  
Given binary tree `[3,9,20,null,null,15,7]`,  


```text
    3
   / \
  9  20
    /  \
   15   7
```

return its bottom-up level order traversal as:  


```text
[
  [15,7],
  [9,20],
  [3]
]
```

### 题意和分析

跟102比，DFS - 递归到最后一层再加,递的过程中先在总list的头部建好各层的list，最后取层数的时候从最后开始取，results.get\(results.size\(\) - 1 - level\).add\(current.val\)； BFS - 加每一层list的时候直接加在总list 0的位置；

### 代码

DFS

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        if (root == null) {
            return result;
        }
        levelOrderHelper(result, root, 0);
        return result;
    }
    private void levelOrderHelper(List<List<Integer>> result, TreeNode current, int level) {
        if (current == null) {
            return;
        }
        if (result.size() == level) {//先DFS一条路走到底把每一层的arraylist创建好，然后回溯的时候在call stack中取到level的值，把对应的层的值传进去
            result.add(new ArrayList<Integer>());
        }
        result.get(result.size() - 1 - level).add(current.val);

        levelOrderHelper(result, current.left, level + 1);
        levelOrderHelper(result, current.right, level + 1);
    }
}
```

BFS

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        if (root == null) {
            return result;
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int size = queue.size();
            List<Integer> oneLevel = new ArrayList<>();
            while (size > 0) {
                TreeNode node = queue.poll();
                oneLevel.add(node.val);
                if (node.left != null) {
                    queue.add(node.left);
                }
                if (node.right != null) {
                    queue.add(node.right);
                }
                size--;
            }
            result.add(0, oneLevel);//唯一的不同点
        }
        return result;
    }
}
```

## 103 Binary Tree Zigzag Level Order Traversal

### 原题

Given a binary tree, return the zigzag level order traversal of its nodes' values. \(ie, from left to right, then right to left for the next level and alternate between\).

For example:  
Given binary tree `[3,9,20,null,null,15,7]`,  


```text
    3
   / \
  9  20
    /  \
   15   7
```

return its zigzag level order traversal as:  


```text
[
  [3],
  [20,9],
  [15,7]
]
```

### 分析

1\) BFS，可以用102普通层序遍历的办法，做个标记flag，第一层从左到右向遍历，第二层从右到左遍历，第三层从左到右遍历，so on and so forth，不过普通层序遍历是用一个queue来实现，这里可以用两个queue或者两个stack实现方向的转换，Java的API则直接使用一个双端队列。

![](../../.gitbook/assets/image%20%2861%29.png)

实现 BFS 的两个方法：

* 使用两层嵌套循环。外层循环迭代树的层级，内层循环迭代每层上的节点，这是上面102/107的做法，也是标准的BFS做法。
* 也可以使用一层循环实现 BFS。将要访问的节点添加到队列中，使用 分隔符（例如：空节点）把不同层的节点分隔开。分隔符表示一层结束和新一层开始。

在其中第二种方法的基础上，可以借助双端队列实现锯齿形顺序。在每一层，使用一个空的双端队列保存该层所有的节点。根据每一层的访问顺序，即从左到右或从右到左，决定从双端队列的哪一端插入节点。

时间和空间复杂度均为O\(n\)，n为节点个数。

![](../../.gitbook/assets/image%20%2859%29.png)

2\) DFS，利用递归栈保存层数的信息，时间复杂度为O\(n\)，空间O\(H\)，n为节点个数，H为二叉树的高度。

![](../../.gitbook/assets/image%20%2860%29.png)

### 代码

BFS

标准BFS

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
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        if (root == null) {
            return result;
        }
        Deque<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        boolean odd = true; // 用来标记flag，以此决定从哪个方向遍历该层，或者用个int整数判断奇偶也行
        
        while (!queue.isEmpty()) {
            List<Integer> oneLevel = new LinkedList<>();
            int count = queue.size();
            for (int i = 0; i < count; i++) {
                TreeNode node = queue.poll();
                if (odd) {// 判断奇偶数的行
                    oneLevel.add(node.val);
                } else {
                    oneLevel.add(0, node.val); // 偶数行插入到头部
                }
                
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
            result.add(oneLevel);
            odd = !odd;
        }
        return result;
    }
}
```

一层循环的BFS，并且用linkedlist来实现

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
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        if (root == null) {
            return result;
        }
        // add the root element with a delimiter to kick off the BFS loop
        LinkedList<TreeNode> queue = new LinkedList<TreeNode>();
        queue.addLast(root);
        queue.addLast(null); // 每个节点末尾加一个分隔符

        LinkedList<Integer> oneLevel = new LinkedList<Integer>();
        boolean odd = true;

        while (queue.size() > 0) {
            TreeNode node = queue.pollFirst();
            if (node != null) { // 节点后面跟的分隔符就是null
                if (odd) {
                    oneLevel.addLast(node.val);
                } else {
                    oneLevel.addFirst(node.val);
                }

                if (node.left != null) {
                    queue.addLast(node.left);
                }
                if (node.right != null) {
                    queue.addLast(node.right);
                }

            } else {
                // we finish the scan of one level
                result.add(oneLevel);
                oneLevel = new LinkedList<Integer>();
                // prepare for the next level
                if (queue.size() > 0)
                    queue.addLast(null);
                odd = !odd;
            }
        }
        return result;
    }
}
```

DFS

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
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        dfs(result, root, 0);
        return result;
    }

    private void dfs(List<List<Integer>> result, TreeNode root, int depth) {
        if (root == null) { // corner case
            return;
        }
        if (result.size() == depth) { // 该层已经遍历完，从0开始
            result.add(new LinkedList<>());
        } 
        if (depth % 2 == 0) { // 奇数层加到尾部
            result.get(depth).add(root.val);
        } else { // 偶数层加到头部
            result.get(depth).add(0, root.val);
        }
        dfs(result, root.left, depth + 1);
        dfs(result, root.right, depth + 1);
    }
}
```

## 111 Minimum Depth of Binary Tree

### 原题概述

Given a binary tree, find its minimum depth.

The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.

**Note:** A leaf is a node with no children.

**Example:**

Given binary tree `[3,9,20,null,null,15,7]`,

```text
    3
   / \
  9  20
    /  \
   15   7
```

return its minimum depth = 2.

### 题意和分析

同样的递归解法，与104类似，需要检查下是否到达叶子结点。

### 代码

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
    public int minDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        if (root.left == null && root.right == null) {
            return 1;
        }
        if (root.left == null) {
            return minDepth(root.right) + 1;
        }
        if (root.right == null) {
            return minDepth(root.left) + 1;
        }
        return Math.min(minDepth(root.left), minDepth(root.right)) + 1;
    }
}
```

BFS迭代解法锻炼思维

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
    public int minDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int depth = 1;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        
        while (!queue.isEmpty()) {
            int currentSize = queue.size();
            
            for (int i = 0; i < currentSize; i++) {
                TreeNode node = queue.poll();
                if (node.left == null && node.right == null) {
                    return depth; // 遇到第一个叶子节点立即返回，确保最小深度
                }
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
            depth++;
        }
        return depth;
    }
}
```

## 226 - Invert Binary Tree

### 原题概述

Invert a binary tree.

**Example:**

Input:

```text
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```

Output:

```text
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

**Trivia:**  
This problem was inspired by [this original tweet](https://twitter.com/mxcl/status/608682016205344768) by [Max Howell](https://twitter.com/mxcl):

> Google: 90% of our engineers use the software you wrote \(Homebrew\), but you can’t invert a binary tree on a whiteboard so f\*\*\* off.

### 题意和分析

同样可以用递归，DFS和BFS来做。

### 代码

递归的写法很简单，只是复杂度没有什么好优化的。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {//递归的终止条件
            return root;
        }
        TreeNode left = invertTree(root.left);
	      TreeNode right = invertTree(root.right);
        
        //递归调用后的最后一步
        root.left = right;
		    root.right = left;

        return root;
    }
}
```

DFS，可以用stack或者deque

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return root;
        }
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            TreeNode left = node.left;
            node.left = node.right;
            node.right = left;

            if (node.left != null) {
                stack.push(node.left);
            }

            if (node.right != null) {
                stack.push(node.right);
            }
        }
        return root;
    }
}
```

BFS，其实就是层序遍历level order traversal，数据结构不一样

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return root;
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);//vs add()

        while (!queue.isEmpty()) {
            TreeNode node = queue.poll();//vs peek()
            TreeNode left = node.left;
            node.left = node.right;
            node.right = left;
            if (node.left != null) {
                queue.offer(node.left);
            }
            if (node.right != null) {
                queue.offer(node.right);
            }
        }
        return root;
    }
}
```

## 617 - Merge Two  Binary Tree

### 原题概述

Given two binary trees and imagine that when you put one of them to cover the other, some nodes of the two trees are overlapped while the others are not.

You need to merge them into a new binary tree. The merge rule is that if two nodes overlap, then sum node values up as the new value of the merged node. Otherwise, the NOT null node will be used as the node of new tree.

**Example 1:**  


```text
Input: 
	Tree 1                     Tree 2                  
          1                         2                             
         / \                       / \                            
        3   2                     1   3                        
       /                           \   \                      
      5                             4   7                  
Output: 
Merged tree:
	     3
	    / \
	   4   5
	  / \   \ 
	 5   4   7
```

**Note:** The merging process must start from the root nodes of both trees.

### 题意和分析

同样可以用递归来做，将一棵树merge到另一颗树中。

### 代码

递归

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
        if (t1 == null) {
            return t2;
        }
        if (t2 == null) {
            return t1;
        }
        t1.val += t2.val;//将t2 merge到t1中来
        t1.left = mergeTrees(t1.left, t2.left);
        t1.right = mergeTrees(t1.right, t2.right);

        return t1;
    }
}
```

## 100 - Same Tree

### 原题概述

Given two binary trees, write a function to check if they are the same or not.

Two binary trees are considered the same if they are structurally identical and the nodes have the same value.

**Example 1:**

```text
Input:     1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]

Output: true
```

**Example 2:**

```text
Input:     1         1
          /           \
         2             2

        [1,2],     [1,null,2]

Output: false
```

**Example 3:**

```text
Input:     1         1
          / \       / \
         2   1     1   2

        [1,2,1],   [1,1,2]

Output: false
```

### 题意和分析

用递归和非递归分别实现。

### 代码

递归

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
	public boolean isSameTree(TreeNode p, TreeNode q) {
		if (p == null && q == null) {
			return true;
		} else if (p == null || q == null) {
			return false;
		}
		if (p.val != q.val) {
			return false;
		}
		return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
	}
}
```

迭代的办法，可以用BFS - Queue或者DFS - Stack来逐个检查，这里用BFS-Queue

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
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null && q == null) {
            return true;
        }
        if (p == null || q == null) {
            return false;
        }
        
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(p);
        queue.offer(q);
        
        while (!queue.isEmpty()) {
            int currentSize = queue.size();
            for (int i = 0; i < currentSize; i++) {
                TreeNode nodeP = queue.poll();
                TreeNode nodeQ = queue.poll();
                
                if (nodeP == null && nodeQ == null) {
                    continue;
                }
                
                if (nodeP == null || nodeQ == null || nodeP.val != nodeQ.val) {
                    return false;
                }
                
                queue.offer(nodeP.left);
                queue.offer(nodeQ.left);
                queue.offer(nodeP.right);
                queue.offer(nodeQ.right);
            }
        }
        return true;
    }
}
```

## 173 - Binary Search Tree Iterator

### 原题概述

Implement an iterator over a binary search tree \(BST\). Your iterator will be initialized with the root node of a BST.

Calling `next()` will return the next smallest number in the BST.

**Note:** `next()` and `hasNext()` should run in average O\(1\) time and uses O\(h\) memory, where h is the height of the tree.

### 题意和分析

这道题是对二叉搜索树的遍历，要求next\(\)返回下一个最小的val，next\(\)和hasNext\(\)的时间复杂度为O\(1\)，空间复杂度要求为O\(h\)，其中h为高度。BST的建树规则是left-root-right，这个跟中序遍历一样。

> 每个节点的val值大于左子节点，小于右子节点。注意它不一定是完全的二叉树。所有结点的val值是唯一的。数组的搜索比较方便，可以直接使用下标，但删除或者插入就比较麻烦了，而链表与之相反，删除和插入都比较简单，但是查找很慢，这自然也与这两种数据结构的存储方式有关，数组是取一段相连的空间，而链表是每创建一个节点便取一个节点所需的空间，只是使用指针进行连接，空间上并不是连续的。而二叉树就既有链表的好处，又有数组的优点。

![&#x4E2D;&#x5E8F;&#x904D;&#x5386;&#x7684;&#x987A;&#x5E8F;&#x662F;GDHBEAKCIJF](../../.gitbook/assets/image.png)

使用自定义的栈来模拟中序遍历。也就是说，我们将采用迭代的方式来模拟中序遍历，而不是采用递归的方法；这样做的过程中，能够轻松的实现这两个函数的调用，而不是用其他额外的空间。

然而，就这两个函数的复杂性而言，会有点复杂，我们需要花一些时间来理解这种方法是否符合问题所说的渐近复杂性的要求。具体算法：

1. 初始化一个空栈 S，用于模拟二叉搜索树的中序遍历。中序遍历采用与递归相同的方法，只是现在使用的是自己的栈而不是系统的堆栈。由于使用自定义的数据结构，因此可以随时暂停和恢复递归。 
2. 还要实现一个帮助函数\_leftmostInorder，在实现中将一次又一次的调用它。这个函数将给定节点中的所有左子节点添加到栈中，直到节点没有左子节点为止。
3. 第一次调用 next\(\) 函数时，必须返回二叉搜索树的最小元素，然后模拟递归必须向前移动一步，即移动到二叉搜索树的下一个最小元素上。栈的顶部始终包含 next\(\) 函数返回的元素。hasNext\(\) 很容易实现，因为只需要调用stack的方法直接检查栈是否为空。 
4. 首先，给定二叉搜索树的根节点，我们调用函数 \_leftmostInorder，这确保了栈顶部始终包含了 next\(\) 函数返回的元素。
5.  假设调用 `next()`，我们需要返回二叉搜索树中的下一个最小元素，即栈的顶部元素。有两种可能性：
   * 一个是栈顶部的节点是一个叶节点。这是最好的情况，因为我们什么都不用做，只需将节点从栈中弹出并返回其值。所以这是个常数时间的操作。
   * 另一个情况是栈顶部的节点拥有右节点。我们不需要检查左节点，因为左节点已经添加到栈中了。栈顶元素要么没有左节点，要么左节点已经被处理了。如果栈顶部拥有右节点，那么我们需要对右节点上调用帮助函数。该时间复杂度取决于树的结构。
6.  `next()` 函数调用中，获取下一个最小的元素不需要花太多时间，但是要保证栈顶元素是 `next()` 函数返回的元素方面花费了一些时间。

### 代码

* 时间复杂度：
  *  hasNext\(\)：若栈中还有元素，则返回 true，反之返回 false。所以这是一个 O\(1\) 的操作。 
  * next\(\)：包含了两个主要步骤。一个是从栈中弹出一个元素，它是下一个最小的元素。这是一个 O\(1\) 的操作。然而，随后我们要调用帮助函数 \_leftmostInorder，它需要递归的，将左节点添加到栈上，是线性时间的操作，最坏的情况下为 O\(N\)。但是只对含有右节点的节点进行调用，它也不会总是处理 N 个节点。只有当有一个非常不平衡的树，才会有 N 个节点。因此该操作的平均时间复杂度仍然是 O\(1\)，符合问题中所要求的。 
* 空间复杂度：O\(h\)O\(h\)，使用了一个栈来模拟递归。



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
class BSTIterator {
    
    Stack<TreeNode> stack;

    public BSTIterator(TreeNode root) {
        stack = new Stack<>();
        
        this._leftmostInorder(root);
    }
    
    private void _leftmostInorder(TreeNode node) {
        while (node != null) {
            // 将给定的node的所有左孩子节点放入到stack中
            this.stack.push(node);
            node = node.left;
        }
    }
    
    /** @return the next smallest number */
    public int next() {
        if (hasNext()) {
            TreeNode topmost = this.stack.pop();
            
            // 根据BST的特征，如果当前节点需要返回，需要把当前节点的右儿子的所有左子树push到stack中
            // 这时候当前节点的左子树已经pop完，仔细想想为什么
            if (topmost.right != null) {
                this._leftmostInorder(topmost.right);
            }
            
            return topmost.val;
        }
        return new Integer(null); // 为空了
    }
    
    /** @return whether we have a next smallest number */
    public boolean hasNext() {
        // 直接调用原生方法进行判断是否还有元素
        return this.stack.size() > 0;
    }
}

/**
 * Your BSTIterator object will be instantiated and called as such:
 * BSTIterator obj = new BSTIterator(root);
 * int param_1 = obj.next();
 * boolean param_2 = obj.hasNext();
 */
```

## 96 - Unique Binary Search Trees

### 原题概述

Given _n_, how many structurally unique **BST's** \(binary search trees\) that store values 1 ... _n_?

**Example:**

```text
Input: 3
Output: 5
Explanation:
Given n = 3, there are a total of 5 unique BST's:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

### 题意和分析

这个解释来自[http://fisherlei.blogspot.com/2013/03/leetcode-unique-binary-search-trees.html](http://fisherlei.blogspot.com/2013/03/leetcode-unique-binary-search-trees.html)，略作修改

 如果把上例的顺序改一下，就可以看出规律了。  
 1                1                      2                       3             3  
    \                 \                 /      \                  /              /   
      3               2              1       3               2             1  
    /                   \                                       /                  \  
 2                       3                                   1                    2  
  
比如，以1为根的树有几个，完全取决于有二个元素的子树有几种。同理，2为根的子树取决于一个元素的子树有几个。以3为根的情况，则与1相同。  
  
定义Count\[i\] 为以\[0,i\]能产生的Unique Binary Tree的数目，  
  
如果数组为空，毫无疑问，只有一种BST，即空树，  
Count\[0\] =1  
  
如果数组仅有一个元素{1}，只有一种BST，单个节点  
Count\[1\] = 1  
  
如果数组有两个元素{1,2}， 那么有如下两种可能  
1                       2  
  \                    /  
    2                1  
Count\[2\] = Count\[0\] \* Count\[1\]   \(1为根的情况\)  
                  + Count\[1\] \* Count\[0\]  \(2为根的情况。  
  
再看一遍三个元素的数组，可以发现BST的取值方式如下：  
Count\[3\] = Count\[0\]\*Count\[2\]  \(1为根的情况\)  
               + Count\[1\]\*Count\[1\]  \(2为根的情况\)  
               + Count\[2\]\*Count\[0\]  \(3为根的情况\)  
  
所以，由此观察，可以得出Count的递推公式为

![](../../.gitbook/assets/image%20%2813%29.png)

这正是[卡塔兰数](http://zh.wikipedia.org/wiki/%E5%8D%A1%E5%A1%94%E5%85%B0%E6%95%B0)的一种定义方式，是一个典型的动态规划的定义方式（根据其实条件和递推式求解结果）。所以思路也很明确了，维护量res\[i\]表示含有i个结点的二叉查找树的数量。根据上述递推式依次求出1到n的的结果即可。

对于BST是否Unique，很难判断，但引入了一个条件以后，立即就清晰了，**即当数组为 1，2，3，4，.. i，.. n时，基于以下原则的BST建树具有唯一性： 以i为根结点的树，其左子树由\[0, i-1\]构成， 其右子树由\[i+1, n\]构成。**

时间上每次求解i个结点的二叉查找树数量的需要一个i步的循环，总体要求n次，所以总时间复杂度是O\(1+2+...+n\)=O\(n^2\)。空间上需要一个数组来维护，并且需要前i个的所有信息，所以是O\(n\)。

### 代码

```java
class Solution {
    public int numTrees(int n) {
        if (n == 0 || n == 1) {
            return 1;
        }
        int[] trees = new int[n + 1];
        trees[0] = 1;

        // DP递推式：tree[t+1] = trees[i] * trees[t - i]，其中0 <= i <= t
        // 令num = t + 1, 则t = num - 1;
        //递推式转化为:
        //trees[num] += trees[i] * trees[num-1]，其中0 <= i <= num - 1, 1 <= num <= n;
        //trees[0] = 1

        for (int num = 1; num <= n; num++) {
            for (int i = 0; i <= num - 1; i++) {
                trees[num] += trees[i] * trees[(num - 1) - i];
            }
        }
        return trees[n];
    }
}
```

## 95 - Unique Binary Search Trees II

### 原题概述

Given an integer _n_, generate all structurally unique **BST's** \(binary search trees\) that store values 1 ... _n_.

**Example:**

```text
Input: 3
Output:
[
  [1,null,3,2],
  [3,2,null,1],
  [3,1,null,null,2],
  [2,1,3],
  [1,null,2,null,3]
]
Explanation:
The above output corresponds to the 5 unique BST's shown below:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

### 题意和分析

跟上一题相比，这道题要求不是总数，而是打印出所有符合条件的BST。

大致思路如下：

1. 每一次都在一个范围内随机选取一个结点作为根。 
2. 每选取一个结点作为根，就把树切分成左右两个子树，直至该结点左右子树为空。

可以看出这也是一个可以划分成子问题求解的题目，所以考点是动态规划。 但具体对于本题来说，采取的是自底向上的求解过程。

 1. 选出根结点后应该先分别求解该根的左右子树集合，也就是根的左子树有若干种，它们组成左子树集合，根的右子树有若干种，它们组成右子树集合。 

2. 然后将左右子树相互配对，每一个左子树都与所有右子树匹配，每一个右子树都与所有的左子树匹配。然后将两个子树插在根结点上。 

3. 最后，把根结点放入链表中。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<TreeNode> generateTrees(int n) {
        if (n < 1) {
            return new ArrayList<TreeNode>();
        }
        return helper(1, n);
    }

    private List<TreeNode> helper(int start, int end) {
        List<TreeNode> result = new ArrayList<>();
        if (start > end) {
            result.add(null);
            return result;
        }

        for (int i = start; i <= end; i++) {//在1...n中依次选取结点作为根
            //接下来根据选取的根，把树切分成左右子树，递归直到为空
            List<TreeNode> leftChild = helper(start, i - 1);
            List<TreeNode> rightChild = helper(i + 1, end);
            for (TreeNode left : leftChild) {
                for (TreeNode right : rightChild) {
                    TreeNode root = new TreeNode(i);//当前树的根

                    //根结合上左右子树
                    root.left = left;
                    root.right = right;

                    result.add(root);
                }
            }
        }
        return result;
    }
}
```

## 110 - Balanced Binary Tree

### 原题概述

Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as:

> a binary tree in which the depth of the two subtrees of _every_ node never differ by more than 1.

**Example 1:**

Given the following tree `[3,9,20,null,null,15,7]`:

```text
    3
   / \
  9  20
    /  \
   15   7
```

Return true.  
  
**Example 2:**

Given the following tree `[1,2,2,3,3,null,null,4,4]`:

```text
       1
      / \
     2   2
    / \
   3   3
  / \
 4   4
```

Return false.

### 题意和分析

平衡二叉树的定义：两个子树的高度相差不会超过1，而且左右子树都是平衡的。用动态规划的两个套路，**自底向上**和**自顶向下来解**。

### 代码

利用递归，算出左右子树的深度，然后严格按照平衡二叉树的定义看左右子树的深度是否相差大于1。其中findDepth\(\)的复杂度需要遍历每个结点为O\(n\)，而对所有结点都会执行以下findDepth\(\)，所以总体时间复杂度为O\(n^2\)，空间为O\(n\)。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public boolean isBalanced(TreeNode root) {
        if (root == null) {
            return true;
        }

        //左右子树的深度
        int left = findDepth(root.left);
        int right = findDepth(root.right);

        return Math.abs(left - right) <= 1 //随时检查
                && isBalanced(root.left) && isBalanced(root.right);
    }

    //递归找到子树的深度
    private int findDepth(TreeNode node) {
        if (node == null) {
            return 0;
        }
        return Math.max(findDepth(node.left), findDepth(node.right)) + 1;//node不为0深度至少为1
    }
}
```

在上面解法的基础上，做记忆化搜索

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
    HashMap<TreeNode, Integer> memo = new HashMap<>(); // 记忆化
    public boolean isBalanced(TreeNode root) {
        if (root == null) {
            return true;
        }
        int left = findDepth(root.left);
        int right = findDepth(root.right);
        
        return Math.abs(left - right) <= 1 && isBalanced(root.left) && isBalanced(root.right);
    }
    
    private int findDepth(TreeNode node) {
        if (node == null) {
            return 0;
        }
        if (memo.containsKey(node)) {
            return memo.get(node);
        }
        int curDepth = 1 + Math.max(findDepth(node.left), findDepth(node.right));
        memo.put(node, curDepth);
        return curDepth;
    }
}
```

也可以利用DFS，在DFS的递归中返回当前结点的height，如果当前结点的子结点是平衡的，dfsHeight\(\)会返回一个非负整数，如果不平衡就返回-1，然后根据leftHeight和rightHeight的结果来决定一层层上升传递的数值。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public boolean isBalanced(TreeNode root) {
        return dfsHeight(root) != -1;//检查返回的数字决定是否平衡
    }
    private int dfsHeight(TreeNode root) {
        if (root == null) {//递归的终止条件，查到最后还是平衡的就返回-1
            return 0;
        }

        //回溯时层层传递
        int leftHeight = dfsHeight(root.left);
        if (leftHeight == -1) { // 提前返回
            return -1;
        }
        int rightHeight = dfsHeight(root.right);
        if (rightHeight == -1) { // 提前返回
            return -1;
        }

        if (Math.abs(leftHeight - rightHeight) > 1) { // 提前返回
            return -1;
        }

        return Math.max(leftHeight, rightHeight) + 1;//返回当前结点的height
    }
}
```

## 108 - Convert Sorted Array to Binary Search Tree

### 原题概述

Given an array where elements are sorted in ascending order, convert it to a height balanced BST.

For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of _every_ node never differ by more than 1.

**Example:**

```text
Given the sorted array: [-10,-3,0,5,9],

One possible answer is: [0,-3,9,-10,null,5], which represents the following height balanced BST:

      0
     / \
   -3   9
   /   /
 -10  5
```

### 题意和分析

这道题是要将有序数组转为二叉搜索树，二叉搜索树始终满足左&lt;=根&lt;=右的特性，如果将二叉搜索树按中序遍历的话，得到的就是一个有序数组。那么反过来，根节点应该是有序数组的中间点，从中间点分开为左右两个有序数组，在分别找出其中间点作为原中间点的左右两个子节点，这不就是是二分查找法的核心思想么。所以这道题考的就是二分查找法。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        return sortedArrayToBST(nums, 0, nums.length - 1);
    }

    private TreeNode sortedArrayToBST(int[] nums, int left, int right) {//传入起点和终点
        if (left > right) {
            return null;
        }
        int mid = left + (right - left) / 2;
        TreeNode current = new TreeNode(nums[mid]);
        current.left = sortedArrayToBST(nums, left, mid - 1);
        current.right = sortedArrayToBST(nums, mid + 1, right);

        return current;
    }
}
```

也可以不使用额外的递归函数，而是在原函数中完成递归，由于原函数的参数是一个数组，所以当把输入数组的中间数字取出来后，需要把所有两端的数组组成一个新的数组，并且分别调用递归函数，并且连到新创建的cur结点的左右子结点上面

## 109 Convert Sorted List to Binary Search Tree

### 题目

Given a singly linked list where elements are sorted in ascending order, convert it to a height balanced BST.

For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of _every_ node never differ by more than 1.

**Example:**

```text
Given the sorted linked list: [-10,-3,0,5,9],

One possible answer is: [0,-3,9,-10,null,5], which represents the following height balanced BST:

      0
     / \
   -3   9
   /   /
 -10  5
```

### 分析

 要求把有序链表转为二叉搜索树，和之前那道 [Convert Sorted Array to Binary Search Tree](http://www.cnblogs.com/grandyang/p/4295245.html) 思路完全一样，只不过是操作的数据类型有所差别，一个是数组，一个是链表。数组方便就方便在可以通过index直接访问任意一个元素，而链表不行。由于二分查找法每次需要找到中点，而链表的查找中间点可以通过快慢指针来操作。找到中点后，要以中点的值建立一个数的根节点，然后需要把原链表断开，分为前后两个链表，都不能包含原中节点，然后再分别对这两个链表递归调用原函数，分别连上左右子节点即可。

### 代码

重写一个递归函数（也可以直接使用原函数做递归，个人喜欢重写一个函数），有两个输入参数，子链表的起点和终点，因为知道了这两个点，链表的范围就可以确定了\(不传入tail，直接用null也行\)，而直接将中间部分转换为二叉搜索树即可重写一个递归函数，有两个输入参数，子链表的起点和终点，因为知道了这两个点，链表的范围就可以确定了，而直接将中间部分转换为二叉搜索树即可。

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
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
    public TreeNode sortedListToBST(ListNode head) {
        if (head == null) {
            return null;
        }
        return recursion(head, null);
    }
    private TreeNode recursion(ListNode node, ListNode tail) {
        if (node == tail) {
            return null;
        }
        ListNode slow = node;
        ListNode fast = node;
        while (fast != tail && fast.next != tail) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        //已经找到中点,构建root和其左右子树
        TreeNode curr = new TreeNode(slow.val);
        curr.left = recursion(node, slow);
        curr.right = recursion(slow.next, tail);
        
        return curr;
    }
}
```

## 669 - Trim a Binary Search Tree

### 原题概述

Given a binary search tree and the lowest and highest boundaries as `L` and `R`, trim the tree so that all its elements lies in `[L, R]` \(R &gt;= L\). You might need to change the root of the tree, so the result should return the new root of the trimmed binary search tree.

**Example 1:**  


```text
Input: 
    1
   / \
  0   2

  L = 1
  R = 2

Output: 
    1
      \
       2
```

**Example 2:**  


```text
Input: 
    3
   / \
  0   4
   \
    2
   /
  1

  L = 1
  R = 3

Output: 
      3
     / 
   2   
  /
 1
```

### 题意和分析

给一个边界返回\[L, R\]，修剪给定的BST，所有不在范围内的结点都应该被剪掉，但是剩下的应该还是BST，左&lt;=根&lt;=右，如果是先遍历一遍BST，把符合要求的结点放入到一个Array里面，然后再重建一个新的BST，这样的想法可能会改变原来BST的总体结构；

所以用另外一种思路，在遍历的过程就对二叉树进行修剪，在递归的过程中判定根结点是否在范围中，如果根结点的值小于L，就返回根结点的右子结点调用递归的值；如果根结点大于R，就返回根结点的左子结点调用递归函数的值；如果根结点在范围内，就将起左右子结点同时更新为对其左右子结点调用递归函数的值，最后返回root。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode trimBST(TreeNode root, int L, int R) {
        if (root == null) {
            return null;
        }
        if (root.val < L) {//左边的结点都不用看了
            return trimBST(root.right, L, R);
        }
        if (root.val > R) {//右边的结点都不用看了
            return trimBST(root.left, L, R);
        }
        
        //root的值在L和R中间的时候
        root.left = trimBST(root.left, L, R);
        root.right = trimBST(root.right, L, R);

        return root;
    }
}
```

同样，也可以用非递归while的办法来代替递归过程

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode trimBST(TreeNode root, int L, int R) {
        if (root == null) {
            return null;
        }
        while (root.val < L || root.val > R) {
            root = (root.val < L) ? root.right : root.left;
        }
        TreeNode current = root;
        while (current != null) {
            while (current.left != null && current.left.val < L) {
                current.left = current.left.right;//太小，移向右边
            }
            current = current.left;
        }
        current = root;
        while (current != null) {
            while (current.right != null && current.right.val > R) {
                current.right = current.right.left;//太大，移向左边
            }
            current = current.right;
        }

        return root;
    }
}
```

## 101 - Symmetric Tree

### 原题概述

Given a binary tree, check whether it is a mirror of itself \(ie, symmetric around its center\).

For example, this binary tree `[1,2,2,3,4,4,3]` is symmetric:

```text
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

But the following `[1,2,2,null,3,null,3]` is not:  


```text
    1
   / \
  2   2
   \   \
   3    3
```

**Note:**  
Bonus points if you could solve it both recursively and iteratively.

### 题意和分析

判断一个Binary Tree是否是平衡树，两个结点n1和n2，需要判断n1的左子结点和n2的右子结点是否相同，以及n1的右子结点和n2的左子结点是否相同。加分项是用递归和迭代的办法分别做出来。

### 代码

递归

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
	public boolean isSymmetric(TreeNode root) {
		if (root == null) {
			return true;
		}
		return isSymmetricLeftRight(root.left, root.right);
	}
	private boolean isSymmetricLeftRight(TreeNode treeLeft, TreeNode treeRight) {
		if (treeLeft == null && treeRight == null) {
			return true;
		} else if (treeLeft == null || treeRight == null) {
			return false;
		}
		if (treeLeft.val != treeRight.val) {
			return false;
		}
		return isSymmetricLeftRight(treeLeft.left, treeRight.right) && isSymmetricLeftRight(treeLeft.right, treeRight.left);
	}
}
```

非递归，同样的思路做迭代，用Queue来做，把左边的结点和右边分别进行比较即可。

> 队列Queue使用时要尽量避免Collection的add\(\)和remove\(\)方法，而是要使用offer\(\)来加入元素，使用poll\(\)来获取并移出元素。它们的优点是通过返回值可以判断成功与否，add\(\)和remove\(\)方法在失败的时候会抛出异常。 如果要使用前端而不移出该元素，使用element\(\)或者peek\(\)方法。
>
> 另外LinkedList类实现了Queue接口，因此我们可以把LinkedList当成Queue来用。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) {
            return true;
        }
        //这里用一个Queue也是可以的，每次while循环依次取出和加入就可以了，为了方便阅读用了两个Queue
        Queue<TreeNode> q1 = new LinkedList<>();
        Queue<TreeNode> q2 = new LinkedList<>();
        q1.offer(root.left);
        q2.offer(root.right);

        while (!q1.isEmpty() && !q2.isEmpty()) {
            TreeNode left = q1.poll();
            TreeNode right = q2.poll();

            if (left == null && right == null) {
                continue;
            }
            if (left == null || right == null || left.val != right.val) {
                return false;
            }

            q1.offer(left.left);
            q1.offer(left.right);
            q2.offer(right.right);
            q2.offer(right.left);
        }
        return true;
    }
}
```

## 124 - Binary Tree Maximum Path Sum

### 原题概述

Given a **non-empty** binary tree, find the maximum path sum.

For this problem, a path is defined as any sequence of nodes from some starting node to any node in the tree along the parent-child connections. The path must contain **at least one node** and does not need to go through the root.

**Example 1:**

```text
Input: [1,2,3]

       1
      / \
     2   3

Output: 6
```

**Example 2:**

```text
Input: [-10,9,20,null,null,15,7]

   -10
   / \
  9  20
    /  \
   15   7

Output: 42
```

### 题意和分析

求二叉树的最大路径，这道题很容易想到DFS，但因为起始位置和结束位置可以是任意位置，所以有点难度。我们知道Tree相关的题目一般都用递归来做，树的递归解法一般是二话不说先递归到leaf，然后回溯到root，同时在回溯的过程中处理结点。以给定的例子稍微改变一下来看

```text
      -10
      / \
     9  20
    /  \
   15   7
```

最长路径为15-&gt;9-&gt;-10-&gt;20，现在递归到了15，接下来回溯到9，这时候以9为root的子树的和为15+9+7=31；然后继续回溯到-10，这个时候以9为root的地方得选一条路径，左路径或右路径，因为叶子结点的sum为15 &gt; 7，所以选左路径15-&gt;9-&gt;-10而不是7-&gt;9-&gt;-10，所以对回溯到的每个结点，就用递归函数返回值来定义为：当前结点为root，到leaf的最大路径之和，然后保留全局最大值在参数中，然后返回。

在递归函数中，如果当前结点不存在，那么直接返回0。否则就分别对其左右子节点调用递归函数，由于路径和（是 路径的和，不是某个结点的值）有可能为负数，而我们当然不希望加上负的路径和，所以我们和0相比，取较大的那个，也就是要么不加，要加就加正数。然后来更新全局最大值结果result，就是以左子结点为终点的最大path之和加上以右子结点为终点的最大path之和，还要加上当前结点值，这样就组成了一个条完整的路径。

### 代码

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
    int result;
    public int maxPathSum(TreeNode root) {
        result = Integer.MIN_VALUE;
        dfs(root);
        return result;
    }
    private int dfs(TreeNode node) { // 后序遍历
        if (node == null) { // 和为0
            return 0;
        }
        
        int left = Math.max(0, dfs(node.left));
        int right = Math.max(0, dfs(node.right));
        
        // 更新遍历在当前节点时的最大路径和
        result = Math.max(result, node.val + left + right); 
        
        // 选择以当前节点为根的含有最大值的路劲，左或右；返回给上一层递归的父节点作为路径
        return node.val + Math.max(left, right); 
    }
}
```

这道题的Follow up是打印这个最大路径，这时候递归函数需要返回该路径上所有的结点组成的数组，对左右子节点调用递归函数后得到的是路径上经过的元素组成的数组，需要统计出数组之和，跟0比较，如果小于0，这个和就清零，同时数组清空，然后更新最大路径之和跟数组，最后拼出来返回值的数组。

## 235 - Lowest Common Ancestor of a Binary Search Tree

### 原题概述

Given a binary search tree \(BST\), find the lowest common ancestor \(LCA\) of two given nodes in the BST.

According to the [definition of LCA on Wikipedia](https://en.wikipedia.org/wiki/Lowest_common_ancestor): “The lowest common ancestor is defined between two nodes p and q as the lowest node in T that has both p and q as descendants \(where we allow **a node to be a descendant of itself**\).”

Given binary search tree:  root = \[6,2,8,0,4,7,9,null,null,3,5\]

```text
        _______6______
       /              \
    ___2__          ___8__
   /      \        /      \
   0      _4       7       9
         /  \
         3   5
```

**Example 1:**

```text
Input: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
Output: 6
Explanation: The LCA of nodes 2 and 8 is 6.
```

**Example 2:**

```text
Input: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4
Output: 2
Explanation: The LCA of nodes 2 and 4 is 2, since a node can be a descendant of itself 
             according to the LCA definition.
```

**Note:**

* All of the nodes' values will be unique.
* p and q are different and both values will exist in the BST.

### 题意和分析

求二叉搜索树的最小共同祖先，正常的思路是用递归来求解，由于二叉搜索树的特点是左&lt;=根&lt;=右，所以根节点的值一直都是中间值，大于左子树的所有节点值，小于右子树的所有节点值，所以如果根节点的值大于给定的两个值p和q之间的较大值，说明p和q都在左子树中，那么此时我们就进入根节点的左子节点继续递归寻找共同父节点；如果根节点小于p和q之间的较小值，说明p和q都在右子树中，那么就进入根节点的右子节点继续递归，如果都不是（大于p和q中较小值而小于较大值），则说明当前根节点就是最小共同父节点，直接返回； 

如果是非递归的写法，就是把递归的过程用while来代替，但是每次循环需要更新一下当前的根节点。

### 代码

递归

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return null;
        }
        if (root.val < Math.min(p.val, q.val)) {//p，q都在右子树中
            return lowestCommonAncestor(root.right, p, q);
        } else if (root.val > Math.max(p.val, q.val)) {//p，q都在左子树中
            return lowestCommonAncestor(root.left, p, q);
        } else {//p，q一个在左一个在右，直接返回
            return root;
        }
    }
}
```

非递归

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return null;
        }
        while (true) {
            if (root.val < Math.min(p.val, q.val)) {
                root = root.right;
            } else if (root.val > Math.max(p.val, q.val)) {
                root = root.left;
            } else {
                break;
            }
        }
        return root;
    }
}
```

## 236 - Lowest Common Ancestor of Binary Tree

### 原题概述

Given a binary tree, find the lowest common ancestor \(LCA\) of two given nodes in the tree.

According to the [definition of LCA on Wikipedia](https://en.wikipedia.org/wiki/Lowest_common_ancestor): “The lowest common ancestor is defined between two nodes p and q as the lowest node in T that has both p and q as descendants \(where we allow **a node to be a descendant of itself**\).”

Given the following binary tree:  root = \[3,5,1,6,2,0,8,null,null,7,4\]

```text
        _______3______
       /              \
    ___5__          ___1__
   /      \        /      \
   6      _2       0       8
         /  \
         7   4
```

**Example 1:**

```text
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
Output: 3
Explanation: The LCA of of nodes 5 and 1 is 3.
```

**Example 2:**

```text
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
Output: 5
Explanation: The LCA of nodes 5 and 4 is 5, since a node can be a descendant of itself
             according to the LCA definition.
```

**Note:**

* All of the nodes' values will be unique.
* p and q are different and both values will exist in the binary tree.

### 题意和分析

这道题跟[235-Lowest Common Ancestor of a Binary Search Tree](https://guilindev.gitbook.io/interview/~/edit/drafts/-LITBKR8sHSxeQQ_2a5r/leetcode/ji-chu-shu-ju-jie-gou-zai-suan-fa-zhong-de-ying-yong/untitled-1#235-lowest-common-ancestor-of-a-binary-search-tree)相比，这道题是普通的二叉树，结点的值不会重复，不能用二叉搜索树的特征，同样用递归，先看当前结点是否为null，若为null直接返回null，如果为p或q中的任意一个，直接返回当前结点；如果都不是就对齐左右子结点，然后分别调用递归函数，因为p和q一定存在，所以如果当前结点不等于p或q的时候，有可能：

1）p和q分别在左右子树，对左右子结点调用递归函数，会分别返回p和q的位置，那当前结点恰好就是p和q的最小父结点，直接返回；

2）p和q都在左子树，这样又有两种情况，一种情况是left会返回p和q中较高的那个位置，而right会返回空，所以我们最终返回非空的left即可，这就是题目中的例子2的情况。还有一种情况是会返回p和q的最小父结点，就是说当前结点的左子树中的某个结点才是p和q的最小父结点，会被返回；

3）若p和q同时位于右子树，同样这里有两种情况，一种情况是right会返回p和q中较高的那个位置，而left会返回空，所以我们最终返回非空的right即可，还有一种情况是会返回p和q的最小父结点，就是说当前结点的右子树中的某个结点才是p和q的最小父结点，返回。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        //递归的基线条件，要么返回null，要么返回p或者q，然后递归回来的时候根据此结果判断
        if (root == null || p == root || q == root) {
            return root;
        }
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        
        //p，q分别在当前root的左右子树
        if (left != null && right != null) {
            return root;
        }
        return left != null ? left : right;//上面2）3）的分析
    }
}
```

如果当前结点不为空，且既不是p也不是q，那么根据上面的分析，p和q的位置就有三种情况，p和q要么分别位于左右子树中，或者同时位于左子树，或者同时位于右子树。这里需要优化的情况就是当p和q同时为于左子树或右子树中，而且返回的结点并不是p或q，那么就是p和q的最小父结点了，已经求出来了，就不用再对右结点调用递归函数了，同样，对返回的right也做同样的优化处理。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || p == root || q == root) {
            return root;
        }
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        if (left != null && left != p && left != q) {
            return left;
        }
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if (right != null && right != p && right != q) {
            return right;
        }
        
        //这是p和q分别在左右子树的情况，这个解法没有优化这种情况
        if (left != null && right != null) {
            return root;
        }

        return left != null ? left : right;
    }
}
```

##  654 - Maximum Binary Tree

### 原题概述

Given an integer array with no duplicates. A maximum tree building on this array is defined as follow:

1. The root is the maximum number in the array.
2. The left subtree is the maximum tree constructed from left part subarray divided by the maximum number.
3. The right subtree is the maximum tree constructed from right part subarray divided by the maximum number.

Construct the maximum tree by the given array and output the root node of this tree.

**Example 1:**  


```text
Input: [3,2,1,6,0,5]
Output: return the tree root node representing the following tree:

      6
    /   \
   3     5
    \    / 
     2  0   
       \
        1
```

**Note:**  


1. The size of the given array will be in the range \[1,1000\].

### 题意和分析

给一个整数数组，创建一个最大二叉树，最大二叉树的定义是最大值为root，然后它的左子树和右子树也是最大二叉树，分治法来递归；使用到了一个辅助数据结构v来让保持降序。遍历原数组，对于每个遍历到的元素，创建一个结点，然后进行循环，如果v不空，且末尾结点值小于当前数字，那么将末尾结点连到当前结点的左子结点，并且移除数组中的末尾结点，这样可以保证子结点都会小于父结点。循环结束后，如果此时v仍不为空，说明结点值很大，那么将当前结点连到数组末尾结点的右子结点上。之后将当前结点加入v中，最后返回v的首结点。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        LinkedList<TreeNode> v = new LinkedList<>();
        for (int num: nums){
            TreeNode cur = new TreeNode(num);
            while (!v.isEmpty() && v.peekFirst().val < cur.val){
                cur.left = v.pop();
            }

            if (!v.isEmpty()){
                v.peekFirst().right = cur;
            }
            v.push(cur);
        }

        return v.peekLast();
    }
}
```

或者使用双端队列

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        Deque<TreeNode> stack = new LinkedList<>();
        for (int i = 0; i < nums.length; i++) {
            TreeNode current = new TreeNode(nums[i]);
            while (!stack.isEmpty() && stack.peek().val < nums[i]) {
                current.left = stack.pop();
            }
            if (!stack.isEmpty()) {
                stack.peek().right = current;
            }
            stack.push(current);
        }
        return stack.isEmpty() ? null : stack.removeLast();
    }
}
```

## 105 Construct Binary Tree from Preorder and Inorder Traversal

### 原题概述

Given preorder and inorder traversal of a tree, construct the binary tree.

**Note:**  
You may assume that duplicates do not exist in the tree.

For example, given

```text
preorder = [3,9,20,15,7]
inorder = [9,3,15,20,7]
```

Return the following binary tree:

```text
    3
   / \
  9  20
    /  \
   15   7
```

### 题意和分析

这道题用先序和中序来建立二叉树，先序的顺序第一个肯定是root，所以二叉树的根结点可以确定，由于题目中说了没有相同的元素，所以利用先序的根我们可以找到这个根在中序的位置，并且在中序的数组中根结点为中心拆分成左右两部分，然后又用我们熟悉的递归调用就可以重建二叉树了。

步骤

```text
preorder = [3,9,20,15,7]
inorder = [9,3,15,20,7]
首先根据 preorder 找到根节点是 3
    
然后根据根节点将 inorder 分成左子树和右子树
左子树
inorder [9]

右子树
inorder [15,20,7]

把相应的前序遍历的数组也加进来
左子树
preorder[9] 
inorder [9]

右子树
preorder[20 15 7] 
inorder [15,20,7]

现在我们只需要构造左子树和右子树即可，成功把大问题化成了小问题
然后重复上边的步骤继续划分，直到 preorder 和 inorder 都为空，返回 null 即可
```

### 代码

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
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if (preorder == null || preorder.length == 0 || inorder == null || inorder.length == 0) {
            return null;
        }
        return buildTree(preorder, 0, preorder.length - 1, inorder, 0, inorder.length - 1);
    }
    private TreeNode buildTree(int[] preorder, int pLeft, int pRight, int[] inorder, int iLeft, int iRight) {
        if (pLeft > pRight || iLeft > iRight) { // base case 已超过叶子节点
            return null;
        }
        
        int index = 0; // 先序遍历的第一个元素总是根节点，同时寻找inorder对应根节点的位置，好分成左右两部分
        for (index = iLeft; index <= iRight; index++) {
            if (preorder[pLeft] == inorder[index]) {
                break;
            }
        }
        
        TreeNode currNode = new TreeNode(inorder[index]); // 当前递归轮的根节点
        
        // 递归构建当前节点的左右子树
        
        // preorder左边界少一个上一轮的root，右边界则是左边界往右index - iLeft的offset，其中index - iLeft表示中序数组中左子树的数目
        // inorder的左边界在左子树的递归中不变，右边界
        currNode.left = buildTree(preorder, pLeft + 1, pLeft + index - iLeft, inorder, iLeft, index - 1); 
        //preorder左边界，index - iLeft + 1的offset
        //inorder
        currNode.right = buildTree(preorder, pLeft + index - iLeft + 1, pRight, inorder, index + 1, iRight);
        
        return currNode;
    }
}
```

优化一下在中序数组中查找root位置的代码使用Hashmap来空间换时间

```java
class Solution {
    HashMap<Integer, Integer> map;
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if (preorder == null || preorder.length == 0 || inorder == null || inorder.length == 0 || preorder.length != inorder.length) {
            return null;
        }
        map = new HashMap<Integer, Integer>();
        // 注意没有重复值，所以可以用中序的元素当作key
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return buildTree(preorder, 0, preorder.length - 1, inorder, 0, inorder.length - 1);
    }

    private TreeNode buildTree (int[] preorder, int pLeft, int pRight, int[] inorder, int iLeft, int iRight) {
        if (pLeft > pRight || iLeft > iRight) { // 基线条件避免死循环
            return null;
        }

        // 1.根据先序当前的左边结点是root的特性重建根结点
        TreeNode current = new TreeNode(preorder[pLeft]);

        // 2.在中序中寻找重建的根结点的值
        int index = map.get(preorder[pLeft]);

        // 3.根据在中序中找到的root的左边是左子树，root的右边是右子树的特性，递归重建左右子树

        // 左子树，前序的左边不算root，到index处；中序的左边一直到找到的root（不含）处
        current.left = buildTree(preorder, pLeft + 1, pLeft + index - iLeft, inorder, iLeft, index - 1);
        // 右子树，从index到最右边
        current.right = buildTree(preorder, pLeft + index - iLeft + 1, pRight, inorder, index + 1, iRight);

        return current;
    }
}
```

迭代办法

* 我们用一个栈和一个指针辅助进行二叉树的构造。初始时栈中存放了根节点（前序遍历的第一个节点），指针指向中序遍历的第一个节点；
* 我们依次枚举前序遍历中除了第一个节点以外的每个节点。如果 index 恰好指向栈顶节点，那么我们不断地弹出栈顶节点并向右移动 index，并将当前节点作为最后一个弹出的节点的右儿子；如果 index 和栈顶节点不同，我们将当前节点作为栈顶节点的左儿子；
* 无论是哪一种情况，我们最后都将当前的节点入栈。

```java
class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if (preorder == null || preorder.length == 0) {
            return null;
        }
        TreeNode root = new TreeNode(preorder[0]);
        Stack<TreeNode> stack = new Stack<TreeNode>();
        stack.push(root);
        int inorderIndex = 0;
        for (int i = 1; i < preorder.length; i++) {
            int preorderVal = preorder[i];
            TreeNode node = stack.peek();
            if (node.val != inorder[inorderIndex]) {
                node.left = new TreeNode(preorderVal);
                stack.push(node.left);
            } else {
                while (!stack.isEmpty() && stack.peek().val == inorder[inorderIndex]) {
                    node = stack.pop();
                    inorderIndex++;
                }
                node.right = new TreeNode(preorderVal);
                stack.push(node.right);
            }
        }
        return root;
    }
}
```

## 106 Construct Binary Tree from Inorder and Postorder Traversal

### 原题概述

Given inorder and postorder traversal of a tree, construct the binary tree.

**Note:**  
You may assume that duplicates do not exist in the tree.

For example, given

```text
inorder = [9,3,15,20,7]
postorder = [9,15,7,20,3]
```

Return the following binary tree:

```text
    3
   / \
  9  20
    /  \
   15   7
```

### 题意和分析

这道题是根据中序和后序来重建二叉树，依然是用递归来做， 后序的顺序的最后一个肯定是根，所以原二叉树的根节点可以知道，题目中给了一个很关键的条件就是树中没有相同元素，有了这个条件就可以在中序遍历中也定位出根节点的位置，并以根节点的位置将中序遍历拆分为左右两个部分，分别对其递归调用原函数。整个过程如下：

1. 中序遍历中根节点是左子树右子树的分割点。
2. 后续遍历的最后一个节点为根节点。

同样根据中序遍历找到根节点的位置，然后顺势计算出左子树串的长度。在后序遍历中分割出左子树串和右子树串，递归的建立左子树和右子树。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        return buildTree(inorder, 0, inorder.length-1, postorder, 0, postorder.length - 1);
    }
    private TreeNode buildTree(int[] in, int inStart, int inEnd, int[] post, int postStart, int postEnd) {
        if (inStart > inEnd || postStart > postEnd) {
            return null;
        }
        int rootVal = post[postEnd];
        int rootIndex = 0;
        for (int i = 0; i <= inEnd; i++) {
            if (in[i] == rootVal) {
                rootIndex = i;
                break;
            }
        }
        int len = rootIndex - inStart;
        TreeNode root = new TreeNode(rootVal);
        root.left = buildTree(in, inStart, rootIndex - 1, post, postStart, postStart + len- 1);
        root.right = buildTree(in, rootIndex + 1, inEnd, post, postStart + len, postEnd - 1);

        return root;
    }
}
```

## 889 Construct Binary from Preorder and Postorder Traversal

### 原题概述

Return any binary tree that matches the given preorder and postorder traversals.

Values in the traversals `pre` and `post` are distinct positive integers.

**Example 1:**

```text
Input: pre = [1,2,4,5,3,6,7], post = [4,5,2,6,7,3,1]
Output: [1,2,3,4,5,6,7]
```

**Note:**

* `1 <= pre.length == post.length <= 30`
* `pre[]` and `post[]` are both permutations of `1, 2, ..., pre.length`.
* It is guaranteed an answer exists. If there exists multiple answers, you can return any of them.

### 题意和分析

前序和后序没有办法确定唯一的二叉树，返回任意一棵树就行。

### 代码

迭代， O\(n\)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    /**
     * 遍历pre，逐个重建node
     * 与pre+in和in+post一样，利用queue存储当前路径
     * node = new TreeNode(pre[i])，如果不是左孩子，就加到右孩子上面
     * 如果在pre和post中遇到了同样的值，说明当前子树的构建完成，从queue中pop出来
     */
    public TreeNode constructFromPrePost(int[] pre, int[] post) {
        Deque<TreeNode> queue = new ArrayDeque<>();
        queue.offer(new TreeNode(pre[0]));
        for (int i = 1,j =0; i < pre.length; i++) {
            TreeNode node = new TreeNode(pre[i]);
            while (queue.getLast().val == post[j]) {
                queue.pollLast();
                j++;
            }
            if (queue.getLast().left == null) {
                queue.getLast().left = node;
            } else {
                queue.getLast().right = node;
            }
            queue.offer(node);
        }
        return queue.getFirst();
    }
}
```

递归

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode constructFromPrePost(int[] pre, int[] post) {
        return constructFromPrePost(pre, 0, pre.length-1, post, 0, post.length-1);
    }

    public TreeNode constructFromPrePost(int[] pre, int preL, int preR, int[] post, int postL, int postR) {
        if (preL > preR || postL > postR) {
            return null;
        }
        TreeNode root = new TreeNode(pre[preL]);
        if (preL == preR) {
            return root;
        }

        int index = -1;
        for (int i = postL ; i < postR ; i++) {
            if (pre[preL+1] == post[i]) {
                index = i;
                break;
            }
        }
        if (index == -1) {
            return root;
        }

        root.left = constructFromPrePost(pre, preL+1, preL+1+(index-postL), post, postL, index);
        root.right = constructFromPrePost(pre, preL+1+(index-postL)+1, preR, post, index+1, postR);

        return root;
    }
}
```

## 297 Serialize and Deserialize Binary Tree

### 原题概述

Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize a binary tree. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that a binary tree can be serialized to a string and this string can be deserialized to the original tree structure.

**Example:** 

```text
You may serialize the following tree:

    1
   / \
  2   3
     / \
    4   5

as "[1,2,3,null,null,4,5]"
```

**Clarification:** The above format is the same as [how LeetCode serializes a binary tree](https://leetcode.com/faq/#binary-tree). You do not necessarily need to follow this format, so please be creative and come up with different approaches yourself.

**Note:** Do not use class member/global/static variables to store states. Your serialize and deserialize algorithms should be stateless.

### 题意和分析

将一个数据结构或者对象（比如这道题的一个二叉树）转换为位序列，为序列化；同时还可以转换回来，为反序列化。位序列可以存储在内存，文件或者网络连接中。 注意不让用全局变量，成员变量和类变量，以此保持无状态。这题可以有两种解法，分别为DFS递归解法（先序+中序，或者中序+后序）和BFS非递归解法（层序）。

### 代码

先序遍历的递归解法

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        serialize(root, sb);
        return sb.toString();
    }
    
    private void serialize(TreeNode root, StringBuilder sb) {
        // 先序遍历序列化，如果遇到空值，填充#，在deserialize的时候根据#判断左右孩子为空的情况
        if (root == null) {
            sb.append("#").append(",");
        } else {
            sb.append(root.val).append(",");
            serialize(root.left, sb);
            serialize(root.right, sb);
        }
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        Queue<String> queue = new LinkedList<>(Arrays.asList(data.split(",")));
        return deserialize(queue);
    }
    
    private TreeNode deserialize(Queue<String> queue) {
        String nodeStr = queue.poll();
        if (nodeStr.equals("#")) {
            return null;
        }
        //按照serialize先序根左右的顺序挨个从队列中取出来即可
        TreeNode root = new TreeNode(Integer.parseInt(nodeStr));
        root.left = deserialize(queue);
        root.right = deserialize(queue);
        
        return root;
    }
}

// Your Codec object will be instantiated and called as such:
// Codec codec = new Codec();
// codec.deserialize(codec.serialize(root));
```

层序遍历的非递归解法

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        if (root == null) return "";
        Queue<TreeNode> q = new LinkedList<>();
        StringBuilder res = new StringBuilder();
        q.add(root);
        while (!q.isEmpty()) {
            TreeNode node = q.poll();
            if (node == null) {
                res.append("n ");
                continue;
            }
            res.append(node.val + " ");
            q.add(node.left);
            q.add(node.right);
        }
        return res.toString();
    }



    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if (data == "") return null;
        Queue<TreeNode> q = new LinkedList<>();
        String[] values = data.split(" ");
        TreeNode root = new TreeNode(Integer.parseInt(values[0]));
        q.add(root);
        for (int i = 1; i < values.length; i++) {
            TreeNode parent = q.poll();
            if (!values[i].equals("n")) {
                TreeNode left = new TreeNode(Integer.parseInt(values[i]));
                parent.left = left;
                q.add(left);
            }
            if (!values[++i].equals("n")) {
                TreeNode right = new TreeNode(Integer.parseInt(values[i]));
                parent.right = right;
                q.add(right);
            }
        }
        return root;
    }
}

// Your Codec object will be instantiated and called as such:
// Codec codec = new Codec();
// codec.deserialize(codec.serialize(root));
```

## 449 Serialize and Deserialize BST

### 题目

Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize a **binary search tree**. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that a binary search tree can be serialized to a string and this string can be deserialized to the original tree structure.

**The encoded string should be as compact as possible.**

**Note:** Do not use class member/global/static variables to store states. Your serialize and deserialize algorithms should be stateless.

### 分析

297是普通二叉树，这道题是BST，对于二叉搜索树而言，左子节点 &lt; 根节点 &lt; 右子节点；根据这个性质，用先序遍历序列（根左右）就可以进行重建了，并且不需要存储空指针。 如果是一个普通二叉树，需要前序和中序一起，或者后序和中序两个序列，才能确定唯一的二叉树结构，而对于BST，由于各元素之间有左子节点小于父节点再小于右子节点的性质，因此用一个前序遍历就可以确定出唯一结构，所以要简单些。

当然，也可以用297的一个层序遍历来确定，同297，只是不用用BST的特性来优化了。

### 代码

Preorder

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        serialize(root, sb);
        return sb.toString();
    }
    
    private void serialize(TreeNode root, StringBuilder sb) {
        if (root == null) {
            return;
        }
        // 先序遍历拼接当前节点
        sb.append(root.val).append(","); // 使用分隔符来区别树节点
        serialize(root.left, sb);
        serialize(root.right, sb);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if (data.isEmpty()) {
            return null;
        }
        // 用数组来做二分查找，比用297中用的queue要方便一些
        String[] arr = data.split(",");
        return deserialize(arr, 0, arr.length - 1); //去掉分隔符后的数组来重构BST    
    }
    
    private TreeNode deserialize(String[] arr, int low, int high) {
        if (low > high) {
            return null;
        }
        TreeNode root = new TreeNode(Integer.parseInt(arr[low])); // 建立父节点
        int index = high + 1; // 初始化，右子树为空的情况
        for (int i = low + 1; i <= high; i++) {
            if (Integer.parseInt(arr[i]) > root.val) {
                index = i;
                break;
            }
        }
        // 递归构建子树，二分查找优化
        root.left = deserialize(arr, low + 1, index - 1);
        root.right = deserialize(arr, index, high);
        return root;
    }
}

// Your Codec object will be instantiated and called as such:
// Codec codec = new Codec();
// codec.deserialize(codec.serialize(root));
```

## 99 Recover Binary Search Tree

### 原题概述

Two elements of a binary search tree \(BST\) are swapped by mistake.

Recover the tree without changing its structure.

**Example 1:**

```text
Input: [1,3,null,null,2]

   1
  /
 3
  \
   2

Output: [3,1,null,null,2]

   3
  /
 1
  \
   2
```

**Example 2:**

```text
Input: [3,1,4,null,null,2]

  3
 / \
1   4
   /
  2

Output: [2,1,4,null,null,3]

  2
 / \
1   4
   /
  3
```

**Follow up:**

* A solution using O\(_n_\) space is pretty straight forward.
* Could you devise a constant space solution?

### 题意和分析

参考了这里（[http://blog.csdn.net/linhuanmars/article/details/24566995](http://blog.csdn.net/linhuanmars/article/details/24566995) ）这道题是要求恢复一颗有两个元素调换错了的二叉查找树。一开始拿到可能会觉得比较复杂，其实观察出规律了就比较简单。主要还是利用二叉查找树的主要性质，就是中序遍历是有序的性质。那么如果其中有元素被调换了，意味着中序遍历中必然出现违背有序的情况。那么会出现几次呢？

有两种情况，如果是中序遍历相邻的两个元素被调换了，很容易想到就只需会出现一次违反情况，只需要把这个两个节点记录下来最后调换值就可以；如果是不相邻的两个元素被调换了，举个例子很容易可以发现，会发生两次逆序的情况，那么这时候需要调换的元素应该是第一次逆序前面的元素，和第二次逆序后面的元素。比如1234567,1和5调换了，会得到5234167，逆序发生在52和41，我们需要把4和1调过来，那么就是52的第一个元素，41的第二个元素调换即可。 搞清楚了规律就容易实现了，中序遍历寻找逆序情况，调换的第一个元素，永远是第一个逆序的第一个元素，而调换的第二个元素如果只有一次逆序，则是那一次的后一个，如果有两次逆序则是第二次的后一个。算法只需要一次中序遍历，所以时间复杂度是O\(n\)，空间是栈大小O\(logn\)。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {

    public void recoverTree(TreeNode root) {
        if (root == null) {
            return;
        }
        ArrayList<TreeNode> pre = new ArrayList<TreeNode>();
        pre.add(null);
        ArrayList<TreeNode> result = new ArrayList<TreeNode>();
        helper(root, pre, result);
        if (result.size() > 0) {
            int temp = result.get(0).val;
            result.get(0).val = result.get(1).val;
            result.get(1).val = temp;
        }
    }
    private void helper(TreeNode root, ArrayList<TreeNode> pre, ArrayList<TreeNode> result) {
        if (root == null) {
            return;
        }
        helper(root.left, pre, result);
        if (pre.get(0) != null && pre.get(0).val > root.val) {
            if (result.size() == 0) {
                result.add(pre.get(0));
                result.add(root);
            } else {
                result.set(1, root);
            }
        }
        pre.set(0, root);
        helper(root.right, pre, result);
    }
}
```

这里有大神介绍了Morris Traversal（[http://www.cnblogs.com/AnnieKim/archive/2013/06/15/morristraversal.html](http://www.cnblogs.com/AnnieKim/archive/2013/06/15/morristraversal.html)），为常数级空间复杂度，符合followup的要求。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {

    public void recoverTree(TreeNode root) {
        TreeNode pre = null;
        TreeNode first = null, second = null;
        // Morris Traversal
        TreeNode temp = null;
        while(root!=null){
            if(root.left!=null){
                // connect threading for root
                temp = root.left;
                while(temp.right!=null && temp.right != root)
                    temp = temp.right;
                // the threading already exists
                if(temp.right!=null){
                    if(pre!=null && pre.val > root.val){
                        if(first==null){first = pre;second = root;}
                        else{second = root;}
                    }
                    pre = root;

                    temp.right = null;
                    root = root.right;
                }else{
                    // construct the threading
                    temp.right = root;
                    root = root.left;
                }
            }else{
                if(pre!=null && pre.val > root.val){
                    if(first==null){first = pre;second = root;}
                    else{second = root;}
                }
                pre = root;
                root = root.right;
            }
        }
        // swap two node values;
        if(first!= null && second != null){
            int t = first.val;
            first.val = second.val;
            second.val = t;
        }
    }
}
```

## 257 Binary Tree Paths

### 原题概述

Given a binary tree, return all root-to-leaf paths.

**Note:** A leaf is a node with no children.

**Example:**

```text
Input:

   1
 /   \
2     3
 \
  5

Output: ["1->2->5", "1->3"]

Explanation: All root-to-leaf paths are: 1->2->5, 1->3
```

### 题意和分析

直接递归来做，比较简单，时间和空间都是O\(n\)。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
   public List<String> binaryTreePaths(TreeNode root) {
      List<String> result = new ArrayList<>();
      if (root == null) {
         return result;
      }
      findPaths(root, result, "");
      return result;
   }
   private void findPaths(TreeNode root, List<String> result, String onePath) {
      if (root.left == null && root.right == null) {
         result.add(onePath + root.val);
      }
      if (root.left != null) {
         findPaths(root.left, result, onePath + root.val + "->");
      }
      if (root.right != null) {
         findPaths(root.right, result, onePath + root.val + "->");
      }
   }
}
```

另外一种递归的写法，不用额外的方法

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
   public List<String> binaryTreePaths(TreeNode root) {
      List<String> paths = new ArrayList<String>();

      if (root == null) {
         return paths;
      }
      if (root.left == null && root.right== null) {
         paths.add(root.val + "");
         return paths;
      }

      for (String path : binaryTreePaths(root.left)) {
         paths.add(root.val + "->" + path);
      }

      for (String path : binaryTreePaths(root.right)) {
         paths.add(root.val + "->" + path);
      }

      return paths;
   }
}
```

## 112 Path Sum 

### 原题概述 

Given a binary tree and a sum, determine if the tree has a root-to-leaf path such that adding up all the values along the path equals the given sum.

**Note:** A leaf is a node with no children.

**Example:**

Given the below binary tree and `sum = 22`,

```text
      5
     / \
    4   8
   /   / \
  11  13  4
 /  \      \
7    2      1
```

return true, as there exist a root-to-leaf path `5->4->11->2` which sum is 22.

### 题意和分析 

典型的递归解法，每次递归以后注意sum需要减去当前root的val。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
   public boolean hasPathSum(TreeNode root, int sum) {
      return dfs(root, sum);
   }
   private boolean dfs(TreeNode root, int sum) {
      if (root == null) {
         return false;
      }
      if (root.left == null && root.right == null && sum == root.val) {
         return true;
      }

      return dfs(root.left, sum - root.val) || dfs(root.right, sum - root.val);
   }
}
```

## 113 Path Sum II 

### 原题概述 

Given a binary tree and a sum, find all root-to-leaf paths where each path's sum equals the given sum.

**Note:** A leaf is a node with no children.

**Example:**

Given the below binary tree and `sum = 22`,

```text
      5
     / \
    4   8
   /   / \
  11  13  4
 /  \    / \
7    2  5   1
```

Return:

```text
[
   [5,4,11,2],
   [5,8,4,5]
]
```

### 题意和分析 

解法跟上面类似，只是需要找出所有的路径。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
   public List<List<Integer>> pathSum(TreeNode root, int sum) {
      List<List<Integer>> result = new ArrayList<>();
      List<Integer> onePath = new ArrayList<>();
      dfs(root, sum, result, onePath);
      return result;
   }
   private void dfs(TreeNode root, int sum, List<List<Integer>> result, List<Integer> onePath) {
      if (root == null) {
         return;//说明这条path已经走完，不符合要求，需要return
      }
      onePath.add(new Integer(root.val));//?
      if (root.left == null && root.right == null && sum == root.val) {
         result.add(new ArrayList<>(onePath));
         onePath.remove(onePath.size() - 1);
         return;//说明这条path已经走完，已经加入result，需要return
      }

      dfs(root.left, sum - root.val, result, onePath);
      dfs(root.right, sum - root.val, result, onePath);

      onePath.remove(onePath.size() - 1);
   }
}
```

## 129 Sum Root to Leaf Numbers 

### 原题概述 

Given a binary tree containing digits from `0-9` only, each root-to-leaf path could represent a number.

An example is the root-to-leaf path `1->2->3` which represents the number `123`.

Find the total sum of all root-to-leaf numbers.

**Note:** A leaf is a node with no children.

**Example:**

```text
Input: [1,2,3]
    1
   / \
  2   3
Output: 25
Explanation:
The root-to-leaf path 1->2 represents the number 12.
The root-to-leaf path 1->3 represents the number 13.
Therefore, sum = 12 + 13 = 25.
```

**Example 2:**

```text
Input: [4,9,0,5,1]
    4
   / \
  9   0
 / \
5   1
Output: 1026
Explanation:
The root-to-leaf path 4->9->5 represents the number 495.
The root-to-leaf path 4->9->1 represents the number 491.
The root-to-leaf path 4->0 represents the number 40.
Therefore, sum = 495 + 491 + 40 = 1026.
```

### 题意和分析 

这是一道树的题目，一般使用递归来做，主要就是考虑递归条件和结束条件。这道题思路还是比较明确的，目标是把从根到叶子节点的所有路径得到的整数都累加起来，递归条件即是把当前的sum乘以10并且加上当前节点传入下一函数，进行递归，最终把左右子树的总和相加。结束条件的话就是如果一个节点是叶子，那么我们应该累加到结果总和中，如果节点到了空节点，则不是叶子节点，不需要加入到结果中，直接返回0即可。算法的本质是一次先序遍历，所以时间是O\(n\)，空间是栈大小，O\(logn\)。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
   public int sumNumbers(TreeNode root) {
      return dfs(root, 0);
   }
   private int dfs(TreeNode root, int sum) {
      if (root == null) {
         return 0;
      }
      if (root.left == null && root.right == null) {
         return sum * 10 + root.val;
      }
      return dfs(root.left, sum * 10 + root.val) + dfs(root.right, sum * 10 + root.val);
   }
}
```

## 116 Populating Next Right Pointers in Each Node

### 原题概述

Given a binary tree

```text
struct TreeLinkNode {
  TreeLinkNode *left;
  TreeLinkNode *right;
  TreeLinkNode *next;
}
```

Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to `NULL`.

Initially, all next pointers are set to `NULL`.

**Note:**

* You may only use constant extra space.
* Recursive approach is fine, implicit stack space does not count as extra space for this problem.
* You may assume that it is a perfect binary tree \(ie, all leaves are at the same level, and every parent has two children\).

**Example:**

Given the following perfect binary tree,

```text
     1
   /  \
  2    3
 / \  / \
4  5  6  7
```

After calling your function, the tree should look like:

```text
     1 -> NULL
   /  \
  2 -> 3 -> NULL
 / \  / \
4->5->6->7 -> NULL
```

### 题意和分析

这道题是层序遍历的应用，找到每一层右边的结点，当遍历到一个节点时，处理该节点的左子树和右子树，这个处理的过程可以是递归或者非递归，两个方法都需要掌握。

### 代码

递归

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public Node left;
    public Node right;
    public Node next;

    public Node() {}
    
    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, Node _left, Node _right, Node _next) {
        val = _val;
        left = _left;
        right = _right;
        next = _next;
    }
};
*/

class Solution {
    public Node connect(Node root) {
        if (root == null) {
            return null;
        }
        if (root.left == null) { //只有一个元素的情况
            root.next = null;
            return root;
        }
        return recursion(root);
    }
    private Node recursion(Node node) {
        if (node == null || node.left == null) { // 该层的下层为空，该层已经处理过
            return null;
        }
        node.left.next = node.right;
        node.right.next = (node.next == null) ? null : node.next.left;
        
        recursion(node.left);
        recursion(node.right);
        
        return node;
    }
}   
```

非递归BFS

```java
class Solution {
    public Node connect(Node root) {
        if (root == null) {
            return null;
        }
        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);
        
        while (!queue.isEmpty()) {
            int size = queue.size();
            // daga
            for (int i = 0; i < size; i++) {
                Node node = queue.poll();
                // 当前层节点
                if (node.left != null) {
                    queue.offer(node.left);
                    node.left.next = node.right;
                }                
                
                if (node.right != null) {
                    queue.offer(node.right);
                    // 完美二叉树也需要处理最右边的节点，指向null
                    node.right.next = (node.next == null) ? null : node.next.left;
                }
            }            
        }
        return root;
    }

```

## 250 Count Univalue Subtrees

### 原题概述

Given a binary tree, count the number of uni-value subtrees.

A Uni-value subtree means all nodes of the subtree have the same value.

**Example :**

```text
Input:  root = [5,1,5,5,5,null,5]

              5
             / \
            1   5
           / \   \
          5   5   5

Output: 4
```

### 题意和分析

 可以用递归来做，第一种解法的思路是先序遍历树的所有的节点，然后对每一个节点调用判断以当前节点为根的字数的所有节点是否相同，用的是分治法的思想，分别对左右字数分别调用递归。

上面的那种解法简单但不是很高效，含有大量的重复check，可以一次遍历就都搞定，我们这样想，符合条件的相同值的字数肯定是有叶节点的，而且叶节点也都相同\(注意单独的一个叶节点也被看做是一个相同值子树\)，那么可以从下往上check，采用后序遍历的顺序，左右根，还是递归调用函数，对于当前遍历到的节点，如果对其左右子节点分别递归调用函数，返回均为true的话，那么说明当前节点的值和左右子树的值都相同，那么又多了一棵树，所以结果自增1，然后返回当前节点值和给定值\(其父节点值\)是否相同，从而回归上一层递归调用。这里特别说明一下在子函数中要使用的那个单竖杠或，为什么不用双竖杠的或，因为单竖杠的或是位或，就是说左右两部分都需要被计算，然后再或，C++这里将true当作1，false当作0，然后进行Bit OR 运算。不能使用双竖杠或的原因是，如果是双竖杠或，一旦左半边为true了，整个就直接是true了，右半边就不会再计算了，这样的话，一旦右子树中有值相同的子树也不会被计算到结果res中了

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int countUnivalSubtrees(TreeNode root) {
        int[] arr = new int[1];
        postOrder(arr, root);
        return arr[0];
    }
    private boolean postOrder(int[] arr, TreeNode root) {
        if (root == null) {
            return true;
        }
        boolean left = postOrder(arr, root.left);
        boolean right = postOrder(arr,root.right);
        if (left && right) {
            if (root.left != null && root.left.val != root.val) {
                return false;
            }
            if (root.right != null && root.right.val != root.val) {
                return false;
            }
            arr[0]++;
            return true;
        }
        return false;
    }
}
```

## 298  Binary Tree Longest Consecutive Sequence

### 原题概述

Given a binary tree, find the length of the longest consecutive sequence path.

The path refers to any sequence of nodes from some starting node to any node in the tree along the parent-child connections. The longest consecutive path need to be from parent to child \(cannot be the reverse\).

**Example 1:**

```text
Input:

   1
    \
     3
    / \
   2   4
        \
         5

Output: 3

Explanation: Longest consecutive sequence path is 3-4-5, so return 3.
```

**Example 2:**

```text
Input:

   2
    \
     3
    / 
   2    
  / 
 1

Output: 2 

Explanation: Longest consecutive sequence path is 2-3, not 3-2-1, so return 2.
```

### 题意和分析

在二叉树中求连续递增最长长度，依然用递归，传入的值包括当前结点，当前结点为止的长度，和应该达到的值（上一结点的值+1），对比当前，左子树和右子树，挑一个最大值返回。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
   public int longestConsecutive(TreeNode root) {
      if (root == null) {
         return 0;
      }
      return dfs(root, 0, root.val);
   }
   private int dfs(TreeNode root, int soFarLongest, int target) {
      if (root == null) {
         return soFarLongest;
      }
      if (root.val == target) {
         soFarLongest++;
      } else {
         soFarLongest = 1;
      }
      int left = dfs(root.left, soFarLongest, root.val + 1);
      int right = dfs(root.right, soFarLongest, root.val + 1);

      return Math.max(soFarLongest, Math.max(left, right));//比较当前为止的路径的结果，和左右子树的结果，返回最大的
   }
}
```

## 653 - 2Sum - Input is a BST

### 原题概述

Given a Binary Search Tree and a target number, return true if there exist two elements in the BST such that their sum is equal to the given target.

**Example 1:**  


```text
Input: 
    5
   / \
  3   6
 / \   \
2   4   7

Target = 9

Output: True
```

**Example 2:**  


```text
Input: 
    5
   / \
  3   6
 / \   \
2   4   7

Target = 28

Output: False
```

### 题意

在BST中寻找是否有两个nodes的val，它们相加的和等于target，返回true，否则返回false。

### 代码

## 543 Diameter of Binary Tree

### 题目

Given a binary tree, you need to compute the length of the diameter of the tree. The diameter of a binary tree is the length of the **longest** path between any two nodes in a tree. This path may or may not pass through the root.

**Example:**  
Given a binary tree  


```text
          1
         / \
        2   3
       / \     
      4   5    
```

Return **3**, which is the length of the path \[4,2,1,3\] or \[5,2,1,3\].

**Note:** The length of path between two nodes is represented by the number of edges between them.

### 分析

要求求出一个二叉树的直径，所谓直径就是树中一个结点到另外一个结点距离最长，用边表示直径。树的做法就用递归，DFS检查所有结点为顶点的直径，选择一个最大的，在每次递归的层里面，将所有结点的左子树和右子树递归的结果选择一个较大值，进入递归的上一层。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    int result;
    public int diameterOfBinaryTree(TreeNode root) {
        if (root == null) {
            return 0;
        }
        result = 1;
        paths(root);
        return result - 1;//边的数量是path中所有结点数量减1
    }
    private int paths(TreeNode node) {
        if (node == null) {
            return 0;
        }
        int L = paths(node.left);
        int R = paths(node.right);
        result = Math.max(result, L + R + 1); //以每个结点为root，计算通过自己root的最长path，与之前递归层比较

        //当前递归层应该返回左右子树中比较大的一个path
        return Math.max(L, R) + 1; //比较左右子树看那个path更长
    }
}
```



## 426 Convert Binary Search Tree to Sorted Doubly Linked List

### 题目

Convert a **Binary Search Tree** to a sorted **Circular Doubly-Linked List** in place.

You can think of the left and right pointers as synonymous to the predecessor and successor pointers in a doubly-linked list. For a circular doubly linked list, the predecessor of the first element is the last element, and the successor of the last element is the first element.

We want to do the transformation **in place**. After the transformation, the left pointer of the tree node should point to its predecessor, and the right pointer should point to its successor. You should return the pointer to the smallest element of the linked list.

**Example 1:**

![](https://assets.leetcode.com/uploads/2018/10/12/bstdlloriginalbst.png)

```text
Input: root = [4,2,5,1,3]


Output: [1,2,3,4,5]

Explanation: The figure below shows the transformed BST. The solid line indicates the successor relationship, while the dashed line means the predecessor relationship.

```

**Example 2:**

```text
Input: root = [2,1,3]
Output: [1,2,3]
```

**Example 3:**

```text
Input: root = []
Output: []
Explanation: Input is an empty tree. Output is also an empty Linked List.
```

**Example 4:**

```text
Input: root = [1]
Output: [1]
```

**Constraints:**

* `-1000 <= Node.val <= 1000`
* `Node.left.val < Node.val < Node.right.val`
* All values of `Node.val` are unique.
* `0 <= Number of Nodes <= 2000`

### 分析

这个题要求将一个 二叉搜索树 就地转化为一个已排序好的双向循环链表 。对于双向循环列表，可以将左右孩子指针作为双向循环链表的前驱和后继指针，第一个节点的前驱是最后一个节点，最后一个节点的后继是第一个节点。需要返回链表中最小元素的指针。

复习一下树的遍历方式：

总的来说，有两种遍历树的策略：

* 深度优先搜索 \(DFS\) 在深度优先搜索中，我们以 深度 优先，从根开始先抵达某个叶子，再回退以前往下一个分支。 深度优先搜索又可以根据根节点、左子结点和右子结点的顺序关系分为前序遍历，中序遍历和后序遍历。
* 广度优先搜索 \(BFS\) 逐层扫描整棵树，按照高度顺序自顶向下。上层的结点比下层更先访问。

下图表示了不同策略下的访问顺序，用 1-2-3-4-5 的顺序来比较不同的策略。

![](../../.gitbook/assets/image%20%28101%29.png)

按照题意，利用DFS的中序遍历+备忘录。

标准的中序遍历采用 左 -&gt; 根 -&gt; 右 的顺序，其中 左 和 右 的部分调用递归。 本题的处理在于将前一个结点与当前结点链接，因此，必须跟踪最后一个结点，该结点在新的双向链表中是当前最大的。

![](../../.gitbook/assets/image%20%28102%29.png)

同时，也需要保留第一个，也就是最小的结点，以完成闭环。 具体算法：

* 将 first 和 last 结点 初始化为 null。
* 调用标准中序遍历 inOrder\(root\) :
  * 若结点不为 null :
  * 调用左子树递归 inOrder\(node.left\)。
  * 若 last 结点不为空，将 last 与当前的 node 链接。
  * 否则初始化 first 结点。
  * 将当前结点标记为最后 : last = node.
  * 调用右子树递归 inOrder\(node.right\)。
* 将最前与最后的结点链接完成闭环，返回 first

### 代码

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public Node left;
    public Node right;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val,Node _left,Node _right) {
        val = _val;
        left = _left;
        right = _right;
    }
};
*/

class Solution {
  // 最小的(第一个)和最大的(最后一个)节点
  Node first = null;
  Node last = null;
    
  public Node treeToDoublyList(Node root) {
    if (root == null) {
        return null;
    }

    inOrder(root);
    // close DLL
    last.right = first;
    first.left = last;
    return first; // 返回最小值
  }

  public void inOrder(Node node) {
    if (node != null) { //开始中序遍历
        
      // left
      inOrder(node.left);
        
      // node 
      if (last != null) {
        // link the previous node (last) with the current one (node)
        last.right = node;
        node.left = last;
      } else {
        // keep the smallest node to close DLL later on
        first = node;
      }
        
      last = node;
        
      // right
      inOrder(node.right);
    }
  }
}
```

## 563 Binary Tree Tilt

### 题目

Given a binary tree, return the tilt of the **whole tree**.

The tilt of a **tree node** is defined as the **absolute difference** between the sum of all left subtree node values and the sum of all right subtree node values. Null node has tilt 0.

The tilt of the **whole tree** is defined as the sum of all nodes' tilt.

**Example:**  


```text
Input: 
         1
       /   \
      2     3
Output: 1
Explanation: 
Tilt of node 2 : 0
Tilt of node 3 : 0
Tilt of node 1 : |2-3| = 1
Tilt of binary tree : 0 + 0 + 1 = 1
```

**Note:**

1. The sum of node values in any subtree won't exceed the range of 32-bit integer.
2. All the tilt values won't exceed the range of 32-bit integer.

### 分析

从问题的描述，需要在给定树的每个结点处找到其坡度，并将所有的坡度相加以获得最终结果。要找出任意结点的坡度，我们需要求出该结点的左子树上所有结点和以及其右子树上全部结点和的差值。

因此，为了找出解决方案，使用递归函数，**在任何结点调用该函数**，都会返回当前结点下面（包括其自身）的结点和。借助于任何结点的左右子结点的这一和值，可以直接获得该结点所对应的坡度。

### 代码

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
    int tilt = 0;
    public int findTilt(TreeNode root) {
        traverse(root);
        return tilt;
    }
    private int traverse(TreeNode node) {
        // 后序，直接按照题目给的例子的顺序来写
        if (node == null) {
            return 0;
        }
        int left = traverse(node.left);
        int right = traverse(node.right);
        tilt += Math.abs(left - right); // 每一轮更新坡度
        return left + right + node.val;
    }
}
```



## 450 Delete Node in a BST

### 题目

Given a root node reference of a BST and a key, delete the node with the given key in the BST. Return the root node reference \(possibly updated\) of the BST.

Basically, the deletion can be divided into two stages:

1. Search for a node to remove.
2. If the node is found, delete the node.

**Note:** Time complexity should be O\(height of tree\).

**Example:**

```text
root = [5,3,6,2,4,null,7]
key = 3

    5
   / \
  3   6
 / \   \
2   4   7

Given key to delete is 3. So we find the node with value 3 and delete it.

One valid answer is [5,4,6,2,null,null,7], shown in the following BST.

    5
   / \
  4   6
 /     \
2       7

Another valid answer is [5,2,6,null,4,null,7].

    5
   / \
  2   6
   \   \
    4   7
```

### 分析

从当前根节点开始递归查找待删除节点，找到后处理根据待删除节点的情况来进行处理，见下面代码的说明，按照题目要求，时间复杂度O\(h\)，h是树的高度。

### 代码

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
    public TreeNode deleteNode(TreeNode root, int key) {
        return findKeyAndDelete(root, key);
    }
    private TreeNode findKeyAndDelete(TreeNode node, int key) {
        if (node == null) { // 基线条件，递归到node为null的时候没找到key，返回null
            return null;
        }
        
        if (key < node.val) { // 递归查找并更新node左子树
            node.left = findKeyAndDelete(node.left, key);
        } else if (key > node.val) {// 递归查找并更新node右子树
            node.right = findKeyAndDelete(node.right, key);
        } else { // 已找到待删除的节点
            /**
            处理四种情况：
            1. 待删除节点没有left和right，返回null，表示直接就删除了
            2. 待删除节点只有left没有right，返回left，表示直接接上left
            3. 待删除节点没有left只有right，返回right，表示直接接上right
            4. 待删除节点既有left又有right，返回right中的最小节点，根据BST特征这个最小节点应该是替代被删除的节点
            */
            if (node.left == null) {// 情况1,2
                return node.right;
            } else if (node.right == null) {// 情况3
                return node.left;
            }
            
            // 情况4，当前节点有左右子树，删除的操作是将当前待删除节点替换成右子树中的最小值，然后删除右子树中的最小值
            TreeNode minNode = findMin(node.right);
            node.val = minNode.val;
            node.right = findKeyAndDelete(node.right, minNode.val);
        }
        return node;
    }
    
    private TreeNode findMin(TreeNode node) {
        while (node.left != null) {
            node = node.left;
        }
        return node;
    }
}
```

## 272 Closest Binary Search Tree Value II

### 题目

Given a non-empty binary search tree and a target value, find k values in the BST that are closest to the target.

**Note:**

* Given target value is a floating point.
* You may assume k is always valid, that is: k ≤ total nodes.
* You are guaranteed to have only one unique set of k values in the BST that are closest to the target.

**Example:**

```text
Input: root = [4,2,5,1,3], target = 3.714286, and k = 2

    4
   / \
  2   5
 / \
1   3

Output: [4,3]
```

**Follow up:**  
 Assume that the BST is balanced, could you solve it in less than O\(n\) runtime \(where n = total nodes\)?

### 分析

中序遍历，维护一个LinkedList，从最左端开始遍历，达到k个后，新来的元素跟最早加入的元素对比看哪个更近，如果新来的元素更近就剔出最早的元素，这一点很重要，为什么是跟最早的元素比较？设想如果target离最早的元素很近，那么新来的元素只会比最后加入的元素离target更远，更不会比最早的元素近；如果target离最早的元素很远（往右子树方向走），那么这时候就应该剔出最早的元素。所以新来的元素跟最早进入linkedlist的元素对比是正确的，所以如果新来的元素不比最早的元素近，那么直接返回当前的k个，因为BST后面的元素只会更远了。 

这道题不用LinkedList，可以用List或者PriorityQueue，但试了下感觉麻烦一些

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    LinkedList<Integer> results; // 这里需要用到LinkedList实现而不是List接口，才能使用下面的一些API
    public List<Integer> closestKValues(TreeNode root, double target, int k) {
        results = new LinkedList<>();
        inOrder(root, target, k);
        return results;
    }
    private void inOrder(TreeNode node, double target, int k) {
        if (node == null) {
            return;
        }
        //先中序遍历到最左边
        inOrder(node.left, target, k);

        if (k > results.size()) { // 还未到kk个
            results.add(node.val);
        } else { // 已有k个candidates，开始检查
            if (Math.abs(node.val - target) < Math.abs(results.peekFirst() - target)) {
                // 剔除最先加入的元素，仔细想想，如果新来的元素比最先加入的元素离target更近，那么最先加入的元素一定是最远的一个
                results.pollFirst();
                results.add(node.val);
            } else {// 如果新来的元素不比最先加入的元素近，根据BST的特征，后面（右子树）的元素不用继续查了
                return; 
            }
        }

        // 不够k个再遍历右子树
        inOrder(node.right, target, k);
    }
}
```

Follow up是如果给定的二叉树是平衡的，可不可以将时间复杂度降到O\(logn\)，利用两个栈来保存target的前驱和后继, 而且两个栈顶的元素保存的是当前距离target最近的前驱和后继, 这样就可以每次取到距离target最近的值. 这种时间复杂度可以达到O\(logn\).

## 366 Find Leaves of Binary Tree

### 题目

Given a binary tree, collect a tree's nodes as if you were doing this: Collect and remove all leaves, repeat until the tree is empty.

Example:

Input: \[1,2,3,4,5\]

```text
      1
     / \
    2   3
   / \     
  4   5    
```

Output: \[\[4,5,3\],\[2\],\[1\]\]

Explanation:

1. Removing the leaves \[4,5,3\] would result in this tree:

   ```text
      1
     / 
    2          
   ```

2. Now removing the leaf \[2\] would result in this tree:

   ```text
      1          
   ```

3. Now removing the leaf \[1\] would result in the empty tree:

   ```text
      []         
   ```

\[\[3,5,4\],\[2\],\[1\]\], \[\[3,4,5\],\[2\],\[1\]\], etc, are also consider correct answers since per each level it doesn't matter the order on which elements are returned.

### 分析

使用dfs，递归时传入上一个节点，如果当前节点是叶子节点，则将值加入集合，使用上个节点将当前叶子节点删除。每递归一次就会修剪掉叶子节点，形成新树，循环修剪叶子节点，直至只剩下根节点，最后将根节点加入结果集。

递归的思想不要一个元素一个元素或者一层一层的角度去想，而是把返回条件写好后，直接考虑左子树和右子树的关系即可，不要想太复杂。

### 代码

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
    public List<List<Integer>> findLeaves(TreeNode root) {
        List<List<Integer>> results = new ArrayList<>();
        while (root != null) {
            List<Integer> oneLevel = new ArrayList<>();
            // 每一层的更新让递归栈来保存 该层叶子节点和当前oneLevel数组的信息，每个oneLevel对应的叶子节点都是最外层
            root = dfs(root, oneLevel); 
            results.add(oneLevel);
        }
        return results;
    }

    private TreeNode dfs(TreeNode node, List<Integer> oneLevel) {
        if (node == null) { // 寻找叶子节点过程中的base case，比如只有左子树或者只有右子树为空
            return null;
        }
        if (node.left == null && node.right == null) { // 叶子节点
            oneLevel.add(node.val);
            return null; // 叶子节点加入list后，从该层返回
        }

        // 递归检查左右子树，它们有自己对应的oneLevel和递归参数在递归栈中
        node.left = dfs(node.left, oneLevel);
        node.right = dfs(node.right, oneLevel);

        return node;
    }
}
```

## 114 Flatten Binary Tree to Linked List

### 题目

Given a binary tree, flatten it to a linked list in-place.

For example, given the following tree:

```text
    1
   / \
  2   5
 / \   \
3   4   6
```

The flattened tree should look like:

```text
1
 \
  2
   \
    3
     \
      4
       \
        5
         \
          6
```

### 分析

1\)解法1，直观想象，可以发现展开的顺序其实就是二叉树的先序遍历。算法和 94 题中序遍历的 Morris 算法有些神似，我们需要两步完成这道题。

1. 将左子树插入到右子树的地方 
2. 将原来的右子树接到左子树的最右边节点 
3. 考虑新的右子树的根节点，一直重复上边的过程，直到新的右子树为 null

如图：

```text
    1
   / \
  2   5
 / \   \
3   4   6

//将 1 的左子树插入到右子树的地方
    1
     \
      2         5
     / \         \
    3   4         6     
       
//将原来的右子树接到左子树的最右边节点
    1
     \
      2          
     / \          
    3   4  
         \
          5
           \
            6
            
 //将 2 的左子树插入到右子树的地方
    1
     \
      2          
       \          
        3       4  
                 \
                  5
                   \
                    6   
        
 //将原来的右子树接到左子树的最右边节点
    1
     \
      2          
       \          
        3      
         \
          4  
           \
            5
             \
              6         
  
  ......
```

2\) 上面1的步骤，因为如果直接用先序遍历，把当前节点的左子树插入到当前节点的右子树位置的话，会把当前位置的右子树的信息丢掉，因此也可以特殊处理一下，先将当前节点的右子树保存在一个stack中，等左子树接上后再弹出接到左孩子下面的位置。

3\)上面2的解法用stack保存右子树，其实用后序遍历，先将最右下的右子树处理了再说，就不用额外数据结构了

### 代码

1\) 直观解法

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
    public void flatten(TreeNode root) {
        TreeNode node = root;
        while (node != null) {
            if (node.left == null) { // 左子树为空，不用管直接去右子树
                node = node.right;
            } else {                
                // 用一个节点记录先找到当前节点左子树的最右节点，让当前节点的右子树可以接入
                TreeNode leftRight = node.left;
                while (leftRight.right != null) {
                    leftRight = leftRight.right;
                }
                
                // 将当前结点的右子树接到左子树的
                leftRight.right = node.right;
                
                // 将当前节点的左子树挪到当前节点的右子树位置
                node.right = node.left;
                
                node.left = null; // 移除当前左子树
                
                // 移向下一个节点
                node = node.right;
            }
        }
    }
}
```

2\) 

```java
class Solution {
    public void flatten(TreeNode root) {
        if (root == null) {
            return;
        }
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        TreeNode pre = null; // 接入的位置
        
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            
            if (pre != null) { //当前节点(上一轮得到)接入stack里面弹出来的元素
                pre.right = node;
                pre.left = null;
            }

            if (node.right != null) { // 先压入右子树，待会后接入
                stack.push(node.right);
            }

            if (node.left != null) { // 再压入左子树，待会先接入
                stack.push(node.left);
            }

            pre = node;
            
        }
    }
}
```

3\) 后序遍历递归和迭代解法

```java
class Solution {
    private TreeNode pre;
    public void flatten(TreeNode root) {
        pre = null;
        postOrder(root);
    }
    private void postOrder(TreeNode node) {
        if (node == null) {
            return;
        }
        postOrder(node.right);
        postOrder(node.left);
        
        node.right = pre;
        node.left = null;
        
        pre = node;
    }
}
```

```java
class Solution {
    public void flatten(TreeNode root) { 
        Stack<TreeNode> toVisit = new Stack<>();
        TreeNode cur = root;
        TreeNode pre = null;

        while (cur != null || !toVisit.isEmpty()) {
            while (cur != null) {
                toVisit.push(cur); // 添加根节点
                cur = cur.right; // 递归添加右节点
            }
            cur = toVisit.peek(); // 已经访问到最右的节点了
            // 在不存在左节点或者右节点已经访问过的情况下，访问根节点
            if (cur.left == null || cur.left == pre) {
                toVisit.pop(); 
                /**************修改的地方***************/
                cur.right = pre;
                cur.left = null;
                /*************************************/
                pre = cur;
                cur = null;
            } else {
                cur = cur.left; // 左节点还没有访问过就先访问左节点
            }
        } 
    }
}
```

## 285 Inorder Successor in BST

### 题目

Given a binary search tree and a node in it, find the in-order successor of that node in the BST.

The successor of a node `p` is the node with the smallest key greater than `p.val`.

**Example 1:** ![](https://assets.leetcode.com/uploads/2019/01/23/285_example_1.PNG)

```text
Input: root = [2,1,3], p = 1
Output: 2
Explanation: 1's in-order successor node is 2. Note that both p and the return value is of TreeNode type.
```

**Example 2:** ![](https://assets.leetcode.com/uploads/2019/01/23/285_example_2.PNG)

```text
Input: root = [5,3,6,2,4,null,null,1], p = 6
Output: null
Explanation: There is no in-order successor of the current node, so the answer is null.
```

**Note:**

1. If the given node has no in-order successor in the tree, return `null`.
2. It's guaranteed that the values of the tree are unique.

### 分析

中序遍历结果是一个递增的数组，顺序后继是中序遍历中当前节点 之后 最小的节点。

方法1：递归执行中序遍历，获取一个list，在结果list中得到p的下一个。时间O\(N\)，空间O\(N\)

方法2： 递归执行中序遍历，在递归过程中获取x的下一个。如果当前值是&lt;=x的，那么根据BST的特性只需要在右子树中找。如果当前值&gt;x，则当前值有可能，它的左子树也有可能有更小的但是也&gt;x的，对左子树递归后，选择更接近的（更小的\)。时间O\(logN\)，空间O\(logN\)调用栈的深度。

方法3：方法2的循环实现，如果当前值是&lt;=x的，那么根据BST的特性只需要在右子树中找：cur=cur.right。 如果当前值&gt;x，则当前值有可能，它的左子树也有可能有更小的但是也&gt;x的。则每次走入这个分支时，当前点是一个候选点，记录该节点的值和历史最小节点的值。 时间O\(logN\)，空间O\(1）

### 代码

方法1

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    List<TreeNode> list;
    public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
        list = new ArrayList<>();
        inOrder(root);
        
        TreeNode result = null;
        for (int i = 0; i < list.size(); i++) {
            if (list.get(i).val == p.val) {
                if (i != list.size() - 1) {
                    result = list.get(i + 1);
                }
            }
        }
        return result;
    }
    private void inOrder(TreeNode node) {
        if (node == null) {
            return;
        }
        inOrder(node.left);
        list.add(node);
        inOrder(node.right);
    }
}
```

方法2

```java
class Solution {
    public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
        return inOrder(root, p);
    }
    private TreeNode inOrder(TreeNode node, TreeNode p) {
        if (p == null || node == null) {
            return null;
        }
        if (node.val <= p.val) {//当前和左边都不可能大于p，继续递归右子树
            return inOrder(node.right, p);
        }
        //node > p
        TreeNode result = inOrder(node.left, p);
        if (result != null && result.val < node.val) {
            return result;
        } else {
            return node;
        }
    }
}
```

方法3

```java
class Solution {
    public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
        if (p == null || root == null) {
            return null;
        }
        TreeNode curr = root;
        TreeNode result = null;
        while (curr != null) {
            if (curr.val <= p.val) {
                curr = curr.right;
            } else {
                if (result == null || result.val > curr.val) {
                    result = curr;
                }
                curr = curr.left;
            }
        }
        return result;
    }

}
```

## 1008 Construct Binary Search Tree from Preorder Traversal

### 题目

Return the root node of a binary **search** tree that matches the given `preorder` traversal.

_\(Recall that a binary search tree is a binary tree where for every node, any descendant of `node.left` has a value `<` `node.val`, and any descendant of `node.right` has a value `>` `node.val`.  Also recall that a preorder traversal displays the value of the `node` first, then traverses `node.left`, then traverses `node.right`.\)_

It's guaranteed that for the given test cases there is always possible to find a binary search tree with the given requirements.

**Example 1:**

```text
Input: [8,5,1,7,10,12]
Output: [8,5,10,1,7,null,12]

```

**Constraints:**

* `1 <= preorder.length <= 100`
* `1 <= preorder[i] <= 10^8`
* The values of `preorder` are distinct.

### 分析

给一个数组，是一个BST的先序遍历，构建返回对应的BST。

1\) 方法1，类似105题-Construct Binary Tree from Preorder and Inorder Traversal的做法：在105题中，需要一个先序数组和一个中序数组，由于BST的中序遍历是递增的，可以根据先序遍历排序得到一个中序遍历的数组，随后使用分治的方法从先序遍历和中序遍历构造出二叉搜索树。

具体的方法是，先获取先序遍历中的第一个元素，它对应了二叉树的根节点，它在中序遍历中的位置为 x，那么中序遍历中 x 左边的所有元素都在根节点的左子树中，x右边的所有元素都在根节点的右子树中，这样根节点的左子树和右子树中的节点个数就都确定了。再回到先序遍历数组中，由于根节点的左子树和右子树在先序遍历中一定都是连续的一段，并且它们的个数已经确定，且左子树的先序遍历出现在右子树之前，那么就得到了根节点左子树和右子树对应的先序遍历。有了子树的先序遍历和中序遍历，就可以递归地构造子树了。过程如下图：

![](../../.gitbook/assets/image%20%28109%29.png)

时间复杂度：对先序数组要排序O\(NlogN\)；

空间复杂度：额外维护一个中序遍历数组，O\(N\)；

2\) 方法2，在方法1中在将先序遍历的数组排序得到中序遍历时，花费了O\(Nlog⁡N\)时间复杂度，但却没有得到任何额外的信息。也就是说，可以直接跳过生成中序遍历的步骤，根据先序遍历直接构造出二叉树。需要注意的是，由于题目中的二叉树是BST，因此根据先序遍历构造出的二叉树才是唯一的。

具体做法就是使用递归的方法，在扫描先序遍历数组的同时，构造出二叉树。递归时维护一个 \(lower, upper\)二元组，表示当前位置可以插入的节点的值的上下界。如果此时先序遍历位置的值处于上下界中，就将这个值作为新的节点插入到当前位置，并递归地处理当前位置的左右孩子的两个位置。否则回溯到当前位置的父节点。

* 将 lower 和 upper 的初始值分别设置为负无穷和正无穷，因为根节点的值可以为任意值。
* 从先序遍历的第一个元素 idx = 0 开始构造二叉树，构造使用的函数名为 helper\(lower, upper\)：
  * 如果 idx = n，即先序遍历中的所有元素已经被添加到二叉树中，那么此时构造已经完成；
  * 如果当前 idx 对应的先序遍历中的元素 val = preorder\[idx\] 的值不在 \[lower, upper\] 范围内，则进行回溯；
  * 如果 idx 对应的先序遍历中的元素 val = preorder\[idx\] 的值在 \[lower, upper\] 范围内，则新建一个节点 root，并对其左孩子递归处理 helper\(lower, val\)，对其右孩子递归处理 helper\(val, upper\)。

![](../../.gitbook/assets/image%20%28108%29.png)



时间复杂度：O\(N\)，仅扫描前序遍历一次。

空间复杂度：O\(N\)，用来存储二叉树。

3\) 方法3，方法2中的递归过程用stack来模拟

* 将先序遍历中的第一个元素作为二叉树的根节点，即 root = new TreeNode\(preorder\[0\]\)，并将其放入栈中。
* 使用 for 循环迭代先序遍历中剩下的所有元素：

  * 将栈顶的元素作为父节点，当前先序遍历中的元素作为子节点。如果栈顶的元素值小于子节点的元素值，则将栈顶的元素弹出并作为新的父节点，直到栈空或栈顶的元素值大于子节点的元素值。注意，这里作为父节点的是最后一个被弹出栈的元素，而不是此时栈顶的元素；
  * 如果父节点的元素值小于子节点的元素值，则子节点为右孩子，否则为左孩子；
  * 将子节点放入栈中。

时间复杂度：O\(N\)，仅扫描前序遍历一次。

空间复杂度：O\(N\)，用来存储栈和二叉树。

### 代码

方法1

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
    // 从前序数组的第一个元素开始
    int preorderIndex = 0;
    int[] preorderArr;
    HashMap<Integer, Integer> indexMap = new HashMap<>();

    public TreeNode bstFromPreorder(int[] preorder) {
        this.preorderArr = preorder;

        // 得到排序后的中序遍历数组
        int[] inorderArr = Arrays.copyOf(preorder, preorder.length);
        Arrays.sort(inorderArr);

        // build a hashmap value -> its index
        int idx = 0;
        for (Integer val : inorderArr) {
            indexMap.put(val, idx++);
        }
        // 类似105题，使用中序数组递归构建二叉树（BST）
        return helper(0, inorderArr.length);
    }

    public TreeNode helper(int in_left, int in_right) {
        // if there is no elements to construct subtrees
        if (in_left == in_right)
            return null;

        // 先序遍历的第一个元素是根节点
        int root_val = preorderArr[preorderIndex];
        TreeNode root = new TreeNode(root_val);

        // root splits inorder list
        // into left and right subtrees
        int index = indexMap.get(root_val);

        // recursion
        preorderIndex++;
        // build left subtree
        root.left = helper(in_left, index);
        // build right subtree
        root.right = helper(index + 1, in_right);
        return root;
    }
}
```

方法2

```java
class Solution {
    int idx = 0;
    int[] preorderArr;
    int len;

    public TreeNode bstFromPreorder(int[] preorder) {
        this.preorderArr = preorder;
        len = preorder.length;
        return helper(Integer.MIN_VALUE, Integer.MAX_VALUE);
    }

    public TreeNode helper(int lower, int upper) {
        // 如果所有先序遍历数组中的元素都用过了，说明BST已经构建完成
        if (idx == len) {
            return null;
        }

        // 得到当前元素
        int val = preorderArr[idx];
        
        // corner case，按照BST的特性，如果当前元素不能够被放在当前位置
        if (val < lower || val > upper) {
            return null;
        }

        // 构建当前元素
        idx++;
        TreeNode root = new TreeNode(val);
        并递归构建左右子树
        root.left = helper(lower, val);
        root.right = helper(val, upper);
        return root;
    }
}
```

方法3

```java
class Solution {
    public TreeNode bstFromPreorder(int[] preorder) {
        int len = preorder.length;
        if (len == 0) {
            return null;
        }

        TreeNode root = new TreeNode(preorder[0]);
        Deque<TreeNode> deque = new ArrayDeque<>();
        deque.push(root);

        for (int i = 1; i < len; i++) {
            // take the last element of the deque as a parent
            // and create a child from the next preorder element
            TreeNode node = deque.peek();
            TreeNode child = new TreeNode(preorder[i]);
            // adjust the parent 
            while (!deque.isEmpty() && deque.peek().val < child.val)
                node = deque.pop();

            // follow BST logic to create a parent-child link
            if (node.val < child.val) node.right = child;
            else node.left = child;
            // add the child into deque
            deque.push(child);
        }
        return root;
    }
}
```

  



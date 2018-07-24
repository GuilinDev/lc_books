# Tree

树在数据结构中占有非常重要的地位。本文从树的基本概念入手，给出完美\(Perfect\)二叉树，完全\(Complete\)二叉树和完满\(Full\)二叉树的区别。 **如果学习过二叉树，但是对这三种二叉树并没有深入的理解，或者完全被中文数据结构教科书所误导\(只听说过满二叉树和完全二叉树\)的朋友不妨花点时间耐着性子将本文仔细阅读N\(&gt;=1\)遍。**

**1.** [**树\(Tree\)**](https://en.wikipedia.org/wiki/Tree_%28data_structure%29)**的基本概念**

**1.1 树的定义**

```text
A tree is a (possibly non-linear) data structure made up of nodes or vertices 
and edges without having any cycle. The tree with no nodes is called the null 
or empty tree. A tree that is not empty consists of a root node and potentially 
many levels of additional nodes that form a hierarchy.
```

树是由结点或顶点和边组成的\(可能是非线性的\)且不存在着任何环的一种数据结构。没有结点的树称为空\(null或empty\)树。一棵非空的树包括一个根结点，还\(很可能\)有多个附加结点，所有结点构成一个多级分层结构。

\[注：本文将node一律译为"结点"\(而不是"节点"\)，因为joint或connection是节点，而node是结点。关于"结点"与"节点"请自行搜索浙江大学陈水福教授的文章--"360度"解读如何正确应用"结点"与"节点"\]

例如：

![](https://images2015.cnblogs.com/blog/1094457/201702/1094457-20170225102115945-363779980.png)

```text
A simple unordered tree; in this diagram, the node labeled 7 has two children, 
labeled 2 and 6, and one parent, labeled 2. The root node, at the top, 
has no parent. 上图是一棵无序的树示例。在上图中，标号为7的结点有两个孩子，分别是标号为2和6的结点。
根结点，在最顶端，它没有双亲。
```

**1.2 树的基本术语**

| **Root** | The top node in a tree. | **根** | 树的顶端结点 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Child** | A node directly connected to another node when moving away from the Root. | **孩子** | 当远离根\(Root\)的时候，直接连接到另外一个结点的结点被称之为孩子\(Child\);  |
| **Parent** | The converse notion of a child. | **双亲** | 相应地，另外一个结点称为孩子\(child\)的双亲\(parent\)。 |
| **Siblings** | A group of nodes with the same parent. | **兄弟** | 具有同一个双亲\(Parent\)的孩子\(Child\)之间互称为兄弟\(Sibling\)。 |
| **Ancestor** | A node reachable by repeated proceeding from child to parent. | **祖先** | 结点的祖先\(Ancestor\)是从根（Root）到该结点所经分支\(Branch\)上的所有结点。 |
| **Descendant** | A node reachable by repeated proceeding from parent to child. | **子孙** | 反之，以某结点为根的子树中的任一结点都称为该结点的子孙\(Ancestor\)。 |
| **Leaf** | A node with no children. | **叶子（终端结点）** | 没有孩子的结点\(也就是度为0的结点\)称为叶子\(Leaf\)或终端结点。 |
| **Branch** | A node with at least one child. | **分支\(非终端结点\)** | 至少有一个孩子的结点称为分支\(Branch\)或非终端结点。 |
| **Degree** | The number of sub trees of a node. | **度** | 结点所拥有的子树个数称为结点的度\(Degree\)。 |
| **Edge** | The connection between one node and another. | **边** | 一个结点和另一个结点之间的连接被称之为边\(Edge\)。 |
| **Path** | A sequence of nodes and edges connecting a node with a descendant. | **路径** | 连接结点和其后代的结点之间的\(结点,边\)的序列。  |
| **Level** | The level of a node is defined by ０ + \(the number of connections between the node and the root\). | **层次** | 结点的层次\(Level\)从根\(Root\)开始定义起，根为第0层，根的孩子为第1层。以此类推，若某结点在第i层，那么其子树的根就在第i+1层。 |
| **Height of node** | The height of a node is the number of edges on the longest path between that node and a leaf. | **结点的高度** | 结点的高度是该结点和某个叶子之间存在的最长路径上的边的个数。  |
| **Height of tree** | The height of a tree is the height of its root node. | **树的高度** | 树的高度是其根结点的高度。  |
| **Depth of node**  | The depth of a node is the number of edges from the tree's root node to the node. | **结点的深度** | 结点的深度是从树的根结点到该结点的边的个数。（注：树的深度指的是树中结点的最大层次。） |
| **Forest** | A forest is a set of n ≥ 0 disjoint trees. | **森林** | 森林是n\(&gt;=0\)棵互不相交的树的集合。 |

**2 二叉树（Binary Tree\)**

**2.1 什么是二叉树（Binary Tree\)**

每个结点至多拥有两棵子树\(即二叉树中不存在度大于2的结点\)，并且，二叉树的子树有左右之分，其次序不能任意颠倒。

**2.2 二叉树的性质**

（1）若二叉树的层次从0开始，则在二叉树的第i层至多有2^i个结点\(i&gt;=0\)。

（2）高度为k的二叉树最多有2^\(k+1\) - 1个结点\(k&gt;=-1\)。 \(空树的高度为-1\)

（3）对任何一棵二叉树，如果其叶子结点\(度为0\)数为m, 度为2的结点数为n, 则m = n + 1。

**2.3 完美二叉树\(Perfect Binary Tree\)**

```text
A Perfect Binary Tree(PBT) is a tree with all leaf nodes at the same depth. 
All internal nodes have degree 2.
```

一个深度为k\(&gt;=-**1**\)且有2^\(k+1\) - 1个结点的二叉树称为**完美二叉树**。 \(注： 国内的数据结构教材大多翻译为"满二叉树"\)

例如：

![](https://images2015.cnblogs.com/blog/1094457/201702/1094457-20170225183610632-1388959691.png)

**2.4 完全二叉树\(Complete Binary Tree\)**  


```text
A Complete Binary Tree （CBT) is a binary tree in which every level, 
except possibly the last, is completely filled, and all nodes 
are as far left as possible.
```

换句话说，完全二叉树从根结点到倒数第二层满足完美二叉树，最后一层可以不完全填充，其叶子结点都靠左对齐。

例如：

![](https://images2015.cnblogs.com/blog/1094457/201702/1094457-20170225183236538-961758163.png)

**2.5 完满二叉树\(Full Binary Tree\)**

```text
A Full Binary Tree (FBT) is a tree in which every node other than the leaves has two children.
```

换句话说，**所有非叶子结点的度都是2**。（**只要你有孩子，你就必然是有两个孩子。**） 

**注：**Full Binary Tree又叫做Strictly Binary Tree。

例如：

![](https://images2015.cnblogs.com/blog/1094457/201702/1094457-20170225183305616-1864401342.png)

**2.6 完美\(Perfect\)二叉树 v.s.** **完全\(Complete\)二叉树**

\(1\) 一棵完美\(**Perfect**\)二叉树看起来是这个样儿的， 【图2.6.1】

![](https://images2015.cnblogs.com/blog/1094457/201702/1094457-20170225194740741-9886325.png)

（2） 那么，将编号为15, 14, ..., 9的叶子结点从右到左依次拿掉或者拿掉部分，则是一棵完全\(Complete\)二叉树,

例如，将上图中的编号为15, 14, 13, 12, 11叶子结点都拿掉\(从右到左的顺序\)， 【图2.6.2】

![](https://images2015.cnblogs.com/blog/1094457/201702/1094457-20170225194935460-153137074.png)

（3） 下图就不是一棵完全\(Complete\)二叉树，【图2.6.3】，

如果将编号11\(K\)结点从编号6\(E\)的左儿子位置移动到编号5\(E\)的右儿子位置，则变成一棵完全\(Complete\)二叉树。

![](https://images2015.cnblogs.com/blog/1094457/201702/1094457-20170225195252648-1767156678.png)

**注：** 图2.6.1， 2.6.2和2.6.3均来自：http://alrightchiu.github.io/SecondRound/binary-tree-introjian-jie.html， 但是，其将Full Binary Tree当做就是Perfect Binary Tree, 作者认为是不正确的，特此说明。

**特别说明：** 其实，理解完全\(Complete\)二叉树可以借助于**栈\(stack\)**的思想。 例如，把图2.6.1中的完美\(Perfect\)二叉树的所有结点按照编号1, 2, 3, ..., 15依次入栈\(push\)。 那么，对栈的每一次出栈\(pop\)操作后，栈里保存的结点集对应到图2.6.1上去都是一棵完全\(Complete\)二叉树。

**2.7 完全\(Complete\)二叉树 v.s. 完满\(Full\)二叉树**

![](https://images2015.cnblogs.com/blog/1094457/201702/1094457-20170225183951601-609950717.png)

**2.8 完满\(Full\)二叉树 v.s. 完全\(Complete\)二叉树 v.s. 完美\(Perfect\)二叉树**

![](https://images2015.cnblogs.com/blog/1094457/201702/1094457-20170225184451991-1942362001.png)

**3. 总结** \(下表参考[来源](http://stackoverflow.com/questions/12359660/difference-between-complete-binary-tree-strict-binary-tree-full-binary-tre)\)

| **完美二叉树** | Perfect Binary Tree | Every node except the leaf nodes have two children and every level \(last level too\) is completely filled. **除了叶子结点之外的每一个结点都有两个孩子，每一层\(当然包含最后一层\)都被完全填充。** |
| --- | --- | --- |
| **完全二叉树** | Complete Binary Tree | Every level except the last level is completely filled and all the nodes are left justified. **除了最后一层之外的其他每一层都被完全填充，并且所有结点都保持向左对齐。** |
| **完满二叉树** | Full/Strictly Binary Tree | Every node except the leaf nodes have two children. **除了叶子结点之外的每一个结点都有两个孩子结点。** |

* 完美\(Perfect\)二叉树一定是完全\(Complete\)二叉树，但完全\(Complete\)二叉树不一定是完美\(Perfect\)二叉树。
* 完美\(Perfect\)二叉树一定是完满\(Full\)二叉树，但完满\(Full\)二叉树不一定是完美\(Perfect\)二叉树。
* 完全\(Complete\)二叉树可能是完满\(Full\)二叉树，完满\(Full\)二叉树也可能是完全\(Complete\)二叉树。
* 既是完全\(Complete\)二叉树又是完满\(Full\)二叉树也不一定就是完美\(Perfect\)二叉树。

来源并加以修改：[https://www.cnblogs.com/idorax/p/6441043.html](https://www.cnblogs.com/idorax/p/6441043.html)

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

DFS，用两个stack，一个用来记录Tree的结点，另一个用来记录最大深度。

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

BFS，用队列来实现

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
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);//add()
        int count = 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            while (size-- > 0) {
                TreeNode node = queue.poll();
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
            count++;
        }
        return count;
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
        TreeNode left = root.left;
        TreeNode right = root.right;
        root.left = invertTree(right);
        root.right = invertTree(left);

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
        if (p == null && q == null) {//递归检查到末尾还没return false那就是return true了
            return true;
        }
        if (p == null || q == null) {
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
 *     TreeNode(int x) { val = x; }
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
            TreeNode node1 = queue.poll();
            TreeNode node2 = queue.poll();

            if (node1 == null && node2 == null) {//一直检查到最后为null
                continue;
            } else if (node1 == null || node2 == null || node1.val != node2.val) {
                return false;
            }
            queue.offer(node1.left);
            queue.offer(node2.left);
            queue.offer(node1.right);
            queue.offer(node2.right);
        }
        return true;
    }
}
```


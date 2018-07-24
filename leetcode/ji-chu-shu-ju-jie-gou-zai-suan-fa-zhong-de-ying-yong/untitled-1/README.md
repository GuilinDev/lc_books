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

## 173 - Binary Search Tree Iterator

### 原题概述

Implement an iterator over a binary search tree \(BST\). Your iterator will be initialized with the root node of a BST.

Calling `next()` will return the next smallest number in the BST.

**Note:** `next()` and `hasNext()` should run in average O\(1\) time and uses O\(h\) memory, where h is the height of the tree.

### 题意和分析

这道题是对二叉搜索树的遍历，要求next\(\)返回下一个最小的val，next\(\)和hasNext\(\)的时间复杂度为O\(1\)，空间为O\(h\)，其中h为高度。BST的建树规则是left-root-right，这个跟中序遍历一样。

> 每个节点的val值大于左子节点，小于右子节点。注意它不一定是完全的二叉树。所有结点的val值是唯一的。数组的搜索比较方便，可以直接使用下标，但删除或者插入就比较麻烦了，而链表与之相反，删除和插入都比较简单，但是查找很慢，这自然也与这两种数据结构的存储方式有关，数组是取一段相连的空间，而链表是每创建一个节点便取一个节点所需的空间，只是使用指针进行连接，空间上并不是连续的。而二叉树就既有链表的好处，又有数组的优点。

![&#x4E2D;&#x5E8F;&#x904D;&#x5386;&#x7684;&#x987A;&#x5E8F;&#x662F;GDHBEAKCIJF](../../../.gitbook/assets/image.png)

下面的思路来自于[https://blog.csdn.net/smile\_watermelon/article/details/47280679](https://blog.csdn.net/smile_watermelon/article/details/47280679)并做了修改

一个非常简单的解决办法是遍历这棵二叉树，把二叉树的元素以从大到小的方式放到一个栈里，这样next\(\)从栈顶获取元素，hasNext\(\)调用栈不为空的判定方法。这种方法能满足题目中的时间复杂度要求，但是O\(n\)的空间复杂度无法满足题目中O\(h\)空间复杂度要求。

我们维护一个栈，栈中存放的是结点指针，**向栈中添加元素**发生在两种情况下：1.初始化迭代器时；2.调用next\(\)从栈中弹出一个元素时。

我们结合下面的实例来描述这个栈中元素的添加和删除过程。对于hasNext\(\)方法，调用栈的empty\(\)方法即可，不做具体分析，仅分析next\(\)方法以及迭代器的数据结构。

> 假设二叉查找树的结构如下：

```text
         6
       /   \
      5     8
     /     / \
    1     7   9
     \      
      3  
     / \
    2   4
12345678910
```

> （1）栈的初始化，我们把root一直到最左下角的结点都放到stack中，即把如下三个结点放在stack中。

```text
        6    |   |
       /     |   |
      5      | 1 |
     /       | 5 |
    1        |_6_|  <---栈内情况
     注意：上图中栈中存放的应该是指针而不是1,5,6等元素，此处仅用作示意，不代表实际情况，勿生疑。
1234567
```

> （2）第一次调用next\(\)，此时弹出指向元素1的指针tmp，这时候栈内只剩下5,6，如果我们不对栈进行维护的话，我们再调用一次next\(\)将会弹出5，这显然是不对的，因为二叉查找树中还有比5小比2大的元素。因此这时候，我们应当将二叉查找树，或者更准确的说，应该把元素1的右子树上的部分结点添加到栈中。

```text
             |   |
             |   |
             |   |
             | 5 |
             |_6_|  <---栈内情况
123456
```

> （3）那么我们将1（tmp）的右子树上所有结点都放到栈内吗？不，此时添加结点的逻辑与初始化迭代器时类似， 我们只把从tmp-&gt;right为根一直到其最坐下角的结点都放到stack中，即，把3,2两个元素放到栈中。

```text
             |   |
             | 2 |
             | 3 |
             | 5 |
             |_6_|  <---栈内情况
123456
```

> （4）再次调用next\(\)时，返回的将是正确的答案2，然后2并没有右结点，则无须添加新的指针到栈中。

```text
             |   |
             |   |
             | 3 |
             | 5 |
             |_6_|  <---栈内情况
123456
```

> （5）之后的过程与此类似，在此把每次调用next\(\)之后的栈的存储情况列举如下：

```text
             |   |       |   |       |   |       |   |       |   |       |   |       |   |
             |   |       |   |       |   |       |   |       |   |       |   |       |   |
             | 4 |  -->  |   |  -->  |   |  -->  |   |  -->  |   |  -->  |   |  -->  |   |
             | 5 |       | 5 |       |   |       | 7 |       |   |       |   |       |   |
             |_6_|       |_6_|       |_6_|       |_8_|       |_8_|       |_9_|       |___|
123456
```

综上所述，我们在栈中存储的是一部分极小值，且栈顶一定是当前树中未访问过的结点中的最小结点。在调用next\(\)弹出最小结点时，需要把被弹出结点的右子树添加到栈中，也就是把比当前最小结点大但是比次小结点小的那些结点中的一部分（不是全部，只是左孩子分支上的一部分）添加到栈中。

然后，我们的这个数据结构是否满足题目要求。我们可以看到每一个结点一定要进入栈中，且该操作是在构造过程和next\(\)中执行的，其中构造过程只执行一次，而next\(\)执行多次。但是进栈的操作存在差异性，有时候调用next\(\)不会往栈中添加任何元素，有时候会添加多个，但是至多不超过h个，其中h是树的高度，因为添加时只顺着树的左分支往下添加。我们一共要调用n次next\(\)，在这n次调用中，每个结点都进栈/出栈，共n次入栈操作，n次出栈操作，平均起来，**每次next\(\)只有`一次入栈操作+一次出栈操作`**，因此**满足平均时间复杂度O\(1\)**的要求。再看空间复杂度，刚才我们已经说过，在往栈中添加元素时**只顺着树的左分支往下添加**，因此**至多添加h个结点**，其中h就是树的高度，因此**满足空间复杂度O\(h\)**的要求。

### 代码

```java
/**
 * Definition for binary tree
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */

public class BSTIterator {

    public BSTIterator(TreeNode root) {
        while (root != null) {//left - root - right,从最left开始
            stack.push(root);
            root = root.left;
        }
    }

    /** @return whether we have a next smallest number */
    public boolean hasNext() {
        return !stack.isEmpty();
    }

    /** @return the next smallest number */
    public int next() {//
        TreeNode node = stack.pop();
        int result = node.val;
        if (node.right != null) {
            node = node.right;
            while (node != null) {
                stack.push(node);
                node = node.left;
            }
        }
        return result;
    }

    private Stack<TreeNode> stack = new Stack<>();
}

/**
 * Your BSTIterator will be called like this:
 * BSTIterator i = new BSTIterator(root);
 * while (i.hasNext()) v[f()] = i.next();
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

![](../../../.gitbook/assets/image%20%2811%29.png)

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
        // 令num = t + 1, 则t = num -1;
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

用动态规划的两个套路，**自底向上**和**自顶向下来解**。

### 代码


# Tree

### 104 - Maximum Depth of Binary Tree

#### 原题概述

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

#### 题意和分析

给一颗二叉树，找到其最大的深度并返回。可以用递归，DFS和BFS来做。

#### 代码

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

BFS，用Queue来实现

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

### 226 - Invert Binary Tree

#### 原题概述

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

#### 题意和分析

同样可以用递归，DFS和BFS来做。

#### 代码

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

### 617 - Merge Two  Binary Tree

#### 原题概述

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

#### 题意和分析

同样可以用递归来做，将一棵树merge到另一颗树中。

#### 代码

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

### 100 - Same Tree

#### 原题概述

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

#### 题意和分析

用递归和非递归分别实现。

#### 代码

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

### 173 - Binary Search Tree Iterator

#### 原题概述

Implement an iterator over a binary search tree \(BST\). Your iterator will be initialized with the root node of a BST.

Calling `next()` will return the next smallest number in the BST.

**Note:** `next()` and `hasNext()` should run in average O\(1\) time and uses O\(h\) memory, where h is the height of the tree.

#### 题意和分析

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

#### 代码

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

### 96 - Unique Binary Search Trees

#### 原题概述

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

#### 题意和分析

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

#### 代码

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

### 95 - Unique Binary Search Trees II

#### 原题概述

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

#### 题意和分析

跟上一题相比，这道题要求不是总数，而是打印出所有符合条件的BST。

大致思路如下：

1. 每一次都在一个范围内随机选取一个结点作为根。 
2. 每选取一个结点作为根，就把树切分成左右两个子树，直至该结点左右子树为空。

可以看出这也是一个可以划分成子问题求解的题目，所以考点是动态规划。 但具体对于本题来说，采取的是自底向上的求解过程。

 1. 选出根结点后应该先分别求解该根的左右子树集合，也就是根的左子树有若干种，它们组成左子树集合，根的右子树有若干种，它们组成右子树集合。 

2. 然后将左右子树相互配对，每一个左子树都与所有右子树匹配，每一个右子树都与所有的左子树匹配。然后将两个子树插在根结点上。 

3. 最后，把根结点放入链表中。

#### 代码

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

### 110 - Balanced Binary Tree

#### 原题概述

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

#### 题意和分析

平衡二叉树的定义：两个子树的高度相差不会超过1，而且左右子树都是平衡的。用动态规划的两个套路，**自底向上**和**自顶向下来解**。

#### 代码

自顶向下 - 这个办法利用递归，算出左右子树的深度，然后严格按照平衡二叉树的定义看左右子树的深度是否相差大于1。其中findDepth\(\)的复杂度需要遍历每个结点为O\(n\)，而对所有结点都会执行以下findDepth\(\)，所以总体时间复杂度为O\(n^2\)，空间为O\(n\)。

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

自底向上 - 利用DFS，在DFS的递归中返回当前结点的height，如果当前结点的子结点是平衡的，dfsHeight\(\)会返回一个非负整数，如果不平衡就返回-1，然后根据leftHeight和rightHeight的结果来决定一层层上升传递的数值。

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
        if (leftHeight == -1) {
            return -1;
        }
        int rightHeight = dfsHeight(root.right);
        if (rightHeight == -1) {
            return -1;
        }

        if (Math.abs(leftHeight - rightHeight) > 1) {
            return -1;
        }

        return Math.max(leftHeight, rightHeight) + 1;//返回当前结点的height
    }
}
```

### 108 - Convert Sorted Array to Binary Search Tree

#### 原题概述

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

#### 题意和分析

这道题是要将有序数组转为二叉搜索树，二叉搜索树始终满足左&lt;根&lt;右的特性，如果将二叉搜索树按中序遍历的话，得到的就是一个有序数组。那么反过来，根节点应该是有序数组的中间点，从中间点分开为左右两个有序数组，在分别找出其中间点作为原中间点的左右两个子节点，这不就是是二分查找法的核心思想么。所以这道题考的就是二分查找法。

#### 代码

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

### 235 - Lowest Common Ancestor of a Binary Search Tree

#### 原题概述

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

#### 题意和分析

求二叉搜索树的最小共同祖先，正常的思路是用递归来求解，由于二叉搜索树的特点是左&lt;=根&lt;=右，所以根节点的值一直都是中间值，大于左子树的所有节点值，小于右子树的所有节点值，所以如果根节点的值大于给定的两个值p和q之间的较大值，说明p和q都在左子树中，那么此时我们就进入根节点的左子节点继续递归寻找共同父节点；如果根节点小于p和q之间的较小值，说明p和q都在右子树中，那么就进入根节点的右子节点继续递归，如果都不是（大于p和q中较小值而小于较大值），则说明当前根节点就是最小共同父节点，直接返回； 

如果是非递归的写法，就是把递归的过程用while来代替，但是每次循环需要更新一下当前的根节点。

#### 代码

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

### 669 - Trim a Binary Search Tree

#### 原题概述

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

#### 题意和分析

给一个边界返回\[L, R\]，修剪给定的BST，所有不在范围内的结点都应该被剪掉，但是剩下的应该还是BST，左&lt;=根&lt;=右，如果是先遍历一遍BST，把符合要求的结点放入到一个Array里面，然后再重建一个新的BST，这样的想法可能会改变原来BST的总体结构；

所以用另外一种思路，在遍历的过程就对二叉树进行修剪，在递归的过程中判定根结点是否在范围中，如果根结点的值小于L，就返回根结点的右子结点调用递归的值；如果根结点大于R，就返回根结点的左子结点调用递归函数的值；如果根结点在范围内，就将起左右子结点同时更新为对其左右子结点调用递归函数的值，最后返回root。

#### 代码

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


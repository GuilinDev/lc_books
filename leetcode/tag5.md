# tag5

## 418 Sentence Screen Fitting

屏幕可显示句子的数量

给你一个 rows x cols 的屏幕和一个用 非空 的单词列表组成的句子，请你计算出给定句子可以在屏幕上完整显示的次数。

注意：

* 一个单词不能拆分成两行。 
* 单词在句子中的顺序必须保持不变。 
* 在一行中 的两个连续单词必须用一个空格符分隔。 
* 句子中的单词总量不会超过 100。 
* 每个单词的长度大于 0 且不会超过 10。 
* 1 ≤ rows, cols ≤ 20,000.

Given a `rows x cols` screen and a sentence represented by a list of **non-empty** words, find **how many times** the given sentence can be fitted on the screen.

**Note:**

1. A word cannot be split into two lines.
2. The order of words in the sentence must remain unchanged.
3. Two consecutive words **in a line** must be separated by a single space.
4. Total words in the sentence won't exceed 100.
5. Length of each word is greater than 0 and won't exceed 10.
6. 1 ≤ rows, cols ≤ 20,000.

 **Example 1:**

```text
Input:
rows = 2, cols = 8, sentence = ["hello", "world"]

Output: 
1

Explanation:
hello---
world---

The character '-' signifies an empty space on the screen.
```

 **Example 2:**

```text
Input:
rows = 3, cols = 6, sentence = ["a", "bcd", "e"]

Output: 
2

Explanation:
a-bcd- 
e-a---
bcd-e-

The character '-' signifies an empty space on the screen.
```

 **Example 3:**

```text
Input:
rows = 4, cols = 5, sentence = ["I", "had", "apple", "pie"]

Output: 
1

Explanation:
I-had
apple
pie-I
had--

The character '-' signifies an empty space on the screen.
```

### 分析

滑动窗口

1）计算以每个单词开始时，每一行可以装下多少个单词。使用滑动窗口的思想 

2）考虑cols特别长，而sentence比较短的情况：每一行都会出现很多句子，可以先去掉每行整句话所占用的长度，只填充剩下的屏幕。即使用newCol计算。 

3）计算屏幕中，可以装下的单词总数，即sum。

 4）结果 = sum/n + rows\*\(每一行整句话的个数\)

### 代码

```java
class Solution {
    public int wordsTyping(String[] sentence, int rows, int cols) {
        int n = sentence.length;
        int totalLen = 0; //length of sentence
        for(int i=0; i<n; i++) {
            //A word cannot be split into two lines.
            if(sentence[i].length() > cols) return 0;
            totalLen += sentence[i].length();
        }
        if(totalLen > rows*cols) return 0;

        totalLen += n-1;
        if(cols == totalLen) return rows;
        //in case that cols is very long
        int newCol = cols%(totalLen+1);
        //cnts[i]: start with sentence[i], there are cnt[i] words in this row
        int[] cnts = new int[n];
        //count: the number of words in the area
        //end: length of all counted words, start from sentence[cur] to sentence[cur+count-1]
        //cur: the index of cnts that is calculating
        int count=0, end=0, cur=0;
        for(int i=0; i<2*n; i++) {
            end += sentence[i%n].length() + 1; // add word and space
            count++;
            //more than one row, calculate cnts[cur], seems like slide window
            while(end >= newCol && cur < n) {
                //whether the last word contains
                cnts[cur] = end <= newCol +1 ? count : count-1;  
                //delete the first word [slide the left border of the window]          
                end -= sentence[cur].length() + 1;
                count--;    
                cur++;
               // System.out.println((cur-1) + "," + cnts[cur-1] +","+ count + "," + end);
            }
            if(cur >= n) break;
        }
        //the number of words in the screen 
        int sum = 0;
        cur = 0; //the first word in each row
        for(int i=0; i<rows; i++) {
            sum += cnts[cur];
            cur = (cnts[cur] + cur)%n;
          //  System.out.println(cur + "," + sum);
        }
        return rows*(cols/(totalLen+1)) + sum/n;
    }
}
```

模拟

设置当前行数r、当前行占用的列c、当前单词的index等几个变量，模拟输出单词的过程。同时，当发现一个完整的sentence输出完成，同时当前行无法再次放置一个单词的时候，表示出现了循环节（意味着下一行会重复输出一个新的sentence，此时我们直接计算所需的行数以及输出的sentence次数，再讲行数r定位到最后一个循环节的开头，加上最后的几次就得到最后的答案。

```java
class Solution {
    public int wordsTyping(String[] sentence, int rows, int cols) {
        int times = 0, i = 0, r = 1, c = 0;  //r从1开始计数，方便后序计算循环节
        while(r<=rows){
            int len = sentence[i].length();
            if (c+len > cols){  //当前行无法输出单词
                r++;
                c = 0;
            }else{
                c += len + 1;   //加上单词的长度包括空格
                i++;
                if (i==sentence.length){   //输出完一个完整句子
                    times++;
                    i = 0;
                    if (sentence[0].length() > cols-c){   //出现循环节
                        times = rows / r * times;         //**rows / r**计算完整循环节的个数，**times**是当前的句子数目也就是一个循环节中输出的句子数
                        r = rows - (rows % r) + 1;        //定位到最后一个**非完整**循环节的开头
                        c = 0;
                    }
                }
            }
        }
        return times;
    }
}

```

## 247 Strobogrammatic Number II

中心对称数是指一个数字在旋转了 180 度之后看起来依旧相同的数字（或者上下颠倒地看）。

找到所有长度为 n 的中心对称数。

A strobogrammatic number is a number that looks the same when rotated 180 degrees \(looked at upside down\).

Find all strobogrammatic numbers that are of length = n.

**Example:**

```text
Input:  n = 2
Output: ["11","69","88","96"]
```

### 分析

![](../.gitbook/assets/image%20%28184%29.png)

### 代码

```java
import java.util.*;
class Solution {
    public List<String> findStrobogrammatic(int n) {
        if (n <= 0) return new ArrayList<>();
        if (n == 1) return Arrays.asList("0", "1", "8");
        if (n == 2) return Arrays.asList("11", "69", "88", "96");
        // arr[n] 表示长度为 n 的所有中心对称数
        List<String>[] arr = new List[n + 1];
        arr[1] = Arrays.asList("0", "1", "8");
        arr[2] = Arrays.asList("00","11", "69", "88", "96");
        for (int i = 3; i <= n; i++) {
            // 随着 i 不断地增长，我们只需要在长度为 i-2 的中心对称数两边添加 11，69，88，96 即可。
            List<String> tmpList = new ArrayList<>();
            for (String s : arr[i - 2]) {
                if (i != n) {
                    tmpList.add("0" + s + "0");
                }
                tmpList.add("1" + s + "1");
                tmpList.add("6" + s + "9");
                tmpList.add("8" + s + "8");
                tmpList.add("9" + s + "6");
            }
            arr[i] = tmpList;
        }
        return arr[n];
    }
}
```

## 792 Number of Matching Subsequences

 给定字符串 `S` 和单词字典 `words`, 求 `words[i]` 中是 `S` 的子序列的单词个数。

Given string `S` and a dictionary of words `words`, find the number of `words[i]` that is a subsequence of `S`.

```text
Example :
Input: 
S = "abcde"
words = ["a", "bb", "acd", "ace"]
Output: 3
Explanation: There are three words in words that are a subsequence of S: "a", "acd", "ace".
```

**Note:**

* All words in `words` and `S` will only consists of lowercase letters.
* The length of `S` will be in the range of `[1, 50000]`.
* The length of `words` will be in the range of `[1, 5000]`.
* The length of `words[i]` will be in the range of `[1, 50]`.

### 分析

![](../.gitbook/assets/image%20%28191%29.png)

![](../.gitbook/assets/image%20%28187%29.png)

![](../.gitbook/assets/image%20%28188%29.png)

### 代码

```java
class Solution {
    public int numMatchingSubseq(String S, String[] words) {
        int ans = 0;
        ArrayList<Node>[] heads = new ArrayList[26];
        for (int i = 0; i < 26; ++i)
            heads[i] = new ArrayList<Node>();

        for (String word: words)
            heads[word.charAt(0) - 'a'].add(new Node(word, 0));

        for (char c: S.toCharArray()) {
            ArrayList<Node> old_bucket = heads[c - 'a'];
            heads[c - 'a'] = new ArrayList<Node>();

            for (Node node: old_bucket) {
                node.index++;
                if (node.index == node.word.length()) {
                    ans++;
                } else {
                    heads[node.word.charAt(node.index) - 'a'].add(node);
                }
            }
            old_bucket.clear();
        }
        return ans;
    }

}

class Node {
    String word;
    int index;
    public Node(String w, int i) {
        word = w;
        index = i;
    }
}
```

## 995 Minimum Number of K Consecutive Bit Flips

In an array `A` containing only 0s and 1s, a `K`-bit flip consists of choosing a \(contiguous\) subarray of length `K` and simultaneously changing every 0 in the subarray to 1, and every 1 in the subarray to 0.

Return the minimum number of `K`-bit flips required so that there is no 0 in the array.  If it is not possible, return `-1`.

**Example 1:**

```text
Input: A = [0,1,0], K = 1
Output: 2
Explanation: Flip A[0], then flip A[2].
```

**Example 2:**

```text
Input: A = [1,1,0], K = 2
Output: -1
Explanation: No matter how we flip subarrays of size 2, we can't make the array become [1,1,1].
```

**Example 3:**

```text
Input: A = [0,0,0,1,0,1,1,0], K = 3
Output: 3
Explanation:
Flip A[0],A[1],A[2]: A becomes [1,1,1,1,0,1,1,0]
Flip A[4],A[5],A[6]: A becomes [1,1,1,1,1,0,0,0]
Flip A[5],A[6],A[7]: A becomes [1,1,1,1,1,1,1,1]
```

**Note:**

1. `1 <= A.length <= 30000`
2. `1 <= K <= A.length`

### 分析

![](../.gitbook/assets/image%20%28186%29.png)

![](../.gitbook/assets/image%20%28185%29.png)

### 代码

```java
public int minKBitFlips(int[] A, int K) {
        int cur = 0, res = 0, n = A.length;
        for (int i = 0; i < n; ++i) {
            if (i >= K && A[i - K] > 1) {
                cur--;
                A[i - K] -= 2;
            }
            if (cur % 2 == A[i]) {
                if (i + K > n) return -1;
                A[i] += 2;
                cur++;
                res++;
            }
        }
        return res;
    }
```

## 963 Minimum Area Rectangle II

给定在 xy 平面上的一组点，确定由这些点组成的任何矩形的最小面积，其中矩形的边**不一定平行于** x 轴和 y 轴。

如果没有任何矩形，就返回 0。

Given a set of points in the xy-plane, determine the minimum area of **any** rectangle formed from these points, with sides **not necessarily parallel** to the x and y axes.

If there isn't any rectangle, return 0.

**Example 1:**

![](https://assets.leetcode.com/uploads/2018/12/21/1a.png)

```text
Input: [[1,2],[2,1],[1,0],[0,1]]
Output: 2.00000
Explanation: The minimum area rectangle occurs at [1,2],[2,1],[1,0],[0,1], with an area of 2.
```

**Example 2:**

![](https://assets.leetcode.com/uploads/2018/12/22/2.png)

```text
Input: [[0,1],[2,1],[1,1],[1,0],[2,0]]
Output: 1.00000
Explanation: The minimum area rectangle occurs at [1,0],[1,1],[2,1],[2,0], with an area of 1.
```

**Example 3:**

![](https://assets.leetcode.com/uploads/2018/12/22/3.png)

```text
Input: [[0,3],[1,2],[3,1],[1,3],[2,1]]
Output: 0
Explanation: There is no possible rectangle to form from these points.
```

**Example 4:**

![](https://assets.leetcode.com/uploads/2018/12/21/4c.png)

```text
Input: [[3,1],[1,1],[0,1],[2,1],[3,3],[3,2],[0,2],[2,3]]
Output: 2.00000
Explanation: The minimum area rectangle occurs at [2,1],[2,3],[3,3],[3,1], with an area of 2.
```

**Note:**

1. `1 <= points.length <= 50`
2. `0 <= points[i][0] <= 40000`
3. `0 <= points[i][1] <= 40000`
4. All points are distinct.
5. Answers within `10^-5` of the actual value will be accepted as correct.

### 分析

此题难度不大，确定4点能够组成矩形，再计算组成的矩形中的最小面积即可。固转化判断4点能不能组成矩形。 我的思路是，从数组中取3点，判断能不能组成直角三角形，并找到直角的顶点。已知矩形的3个顶点，第4点也就顶了 假设a,b,c是已知的3个顶点且角abc是直角，那点d\(dx,dy\) dx = ax + cx - bx,dy = ay +cy -by.

### 代码

```java
class Solution {
    public double minAreaFreeRect(int[][] points) {
        if(points == null || points.length < 4 || points[0].length != 2)
            return 0;
        int n = points.length;
        double res = Double.MAX_VALUE;
        for(int i = 0; i < n - 3; i++){
            for(int j = i+1; j < n-2;j++){
                for(int k = j+1; k < n-1;k++){
                    if(isRightAngle(points[j],points[i],points[k])){
                        res = Math.min(res,minAreaFreeRect(points,j,i,k,k+1));
                        continue;
                    }
                    if(isRightAngle(points[i],points[j],points[k])){
                        res = Math.min(res,minAreaFreeRect(points,i,j,k,k+1));
                        continue;
                    }
                    if(isRightAngle(points[i],points[k],points[j])){
                        res = Math.min(res,minAreaFreeRect(points,i,k,j,k+1));
                        continue;
                    }
                }
            }
        }
        return res == Double.MAX_VALUE ? 0 : res;
    }
    private double minAreaFreeRect(int[][] points,int a,int b,int c,int start){
        int n = points.length;
        double res = Double.MAX_VALUE;
        int x = points[a][0] + points[c][0] - points[b][0];
        int y = points[a][1] + points[c][1] - points[b][1];
        for(int i = start; i < n;i++){
            if(points[i][0] == x && points[i][1] == y){
                double ab = Math.sqrt((points[a][0] - points[b][0]) * (points[a][0] - points[b][0])
                        + (points[a][1] - points[b][1]) * (points[a][1] - points[b][1]));
                double bc = Math.sqrt((points[c][0] - points[b][0]) * (points[c][0] - points[b][0])
                        + (points[c][1] - points[b][1]) * (points[c][1] - points[b][1]));
                return ab * bc;
            }
        }
        return res;
    }
    private boolean isRightAngle(int[] a,int[] b, int[] c){
        return (a[0] - b[0]) * (b[0] - c[0]) + (a[1] - b[1]) * (b[1] - c[1]) == 0;
    }
}
```

## 1273 Delete Tree Node

给你一棵以节点 0 为根节点的树，定义如下： 

* 节点的总数为 nodes 个；
*  第 i 个节点的值为 value\[i\] ； 
* 第 i 个节点的父节点是 parent\[i\] 。 

请你删除节点值之和为 0 的每一棵子树。 在完成所有删除之后，返回树中剩余节点的数目。

A tree rooted at node 0 is given as follows:

* The number of nodes is `nodes`;
* The value of the `i`-th node is `value[i]`;
* The parent of the `i`-th node is `parent[i]`.

Remove every subtree whose sum of values of nodes is zero.

After doing so, return the number of nodes remaining in the tree.

**Example 1:** ![](https://assets.leetcode.com/uploads/2019/07/02/1421_sample_1.PNG)

```text
Input: nodes = 7, parent = [-1,0,0,1,2,2,2], value = [1,-2,4,0,-2,-1,-1]
Output: 2
```

**Example 2:**

```text
Input: nodes = 7, parent = [-1,0,0,1,2,2,2], value = [1,-2,4,0,-2,-1,-2]
Output: 6
```

**Example 3:**

```text
Input: nodes = 5, parent = [-1,0,1,0,0], value = [-672,441,18,728,378]
Output: 5
```

**Example 4:**

```text
Input: nodes = 5, parent = [-1,0,0,1,1], value = [-686,-842,616,-739,-746]
Output: 5
```

**Constraints:**

* `1 <= nodes <= 10^4`
* `parent.length == nodes`
* `0 <= parent[i] <= nodes - 1`
* `parent[0] == -1` which indicates that `0` is the root.
* `value.length == nodes`
* `-10^5 <= value[i] <= 10^5`
* The given input is **guaranteed** to represent a **valid tree**.

### 分析

![](../.gitbook/assets/image%20%28192%29.png)

### 代码

```java
class Solution {

    /**
     * @param nodes  节点的总数为 nodes 个
     * @param parent 第 i 个节点的值为 value[i]
     * @param value  第 i 个节点的父节点是 parent[i]
     * @return 删除节点值之和为 0 的每一棵子树。在完成所有删除之后，返回树中剩余节点的数目。
     */
    public int deleteTreeNodes(int nodes, int[] parent, int[] value) {
        TreeNode root = null;
        TreeNode[] treeArr = new TreeNode[nodes];

        // 创建节点对象
        for (int i = 0; i < nodes; i++) {
            treeArr[i] = new TreeNode(value[i]);
            if (parent[i] == -1) root = treeArr[i];
        }
        // 给每个节点构造子结点
        for (int i = 0; i < nodes; i++) {
            if (parent[i] != -1) {
                // 第 i 个节点的父节点是 parent[i]
                treeArr[parent[i]].addTreeNode(treeArr[i]);
            }
        }
        // 计算每个子树的节点数量和总和值
        computeTree(root);

        if (root.sum == 0) return 0;
        // return nodes - removeZeroTreeNode(treeArr);
        return nodes - removeZeroTreeNode(root);
    }

    // 递归计算该及其孩子节点的“节点总数”和“值的加和”
    public void computeTree(TreeNode node) {
        node.sum = node.value;
        node.num = 1;
        for (TreeNode child : node.children) {
            computeTree(child);
            node.sum += child.sum;
            node.num += child.num;
        }
    }

    // 计算该节点及其孩子节点会被移除的节点总数
    public int removeZeroTreeNode(TreeNode node) {
        int removeNum = 0;  // 统计子树和为 0 的节点个数
        // 因为已经有了节点数组，所以不用 BFS，直接数即可
        for (int i = 0; i < node.children.size(); i++) {
            TreeNode child = node.children.get(i);
            if (child.sum == 0) {
                removeNum += child.num;
            } else {
                removeNum += removeZeroTreeNode(child);
            }
        }
        return removeNum;
    }

    /**
     * 多叉树的结点类
     */
    class TreeNode {
        int value;  // 节点权值
        int sum;    // 子树和
        int num;    // 子树的节点数量
        List<TreeNode> children;    // 子结点

        public TreeNode() {
            this.children = new ArrayList<TreeNode>();
        }

        public TreeNode(int value) {
            this.value = value;
            this.children = new ArrayList<TreeNode>();
        }

        /**
         * 添加子结点
         *
         * @param treeNode
         */
        public void addTreeNode(TreeNode treeNode) {
            this.children.add(treeNode);
        }
    }
}

```

## 1612 Check If Two Expression Trees are Equivalent

二叉表达式树是一种表达算术表达式的二叉树。二叉表达式树中的每一个节点都有零个或两个子节点。 叶节点（有 0 个子节点的节点）表示操作数，非叶节点（有 2 个子节点的节点）表示运算符。在本题中，我们只考虑 '+' 运算符（即加法）。

给定两棵二叉表达式树的根节点 root1 和 root2 。如果两棵二叉表达式树等价，返回 true ，否则返回 false 。

当两棵二叉搜索树中的变量取任意值，分别求得的值都相等时，我们称这两棵二叉表达式树是等价的。

进阶：当你的答案需同时支持 '-' 运算符（减法）时，你该如何修改你的答案？

A [**binary expression tree**](https://en.wikipedia.org/wiki/Binary_expression_tree) is a kind of binary tree used to represent arithmetic expressions. Each node of a binary expression tree has either zero or two children. Leaf nodes \(nodes with 0 children\) correspond to operands \(variables\), and internal nodes \(nodes with two children\) correspond to the operators. In this problem, we only consider the `'+'` operator \(i.e. addition\).

You are given the roots of two binary expression trees, `root1` and `root2`. Return `true` _if the two binary expression trees are equivalent_. Otherwise, return `false`.

Two binary expression trees are equivalent if they **evaluate to the same value** regardless of what the variables are set to.

**Follow up:** What will you change in your solution if the tree also supports the `'-'` operator \(i.e. subtraction\)?

**Example 1:**

```text
Input: root1 = [x], root2 = [x]
Output: true
```

**Example 2:**

![](https://assets.leetcode.com/uploads/2020/10/04/tree1.png)

```text
Input: root1 = [+,a,+,null,null,b,c], root2 = [+,+,b,c,a]
Output: true
Explaination: a + (b + c) == (b + c) + a
```

**Example 3:**

![](https://assets.leetcode.com/uploads/2020/10/04/tree2.png)

```text
Input: root1 = [+,a,+,null,null,b,c], root2 = [+,+,b,d,a]
Output: false
Explaination: a + (b + c) != (b + d) + a
```

**Constraints:**

* The number of nodes in both trees are equal, odd and, in the range `[1, 4999]`.
* `Node.val` is `'+'` or a lower-case English letter.
* It's **guaranteed** that the tree given is a valid binary expression tree.

### 分析

* 由于表达树只有加法操作，且操作数都在叶节点上，表达树的计算简化为统计叶节点的字符变量；
* 两个表达树是否相等就是最后变量的数目是否一致。

### 代码

```java
/**
 * Definition for a binary tree node.
 * class Node {
 *     char val;
 *     Node left;
 *     Node right;
 *     Node() {this.val = ' ';}
 *     Node(char val) { this.val = val; }
 *     Node(char val, Node left, Node right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public boolean checkEquivalence(Node root1, Node root2) {
        int[] c1 = new int[26], c2 = new int[26];
        dfs(root1, c1);
        dfs(root2, c2);
        return Arrays.equals(c1, c2);
    }

    private void dfs(Node root, int[] counter) {
        if (root.left == null) counter[root.val - 'a']++;
        else {
            dfs(root.left, counter);
            dfs(root.right, counter);
        }
    }
}

```

## 360 Sort Tramsformed Array

有序转化数组，给你一个已经 排好序 的整数数组 nums 和整数 a、b、c。对于数组中的每一个数 x，计算函数值 f\(x\) = ax2 + bx + c，请将函数值产生的数组返回。

要注意，返回的这个数组必须按照 升序排列，并且我们所期望的解法时间复杂度为 O\(n\)。

Given a **sorted** array of integers nums and integer values a, b and c. Apply a quadratic function of the form f\(x\) = ax2 + bx + c to each element x in the array.

The returned array must be in **sorted order**.

Expected time complexity: **O\(n\)**

**Example 1:**

```text
Input: nums = [-4,-2,2,4], a = 1, b = 3, c = 5
Output: [3,9,15,33]
```

**Example 2:**

```text
Input: nums = [-4,-2,2,4], a = -1, b = 3, c = 5
Output: [-23,-5,1,7]
```

### 分析

The idea is simple: 

For a parabola,

* if a &gt;= 0, the min value is at its vertex. So our our sorting should goes from two end points towards the vertex, high to low. 
* if a &lt; 0, the max value is at its vertex. So our sort goes the opposite way.

### 代码

```java
public class Solution {
    public int[] sortTransformedArray(int[] nums, int a, int b, int c) {
        int[] res = new int[nums.length];
        int start = 0;
        int end = nums.length - 1;
        int i = a >= 0 ? nums.length - 1 : 0;
        while(start <= end) {
            int startNum = getNum(nums[start], a, b, c);
            int endNum = getNum(nums[end], a, b, c);
            if(a >= 0) {
                if(startNum >= endNum) {
                    res[i--] = startNum;
                    start++;
                }
                else{
                    res[i--] = endNum;
                    end--;
                }
            }
            else{
                if(startNum <= endNum) {
                    res[i++] = startNum;
                    start++;
                }
                else{
                    res[i++] = endNum;
                    end--;
                }
            }
        }
        return res;
    }
    public int getNum(int x, int a, int b, int c) {
        return a * x * x + b * x + c;
    }
}
```

## 1074 Number of Submatrices That Sum to Target



Given a `matrix` and a `target`, return the number of non-empty submatrices that sum to target.

A submatrix `x1, y1, x2, y2` is the set of all cells `matrix[x][y]` with `x1 <= x <= x2` and `y1 <= y <= y2`.

Two submatrices `(x1, y1, x2, y2)` and `(x1', y1', x2', y2')` are different if they have some coordinate that is different: for example, if `x1 != x1'`.

**Example 1:** ![](https://assets.leetcode.com/uploads/2020/09/02/mate1.jpg)

```text
Input: matrix = [[0,1,0],[1,1,1],[0,1,0]], target = 0
Output: 4
Explanation: The four 1x1 submatrices that only contain 0.
```

**Example 2:**

```text
Input: matrix = [[1,-1],[-1,1]], target = 0
Output: 5
Explanation: The two 1x2 submatrices, plus the two 2x1 submatrices, plus the 2x2 submatrix.
```

**Example 3:**

```text
Input: matrix = [[904]], target = 0
Output: 0
```

**Constraints:**

* `1 <= matrix.length <= 100`
* `1 <= matrix[0].length <= 100`
* `-1000 <= matrix[i] <= 1000`
* `-10^8 <= target <= 10^8`

### 分析

最简单的思路是O\(n^4\)时间复杂度的，以下算法基于该复杂度进行优化

Intuition

Preaquis: 560. Subarray Sum Equals K Find the Subarray with Target Sum in linear time.

Explanation

* For each row, calculate the prefix sum. 
* For each pair of columns, calculate the accumulated sum of rows. 

Now this problem is same to, "Find the Subarray with Target Sum".

Complexity：

Time O\(MN^2\) Space O\(N\)

1.设： sum\[x\]\[y\] = sum{matrix\[x\]\[0\], matrix\[x\]\[1\],...,natrix\[x\]\[y-1\]}; 

2.取： line\[x\] = sum\[x\]\[y2\] - sum\[x\]\[y1\]; 

3.那么x1,y1,x2,y2矩阵和为： sum{ line\[x1\],line\[x1+1\],...,line\[x2\] }; 

4.取： lineSum\[x\] = sum{line\[0\],line\[1\],...,line\[x-1\]}; 

5.最终，该问题转化为求： lineSum\[\] 中, lineSum\[x2\] - lineSum\[x1\] = target的个数（x2&gt;x1）;

注：其中y2对应到代码的endY, y1对应到代码的startY

### 代码

```java
class Solution {
    public int numSubmatrixSumTarget(int[][] A, int target) {
        int res = 0, m = A.length, n = A[0].length;
        for (int i = 0; i < m; i++)
            for (int j = 1; j < n; j++)
                A[i][j] += A[i][j - 1];
        Map<Integer, Integer> counter = new HashMap<>();
        for (int i = 0; i < n; i++) {
            for (int j = i; j < n; j++) {
                counter.clear();
                counter.put(0, 1);
                int cur = 0;
                for (int k = 0; k < m; k++) {
                    cur += A[k][j] - (i > 0 ? A[k][i - 1] : 0);
                    res += counter.getOrDefault(cur - target, 0);
                    counter.put(cur, counter.getOrDefault(cur, 0) + 1);
                }
            }
        }
        return res;
    }
}
```


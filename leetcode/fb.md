# FB

## 953 Verifying an Alien Dictionary

直接顺序对比

```java
class Solution {
    public boolean isAlienSorted(String[] words, String order) {
        if (words == null || words.length == 0) {
            return true;
        }
        String prev = words[0];
        for (int i = 1; i < words.length; i++) {
            // compare prev and curr
            if(!check(prev, words[i], order)) {
                return false;
            }
            prev = words[i];
        }
        return true;
    }
    private boolean check(String prev, String curr, String order) {
        int idx1 = 0, idx2 = 0;
        while (idx1 < prev.length() && idx2 < curr.length()) {
            int p1 = order.indexOf(prev.charAt(idx1));
            int p2 = order.indexOf(curr.charAt(idx2));
            if (p1 < p2) {
                return true;
            } else if (p1 > p2) {
                return false;
            } else { // same char
                idx1++;
                idx2++;
            }
            
        }
        if (idx1 < prev.length() && idx2 >= curr.length()) { // if prev is longer than curr
            return false;
        }
        return true;
    }
}
```

## 1249 Minimum Remove to Make Valid Parentheses

额外Stack和Set记录哪些字符串可以删掉

```java
class Solution {
    public String minRemoveToMakeValid(String s) {
        if (s.isEmpty()) {
            return "";
        }
        Set<Integer> indicesToRemove = new HashSet<>();
        Stack<Integer> stack = new Stack<>();
        
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (ch == '(') {
                stack.push(i);
            }
            if (ch == ')') {
                if (stack.isEmpty()) {
                    indicesToRemove.add(i);
                } else {
                    stack.pop();
                }
            }
        }
        
        while (!stack.isEmpty()) { // some '(' left inside the stack
            indicesToRemove.add(stack.pop());
        }
        
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < s.length(); i++) {
            if (!indicesToRemove.contains(i)) {
                sb.append(s.charAt(i));
            }
        }
        return sb.toString();
    }
}
```

## 1428 Leftmost Column with at Least a One

对所有行进行遍历，对每一行最左边出现的1的位置进行记录（找1的过程利用二分搜索），最后返回所有行中最左边1的列数。

```java
/**
 * // This is the BinaryMatrix's API interface.
 * // You should not implement it, or speculate about its implementation
 * interface BinaryMatrix {
 *     public int get(int row, int col) {}
 *     public List<Integer> dimensions {}
 * };
 */

class Solution {
    public int leftMostColumnWithOne(BinaryMatrix binaryMatrix) {
        int rows = binaryMatrix.dimensions().get(0);
        int cols = binaryMatrix.dimensions().get(1);
        System.out.println(rows);
        
        int smallestIndex = Integer.MAX_VALUE;
        
        for (int i = 0; i < rows; i++) {
            int left = 0, right = cols - 1;
            while (left + 1 < right) {
                int mid = left + (right - left) / 2; // mid element of one row
                if (binaryMatrix.get(i, mid) == 0) { // one the right
                    left = mid;
                } else { // itself or on the left
                    right = mid;
                }
            }
            if (binaryMatrix.get(i, left) == 1) {
                smallestIndex = Math.min(smallestIndex, left);
            }  else if (binaryMatrix.get(i, right) == 1) {
                smallestIndex = Math.min(smallestIndex, right);
            } 
        }
        return smallestIndex == Integer.MAX_VALUE ? -1 : smallestIndex;
    }
}
```

## 238 Product of Array Except Self

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/array\#238-product-of-array-except-self" %}

## 973 K Closest Points to Origin

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer-1\#973-k-closest-points-from-origin" %}

## 560 Subarray Sum Equals K

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zi-shu-zu-subarray\#560-subarray-sum-equals-k" %}

## 680 Valid Palindrome II

双指针

```java
class Solution {
    public boolean validPalindrome(String s) {
        if (s.isEmpty() || s.length() <= 2) {
            return true;
        }
        int len = s.length();
        int left = 0, right = len - 1;
        while (left < right && s.charAt(left) == s.charAt(right)) {
            left++;
            right--;
        }
        
        if (left == right) {
            return true;
        }
        
        // left++, right--
        if (checkPalindrome(s, left + 1, right) || checkPalindrome(s, left, right - 1)) {
            return true;
        }
        return false;
    }    
    private boolean checkPalindrome(String str, int left, int right) {
        while (left < right) {
            if (str.charAt(left) != str.charAt(right)) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
}
```

## 415 Add Strings

各一个指针

```java
class Solution {
    public String addStrings(String num1, String num2) {
        StringBuilder res = new StringBuilder();

        int carry = 0;
        int p1 = num1.length() - 1;
        int p2 = num2.length() - 1;
        while (p1 >= 0 || p2 >= 0) {
            int x1 = p1 >= 0 ? num1.charAt(p1) - '0' : 0;
            int x2 = p2 >= 0 ? num2.charAt(p2) - '0' : 0;
            int value = (x1 + x2 + carry) % 10;
            carry = (x1 + x2 + carry) / 10;
            res.append(value);
            p1--;
            p2--;    
        }
        
        if (carry != 0)
            res.append(carry);
        
        return res.reverse().toString();
    }
}
```

## 273 Integer to English

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#273-integer-to-english-words" %}

## 269 Alien Dictionary

最好拓扑排序的解法

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/graph\#269-alien-dictionary" %}

## 199 Binary Tree Right Side View

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-3\#199-binary-tree-right-side-view" %}

## 158 Read N Characters Given Read4 II - Call multiple times

```java
/**
 * The read4 API is defined in the parent class Reader4.
 *     int read4(char[] buf4); 
 */

public class Solution extends Reader4 {
    /**
     * @param buf Destination buffer
     * @param n   Number of characters to read
     * @return    The number of actual characters read
     */
    char[] bufCache = new char[4];
    int bufPtr = 0;
    int bufCount = 0;

    public int read(char[] buf, int n) {
        int nIndex = 0;

        // fill out every position until reach n
        while (nIndex < n) {
            // only if bufPtr does not reach the end of bufCache array, we can assign value from bufCache to final buf array
            if (bufPtr < bufCount) {
                buf[nIndex++] = bufCache[bufPtr++];
            }

            // if we already used all characters from bufCache, we need to read new characters by calling read4()
            // and then fill the bufCache
            else {
                bufCount = read4(bufCache);
                bufPtr = 0;

                // if no more characters we can read, we should break the entire loop and return 0
                if (bufCount == 0) {
                    break;
                }
            }
        }
        return nIndex;
    } 
}
```

## 301 Remove Invalid Parenthesis

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-3\#301-remove-invalid-parentheses" %}

## 1570 Dot Product of Two Sparse Vectors

```java
class SparseVector {
    
    Map<Integer, Integer> map;      // For all non-zero values in the vector, we map the index to the non-zero value.
    
    // O(nums.length) time.
    // O(numNonZeroValues) space.
    SparseVector(int[] nums) {
        map = new HashMap<>();
        // Store the position and value of non-zero values.
        for (int i = 0; i < nums.length; ++i) {
            if (nums[i] != 0) {
                map.put(i, nums[i]);        
            }
        }
    }
    
	// Return the dotProduct of two sparse vectors
    // O(min(vec1numNonZeroValues, vec2numNonZeroValues)) time because we iterate through non-zero values of the vector that has fewer non-zero values and for each value we check in O(1) time whether the other vector has a non-zero value at that index.
    // O(1) space.
    public int dotProduct(SparseVector vec) {        
        if (vec.map.size() < this.map.size()) return vec.dotProduct(this);      // We want to iterate through the smaller map.

        int result = 0;
        for (Integer currIdx : this.map.keySet()) {
            // If both vectors have a non-zero value at currIdx then multiply the values and add them to the result.
            if (vec.map.containsKey(currIdx)) {
                result += this.map.get(currIdx) * vec.map.get(currIdx);
            }
        }
        return result;
    }
}

// Your SparseVector object will be instantiated and called as such:
// SparseVector v1 = new SparseVector(nums1);
// SparseVector v2 = new SparseVector(nums2);
// int ans = v1.dotProduct(v2);
```

## 211 Design Add and Search Words Data Structure

## 215 Kth Largest Element in an Array

{% embed url="https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer-1\#215-kth-largest-element-in-an-array" %}

#### 721    Accounts Merge

[根据邮件和用户名合并账户 - DFS](https://leetcode-cn.com/problems/accounts-merge/solution/zhang-hu-he-bing-by-leetcode/)

#### 125    Valid Palindrome

[双指针](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-2#125-valid-palindrome)

#### 124    Binary Tree Maximum Path Sum

[任意起点的路径最大值 - 回溯](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#124-binary-tree-maximum-path-sum)

#### 278    First Bad Version

[Binary Search](https://app.gitbook.com/@guilindev/s/interview/leetcode/er-fen-sou-suo#278-first-bad-version)

#### 88    Merge Sorted Array

[从后向前](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/he-bing-you-xu-de-arrays#88-merge-sorted-array)

#### 29    Divide Two Integers

[不用乘除和取余的除法 - 位操作](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled#29-divide-two-integers)

#### 426    Convert Binary Search Tree to Sorted Doubly Linked List

[BST转换为有序链表 - 中序遍历+备忘录](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#426-convert-binary-search-tree-to-sorted-doubly-linked-list)

#### 297    Serialize and Deserialize Binary Tree

[先序或层序](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#297-serialize-and-deserialize-binary-tree)

#### 438    Find All Anagrams in a String

[一个字符串中找到所有异位词子串的起始位置 - 滑动窗口 + HashMap/Array](https://leetcode.com/problems/find-all-anagrams-in-a-string/solution/)

#### 636    Exclusive Time of Functions

[抢占CPU资源的函数独占时间，关键是单线程非抢占cpu，就是说父函数调用子函数，子函数结束之后、父函数才有可能结束。不会出现父函数结束了，子函数继续运行的情况 - 栈](https://leetcode-cn.com/problems/exclusive-time-of-functions/solution/han-shu-de-du-zhan-shi-jian-by-leetcode/)

#### 523    Continuous Subarray Sum

[找一个子数组，子数组的和是给定k的n倍 - 存前缀累积和](https://leetcode-cn.com/problems/continuous-subarray-sum/solution/lian-xu-de-zi-shu-zu-he-by-leetcode/)

#### 282    Expression Add Operators

[给一个字符串形式的数，组合加减乘得到给定值的所有情况 - 回溯](https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking#282-expression-add-operators)

#### 173    Binary Search Tree Iterator

[BST返回和检查下一个最小数 - Stack](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#173-binary-search-tree-iterator)

#### 34    Find First and Last Position of Element in Sorted Array

[二分找左右边界](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zai-array-zhong-cha-xun-yuan-su#34-find-first-and-last-position-of-element-in-sorted-array-search-for-a-range)

#### 528    Random Pick with Weight

[前缀和+二分](https://app.gitbook.com/@guilindev/s/interview/leetcode/tag0#528-random-pick-with-weight)

#### 987    Vertical Order Traversal of a Binary Tree

这道题跟314的不同之处在于，这道题要计算左右子树的坐标方式，314只管BFS打印出来。

[垂序遍历 DFS记录坐标+排序](https://leetcode-cn.com/problems/vertical-order-traversal-of-a-binary-tree/solution/er-cha-shu-de-chui-xu-bian-li-by-leetcode-2/)

#### 50    Pow\(x, n\)

[递归折半](https://app.gitbook.com/@guilindev/s/interview/leetcode/er-fen-sou-suo#50-power-x-n)

#### 65    Valid Number

[检查字符串是否是数](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#65-valid-number)

#### 986    Interval List Intersections

[两两合并两个数组中的intervals - 双指针](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-2#986-interval-list-intersections)

#### 133    Clone Graph

[BFS](https://app.gitbook.com/@guilindev/s/interview/leetcode/graph#133-clone-graph)

#### 31    Next Permutation

[记住特殊的解法](https://app.gitbook.com/@guilindev/s/interview/leetcode/array#31-next-permutation)

#### 708    Insert into a Sorted Circular Linked List $

[插入元素到循环有序链表中](https://leetcode.com/problems/insert-into-a-sorted-circular-linked-list/) [one pass or two passes](https://leetcode.com/problems/insert-into-a-sorted-circular-linked-list/discuss/149374/Java-5ms-One-Pass-and-Two-Pass-Traverse-With-Detailed-Comments-and-Edge-cases!)

```java
class Solution {
    public Node insert(Node start, int x) {
        // if start is null, create a node pointing to itself and return
        if (start == null) {
            Node node = new Node(x, null);
            node.next = node;
            return node;
        }
        // if start is not null, try to insert it into correct position
        // 1st pass to find max node
        Node cur = start;
        while (cur.val <= cur.next.val && cur.next != start) 
            cur = cur.next;
        // 2nd pass to insert the node in to correct position
        Node max = cur;
        Node dummy = new Node(0, max.next); // use a dummy head to make insertion process simpler
        max.next = null; // break the cycle
        cur = dummy;
        while (cur.next != null && cur.next.val < x) {
            cur = cur.next;
        }
        cur.next = new Node(x, cur.next); // insert
        Node newMax = max.next == null ? max : max.next; // reconnect to cycle
        newMax.next = dummy.next;
        return start;
    }
}
```

#### 249    Group Shifted Strings $

[找出可以往后shift的字符串的分组](https://leetcode.com/problems/group-shifted-strings/)

Basically we need to form some sort of key for each word to group them. \(Remember the idea of group all anagrams?\)

Consider acf and pru. Now notice the differnce between each characters. acf = 0-&gt;2-&gt;3, and pru = 0-&gt;2-&gt;3. So these two form same group. So in this case, you can simply use integers ASCII difference to form key.

Now consider corner case, az and ba, where az = 0-&gt;25 and ba = 0-&gt;-1. When it goes negative, just make it positive\(rotate or mod equivalent\) by adding 26. So it becomes, ba = 0-&gt;25, which forms same group.

```java
class Solution {
    public List<List<String>> groupStrings(String[] strings) {
    Map<String, List<String>> map = new HashMap<>();

    for(String s : strings) {
        String key = getKey(s);
        List<String> list = map.getOrDefault(key, new ArrayList<>());
        list.add(s);
        map.put(key, list);
    }
    return new ArrayList<>(map.values());
}

private String getKey(String s) {
    char[] chars = s.toCharArray();
    String key = "";
    for(int i = 1; i < chars.length; i++) {
        int diff = chars[i] - chars[i-1];
        key += diff < 0 ? diff + 26 : diff;
        key += ",";
    }
    return key;
}
}
```

#### 42    Trapping Rain Water

[DP/stack](https://app.gitbook.com/@guilindev/s/interview/leetcode/stack#42-trapping-rain-water)

#### 340    Longest Substring with At Most K Distinct Characters $

[最多包含k个不同字符的子字符串的长度 - HashMap记录滑动窗口](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/)

```java
class Solution {
    public int lengthOfLongestSubstringKDistinct(String s, int k) {
    Map<Character, Integer> map = new HashMap<>();
    int left = 0; // 记录窗口的最左边字符的index
    int best = 0;
    for(int i = 0; i < s.length(); i++) {
        char c = s.charAt(i);
        map.put(c, map.getOrDefault(c, 0) + 1);
        while (map.size() > k) { // 检查hashmap的size
            char leftChar = s.charAt(left);
            if (map.containsKey(leftChar)) {
                map.put(leftChar, map.get(leftChar) - 1);                     
                if (map.get(leftChar) == 0) { 
                    map.remove(leftChar);
                }
            }
            left++;
        }
        best = Math.max(best, i - left + 1);
    }
    return best;
} 
}
```

#### 543    Diameter of Binary Tree

[一个结点到另一个结点最长距离 - DFS](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#543-diameter-of-binary-tree)

#### 398    Random Pick Index

[有重复元素的随机数返回索引 - 蓄水池抽样问题](https://leetcode-cn.com/problems/random-pick-index/solution/xu-shui-chi-chou-yang-wen-ti-by-an-xin-9/)

#### 139    Word Break

[DP + 剪枝/Trie](https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer#139-word-break)

#### 339    Nested List Weight Sum

[嵌套数组深度为权重求和 - DFS](https://app.gitbook.com/@guilindev/s/interview/leetcode/dfs#339-nested-list-weight-sum)

#### 785    Is Graph Bipartite?

Recall that a graph is bipartite if we can split its set of nodes into two independent subsets A and B, such that every edge in the graph has one node in A and another node in B. 若无向图 G=\(V,E\) 的顶点集 V 可以分割为两个互不相交的子集，且图中每条边的两个顶点分别属于不同的子集，则称图 G 为一个二分图。

[判断是否二分图 DFS/BFS/UF](https://leetcode-cn.com/problems/is-graph-bipartite/solution/bfs-dfs-bing-cha-ji-san-chong-fang-fa-pan-duan-er-/)

#### 270    Closest Binary Search Tree Value $

[Top Down给定的值可能为double](https://leetcode.com/problems/closest-binary-search-tree-value/)

```java
class Solution {
    public int closestValue(TreeNode root, double target) {
        int ret = root.val;
        while (root != null) {
            if (Math.abs(target - root.val) < Math.abs(target - ret)) {
                ret = root.val;
                if (Math.abs(target - root.val) <= 0.5) //BST且没重复，不会有比这更小的值
                    break;
            }
            root = root.val > target ? root.left : root.right;
        }
        return ret;
    } 
}
```

#### 378    Kth Smallest Element in a Sorted Matrix

[二维矩阵中第k小的值 - 二分法](https://app.gitbook.com/@guilindev/s/interview/leetcode/er-fen-sou-suo#378-kth-smallest-element-in-a-sorted-matrix)

#### 670    Maximum Swap

[非负整数中交换一次两位得到最大 - 存索引](https://leetcode-cn.com/problems/maximum-swap/solution/zui-da-jiao-huan-by-leetcode/)

#### 76    Minimum Window Substring

[最短substring中包含给定字符串中的所有字符 - 两个hashmap双指针](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-2#76-minimum-window-substring)

#### 317    Shortest Distance from All Buildings

[周围楼间最短距离盖楼 BFS](https://app.gitbook.com/@guilindev/s/interview/leetcode/tag5#317-shortest-distance-from-all-buildings)

#### 536    Construct Binary Tree from String $

[从一个数字（根）和小括号（层数）的字符串生成二叉树 DFS或Stack](https://leetcode.com/problems/construct-binary-tree-from-string/)

```java
class Solution {
    public TreeNode str2tree(String s) {
        Stack<TreeNode> stack = new Stack<>();
        for(int i = 0, j = i; i < s.length(); i++, j = i){
            char c = s.charAt(i);
            if(c == ')')    stack.pop();
            else if(c >= '0' && c <= '9' || c == '-'){
                while(i + 1 < s.length() && s.charAt(i + 1) >= '0' && s.charAt(i + 1) <= '9') i++;
                TreeNode currentNode = new TreeNode(Integer.valueOf(s.substring(j, i + 1)));
                if(!stack.isEmpty()){
                    TreeNode parent = stack.peek();
                    if(parent.left != null)    parent.right = currentNode;
                    else parent.left = currentNode;
                }
                stack.push(currentNode);
            }
        }
        return stack.isEmpty() ? null : stack.peek();
    }
}
```

OP's solution is on average O\(nlgn\); worse case O\(n^2\). This could be solved in O\(n\). Here's my recursive one:

```java
class Solution {
    int p;

    public TreeNode str2tree(String s) {
        if (s == null || s.isEmpty()) return null;
        p = 0;
        s = "(" + s + ")";
        return build(s);
    }

    private TreeNode build(String s) {
        int start = p + 1;
        int end = start + 1;
        while (end < s.length() && Character.isDigit(s.charAt(end))) end++;
        int val = Integer.valueOf(s.substring(start, end));
        TreeNode root = new TreeNode(val);
        p = end;
        if (s.charAt(p) == '(') {
            root.left = build(s);
            if (s.charAt(p) == '(') {
                root.right = build(s);
            }
        }
        p++;
        return root;
    }
}
```

#### 896    Monotonic Array

[判断数组是单调递增或递减 - 可以一次遍历](https://leetcode-cn.com/problems/monotonic-array/solution/dan-diao-shu-lie-by-leetcode/)

#### 621    Task Scheduler

[两个任务间至少间隔n个点 - 贪心/PQ](https://app.gitbook.com/@guilindev/s/interview/leetcode/queue#621-task-scheduler)

#### 1060 Missing Element in Sorted Array $

[找出有序数组中从1开始第Kth缺失的元素 - 二分](https://leetcode.com/problems/missing-element-in-sorted-array/solution/)

```java
class Solution {
  // Return how many numbers are missing until nums[idx]
  int missing(int idx, int[] nums) {
    return nums[idx] - nums[0] - idx;
  }

  public int missingElement(int[] nums, int k) {
    int n = nums.length;
    // If kth missing number is larger than 
    // the last element of the array
    if (k > missing(n - 1, nums))
      return nums[n - 1] + k - missing(n - 1, nums);

    int left = 0, right = n - 1, pivot;
    // find left = right index such that 
    // missing(left - 1) < k <= missing(left)
    while (left != right) {
      pivot = left + (right - left) / 2;

      if (missing(pivot, nums) < k) left = pivot + 1;
      else right = pivot;
    }

    // kth missing number is greater than nums[idx - 1]
    // and less than nums[idx]
    return nums[left - 1] + k - missing(left - 1, nums);
  }
}
```

#### 825    Friends Of Appropriate Ages

[适龄朋友交友](https://leetcode-cn.com/problems/friends-of-appropriate-ages/solution/gua-ling-de-peng-you-by-leetcode/)

#### 236    Lowest Common Ancestor of a Binary Tree

[top down](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#236-lowest-common-ancestor-of-binary-tree)

#### 311    Sparse Matrix Multiplication $

[两个稀疏矩阵的乘法](https://leetcode.com/problems/sparse-matrix-multiplication/discuss/76154/Easiest-JAVA-solution)

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

#### 140    Word Break II

[列举所有单词可拆分的情况](https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer#140-word-break-ii)

#### 56    Merge Intervals

[排序/PQ](https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou#56-merge-intervals)

#### 938    Range Sum of BST

[BST的low, high之间的范围和](https://leetcode-cn.com/problems/range-sum-of-bst/solution/er-cha-sou-suo-shu-de-fan-wei-he-by-leetcode/)

#### 616    Add Bold Tag in String $

[Dict包含的子串分别加入标识 - 用一个01二叉树组标记位置是不是子串](https://leetcode.com/problems/add-bold-tag-in-string/discuss/104248/Java-Solution-boolean-array)

```java
class Solution {
    public String addBoldTag(String s, String[] dict) {
        boolean[] bold = new boolean[s.length()];
        for (int i = 0, end = 0; i < s.length(); i++) {
            for (String word : dict) {
                if (s.startsWith(word, i)) {
                    end = Math.max(end, i + word.length());
                }
            }
            bold[i] = end > i;
        }

        StringBuilder result = new StringBuilder();
        for (int i = 0; i < s.length(); i++) {
            if (!bold[i]) {
                result.append(s.charAt(i));
                continue;
            }
            int j = i;
            while (j < s.length() && bold[j]) j++;
            result.append("<b>" + s.substring(i, j) + "</b>");
            i = j - 1;
        }

        return result.toString();
    }
}
```

#### 827    Making A Large Island

[最多将1格海填充成陆地求得最大岛屿 - UF/DFS标记/BFS标记](https://leetcode-cn.com/problems/making-a-large-island/solution/dao-yu-wen-ti-zhi-zui-da-ren-gong-dao-danaus-genut/)

#### 23    Merge k Sorted Lists

[Merge Sort](https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist#23-merge-k-sorted-lists)

#### 138    Copy List with Random Pointer

[in place](https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist#138-copy-list-with-random-pointer)

#### 314    Binary Tree Vertical Order Traversal $

[树形结构的从左向右Traversal - BFS](https://leetcode.com/problems/binary-tree-vertical-order-traversal/discuss/76401/5ms-Java-Clean-Solution)

* BFS, put node, col into queue at the same time
* Every left child access col - 1 while right child col + 1
* This maps node into different col buckets
* Get col boundary min and max on the fly
* Retrieve result from cols

  ```java
  class Solution {
    public List<List<Integer>> verticalOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) {
            return res;
        }

        Map<Integer, ArrayList<Integer>> map = new HashMap<>();
        Queue<TreeNode> q = new LinkedList<>(); // 存nodes
        Queue<Integer> cols = new LinkedList<>(); //存列数信息

        q.add(root); 
        cols.add(0);

        int min = 0;
        int max = 0;

        while (!q.isEmpty()) {
            TreeNode node = q.poll();
            int col = cols.poll();

            if (!map.containsKey(col)) {
                map.put(col, new ArrayList<Integer>());
            }
            map.get(col).add(node.val);

            if (node.left != null) {
                q.add(node.left); 
                cols.add(col - 1);
                min = Math.min(min, col - 1);
            }

            if (node.right != null) {
                q.add(node.right);
                cols.add(col + 1);
                max = Math.max(max, col + 1);
            }
        }

        for (int i = min; i <= max; i++) {
            res.add(map.get(i));
        }

        return res;
    }
  }
  ```

#### 689    Maximum Sum of 3 Non-Overlapping Subarrays

[找出一个整数数组中，三个不重叠子数组的起点index集合，保证三个子数组相加的和最大 - DP](https://leetcode-cn.com/problems/maximum-sum-of-3-non-overlapping-subarrays/solution/san-ge-wu-zhong-die-zi-shu-zu-de-zui-da-he-by-leet/)

```java
class Solution {
        /**
         * cache[i] = sum(nums[i],...,nums[i+k])
         * int max_idx(int[] arr, int i, int j) -> 数组arr在[i,j]范围内的最大值的第一次出现的位置
         * left[i] = max_idx(cache, 0, i)
         * right[i] = max_idx(cache, i, cache.lenght - 1)
         *
         * @param nums
         * @param k
         * @return
         */
        public int[] maxSumOfThreeSubarrays(int[] nums, int k) {
            int[] cache = new int[nums.length - k + 1];
            int[] left = new int[nums.length - k + 1];
            int[] right = new int[nums.length - k + 1];
            int sum = 0;
            for (int i = 0; i < k; i++) {
                sum += nums[i];
            }
            cache[0] = sum;
            for (int i = 1; i < cache.length; i++) {
                int v = cache[i] = sum = sum + nums[i + k - 1] - nums[i - 1];
                if (v > cache[left[i - 1]]) {
                    left[i] = i;
                } else {
                    left[i] = left[i - 1];
                }
            }
            right[right.length - 1] = right.length - 1;
            for (int i = cache.length - 2; i >= 0; i--) {
                if (cache[i] >= cache[right[i + 1]]) {
                    right[i] = i;
                } else {
                    right[i] = right[i + 1];
                }
            }
            int interval = k << 1;
            int[] res = new int[]{0, k, interval};
            int max = Integer.MIN_VALUE;
            int max_m = cache.length - k;
            for (int m = k; m < max_m; m++) {
                int v = cache[left[m - k]] + cache[m] + cache[right[m + k]];
                if (v > max) {
                    max = v;
                    res[0] = left[m - k];
                    res[1] = m;
                    res[2] = right[m + k];
                }
            }
            return res;
        }
    }
```

#### 347    Top K Frequent Elements

[小顶堆 + HashMap](https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table#347-top-k-frequent-elements)

#### 146    LRU Cache

[背诵题](https://app.gitbook.com/@guilindev/s/interview/leetcode/gao-ji-de-shu-ju-jie-gou-he-suan-fa#146-lru-cache)

#### 10    Regular Expression Matching

[星号\*匹配前一个字符](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#10-regular-expression-matching)

#### 766    Toeplitz Matrix

[判断托普利茨矩阵（有一条斜线元素相等）- 检查左上邻居](https://leetcode-cn.com/problems/toeplitz-matrix/solution/tuo-pu-li-ci-ju-zhen-by-leetcode/)

#### 1197 Minimum Knight Moves

[BFS](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-3#1197-minimum-knight-moves)

#### 163    Missing Ranges $

[有序数组给出上下边界，打印出所有缺失的区间](https://leetcode.com/problems/missing-ranges/)

* Find the range between lower and first element in the array.
* Find ranges between adjacent elements in the array.
* Find the range between upper and last element in the array.

  ```java
  class Solution {
    public List<String> findMissingRanges(int[] nums, int lower, int upper) {
        List<String> result = new ArrayList<>();
        if (nums == null || nums.length == 0){
            result.add(formRange(lower,upper));
            return result;
        }

        // 1st step
        if (nums[0] > lower){
            result.add(formRange(lower,nums[0]-1));
        }

        // 2nd step
        for (int i = 0; i < nums.length-1; i++){
            if (nums[i+1] != nums[i] && nums[i+1] > nums[i] +1) {
                result.add(formRange(nums[i]+1, nums[i+1]-1));
            }
        }

       // 3rd step
        if (nums[nums.length-1] < upper){
            result.add(formRange(nums[nums.length-1]+1, upper));
        }
        return result;
    }

    public String formRange(int low, int high){
        return low == high ? String.valueOf(low) : (low + "->" + high);
    }
  }
  ```

#### 71    Simplify Path

[把Unix风格路径规范和简化 - 顺序栈](https://leetcode-cn.com/problems/simplify-path/solution/2020040371medianzhan-zi-fu-shu-zu-ke-bian-zi-fu-ch/)

#### 1498 Number of Subsequences That Satisfy the Given Sum Condition

[子数组中的最小值和最大值相加的和，小于等于目标值的子数组总数（大数据集）- 排序后二分](https://leetcode-cn.com/problems/number-of-subsequences-that-satisfy-the-given-sum-condition/solution/java-er-fen-sou-suo-by-aliengod/)

#### 43    Multiply Strings

[字符串相乘保存很大的数 - 每位相乘后错位相加（cross addition）](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#43-multiply-strings)

#### 1004 Max Consecutive Ones III

[01数组中可以将k个0变成1，求最多连续的1 - 理解成滑动窗口最多有k个0](https://leetcode-cn.com/problems/max-consecutive-ones-iii/solution/javahua-dong-chuang-kou-by-coldpepsi/)

#### 200    Number of Islands

[三种方法](https://app.gitbook.com/@guilindev/s/interview/leetcode/dfs#200-number-of-islands)

#### 304    Range Sum Query 2D - Immutable

[二维矩阵的区域和搜索 - DP缓存](https://leetcode-cn.com/problems/range-sum-query-2d-immutable/solution/er-wei-qu-yu-he-jian-suo-ju-zhen-bu-ke-bian-by-lee/)

#### 333    Largest BST Subtree $

[找出有最多结点的子BST](https://leetcode.com/problems/largest-bst-subtree/)

```java
class Solution {
    // O(n)
    // return array for each node: 
    //     [0] --> min
    //     [1] --> max
    //     [2] --> largest BST in its subtree(inclusive)

    public int largestBSTSubtree(TreeNode root) {
        int[] ret = largestBST(root);
        return ret[2];
    }

    private int[] largestBST(TreeNode node){
        if(node == null){
            return new int[]{Integer.MAX_VALUE, Integer.MIN_VALUE, 0};
        }
        int[] left = largestBST(node.left);
        int[] right = largestBST(node.right);
        if(node.val > left[1] && node.val < right[0]){
            return new int[]{Math.min(node.val, left[0]), Math.max(node.val, right[1]), left[2] + right[2] + 1};
        }else{
            return new int[]{Integer.MIN_VALUE, Integer.MAX_VALUE, Math.max(left[2], right[2])};
        }
    }
}
```

#### 247    Strobogrammatic Number II

[找出所有180度旋转后的中心对称数，长度为n](https://app.gitbook.com/@guilindev/s/interview/leetcode/tag5#247-strobogrammatic-number-ii)

#### 1047 Remove All Adjacent Duplicates In String

[删除字符串中所有的相邻并且重复的字符（反复执行直到最后无法删除） - 栈](https://leetcode-cn.com/problems/remove-all-adjacent-duplicates-in-string/solution/shan-chu-zi-fu-chuan-zhong-de-suo-you-xiang-lin-zh/)

#### 203    Remove Linked List Elements

[将等于目标值的元素从链表中删除](https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist#203-remove-linked-list-elements)

#### 253    Meeting Rooms II

[共需要多少个会议室](https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou#253-meeting-rooms-ii)

#### 525    Contiguous Array

[二分数组中找到所有包含相同数量0和1的子数组的数目 - HashMap](https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table#525-contiguous-array)

#### 1424 Diagonal Traverse II

[二维整数列表的左下到右上对角线遍历 - Treemap](https://leetcode-cn.com/problems/diagonal-traverse-ii/solution/treemapan-dui-jiao-xian-ju-he-zhi-by-zuo-zhou-ren/)

#### 266    Palindrome Permutation $

[字符串可以打散，判断是否回文 - 计数/hashset](https://leetcode.com/problems/palindrome-permutation/)

可以统计数目，也可以更简单 - iterate over string, adding current character to set if set doesn't contain that character, or removing current character from set if set contains it. When the iteration is finished, just return set.size\(\)==0 \|\| set.size\(\)==1.

set.size\(\)==0 corresponds to the situation when there are even number of any character in the string, and set.size\(\)==1 corresponsds to the fact that there are even number of any character except one.

```java
class Solution {
    public boolean canPermutePalindrome(String s) {
        Set<Character> set=new HashSet<Character>();
        for(int i=0; i<s.length(); ++i){
            if (!set.contains(s.charAt(i)))
                set.add(s.charAt(i));
            else 
                set.remove(s.charAt(i));
        }
        return set.size()==0 || set.size()==1;
    }
}
```

#### 78    Subsets

[背诵题](https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking#78-subsets)

#### 109    Convert Sorted List to Binary Search Tree

[排好序的链表转换成BST - 快慢指针找中点](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#109-convert-sorted-list-to-binary-search-tree)

#### 1233 Remove Sub-Folders from the Filesystem

[删除给定数组中的所有子文件夹 - 排序去重](https://leetcode-cn.com/problems/remove-sub-folders-from-the-filesystem/solution/java-pai-xu-zhi-hou-mei-ge-jin-xing-pan-duan-bu-cu/)

#### 977    Squares of a Sorted Array

[非递减数组的平方形成新数组，有负数 - 双指针](https://leetcode-cn.com/problems/squares-of-a-sorted-array/solution/you-xu-shu-zu-de-ping-fang-by-leetcode-solution/)

#### 227    Basic Calculator II

[有加减乘除没有括号](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#227-basic-calculator-ii)

#### 381    Insert Delete GetRandom O\(1\) - Duplicates allowed

[设计数据结构O\(1\) 时间插入、删除和获取随机元素 - 允许重复](https://leetcode-cn.com/problems/insert-delete-getrandom-o1-duplicates-allowed/solution/o1-shi-jian-cha-ru-shan-chu-he-huo-qu-sui-ji-yua-5/)

#### 958    Check Completeness of a Binary Tree

[检查二叉树是否是完全二叉树 - BFS](https://leetcode-cn.com/problems/check-completeness-of-a-binary-tree/solution/java-ceng-xu-bian-li-by-zxy0917/)

#### 114    Flatten Binary Tree to Linked List

[先序](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#114-flatten-binary-tree-to-linked-list)

#### 489    Robot Room Cleaner

[489+回溯](https://app.gitbook.com/@guilindev/s/interview/leetcode/tag0#489-robot-room-cleaner)

#### 246    Strobogrammatic Number $

[判断是否是中心对称数](https://leetcode.com/problems/strobogrammatic-number/)

```java
class Solution {
    public boolean isStrobogrammatic(String num) {
        Map<Character, Character> map = new HashMap<Character, Character>();
        map.put('6', '9');
        map.put('9', '6');
        map.put('0', '0');
        map.put('1', '1');
        map.put('8', '8');

        int l = 0, r = num.length() - 1;
        while (l <= r) {
            if (!map.containsKey(num.charAt(l))) return false;
            if (map.get(num.charAt(l)) != num.charAt(r))
                return false;
            l++;
            r--;
        }

        return true;
    }
}
```

#### 8 String to Integer \(atoi\)

[各种边界条件](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#8-string-to-integer-atoi)

#### 157    Read N Characters Given Read4 $

[Read4读取N个字符，读一次](https://leetcode.com/problems/read-n-characters-given-read4/)

```java
class Solution {
    public int read(char[] buf, int n) {
        boolean eof = false;      // end of file flag
        int total = 0;            // total bytes have read
        char[] tmp = new char[4]; // temp buffer

        while (!eof && total < n) {
            int count = read4(tmp);

            // check if it's the end of the file
            eof = count < 4;

            // get the actual count
            count = Math.min(count, n - total);

            // copy from temp buffer to buf
            for (int i = 0; i < count; i++) 
            buf[total++] = tmp[i];
        }

        return total;
    }
}
```

#### 695    Max Area of Island

[二维数组中找最大的岛 - DFS/BFS/UF](https://app.gitbook.com/@guilindev/s/interview/leetcode/dfs#695-max-area-of-islands)

#### 934    Shortest Bridge

[二分二维数组中，将0变成1形成最短的桥，需要转换0的最小次数 - BFS](https://leetcode-cn.com/problems/shortest-bridge/solution/zui-duan-de-qiao-by-leetcode/)

#### 129    Sum Root to Leaf Numbers

[root到leaf各条路劲形成的数的和](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#129-sum-root-to-leaf-numbers)

#### 752    Open the Lock

[圆形转盘锁解锁 - BFS](https://app.gitbook.com/@guilindev/s/interview/leetcode/tag1#752-open-the-lock)

#### 1539 Kth Missing Positive Number

[严格升序数组中从1开始找出第k个缺失的正整数 - 二分](https://leetcode-cn.com/problems/kth-missing-positive-number/solution/di-k-ge-que-shi-de-zheng-zheng-shu-by-leetcode-sol/)

#### 647    Palindromic Substrings

[所有回文子串 - 直接计算或DP 复杂度一样](https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer#647-palindromic-substrings)

#### 463    Island Perimeter

[注意是否有湖的情况](https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table#463-island-perimeter)

#### 863    All Nodes Distance K in Binary Tree

[Hashmap添加父节点引用/BFS当作图处理](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#863-all-nodes-distance-k-in-binary-tree)

#### 33 Search in Rotated Sorted Array

[二分](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zai-array-zhong-cha-xun-yuan-su#33-search-in-rotated-sorted-array)

#### 921    Minimum Add to Make Parentheses Valid

[使括号字符串有效的最小添加数 - 计数平衡](https://leetcode-cn.com/problems/minimum-add-to-make-parentheses-valid/solution/shi-gua-hao-you-xiao-de-zui-shao-tian-jia-by-leetc/)

#### 1242 Web Crawler Multithreaded $

[根据URL的pattern设计多线程网络爬虫可以爬相同的主机名](https://leetcode.com/problems/web-crawler-multithreaded/)

```java
class Solution {
    public List<String> crawl(String startUrl, HtmlParser htmlParser) {
        String hostname = getHostname(startUrl);

        Set<String> visited = ConcurrentHashMap.newKeySet();
        visited.add(startUrl);

        return crawl(startUrl, htmlParser, hostname, visited)
            .collect(Collectors.toList());
    }

    private Stream<String> crawl(String startUrl, HtmlParser htmlParser, String hostname, Set<String> visited) {
        Stream<String> stream = htmlParser.getUrls(startUrl)
            .parallelStream()
            .filter(url -> isSameHostname(url, hostname))
            .filter(url -> visited.add(url))
            .flatMap(url -> crawl(url, htmlParser, hostname, visited));

        return Stream.concat(Stream.of(startUrl), stream);
    }

    private String getHostname(String url) {
        int idx = url.indexOf('/', 7);
        return (idx != -1) ? url.substring(0, idx) : url;
    }

    private boolean isSameHostname(String url, String hostname) {
        if (!url.startsWith(hostname)) {
            return false;
        }
        return url.length() == hostname.length() || url.charAt(hostname.length()) == '/';
    }
}
```

#### 920    Number of Music Playlists

[播放列表，全部播完才能重复，大数据集 - DP](https://leetcode-cn.com/problems/number-of-music-playlists/solution/bo-fang-lie-biao-de-shu-liang-by-leetcode/)

#### 380    Insert Delete GetRandom O\(1\) - 无重复

[常数时间对数据结构插入删除和随机访问](https://leetcode-cn.com/problems/insert-delete-getrandom-o1/solution/chang-shu-shi-jian-cha-ru-shan-chu-he-huo-qu-sui-j/)

#### 844    Backspace String Compare

[\#代表退格的字符串判断相等](https://leetcode-cn.com/problems/backspace-string-compare/solution/bi-jiao-han-tui-ge-de-zi-fu-chuan-by-leetcode-solu/)

#### 435    Non-overlapping Intervals

[找到需要移除的最小区间数目，让剩下的互不重叠 - DP](https://leetcode-cn.com/problems/non-overlapping-intervals/solution/wu-zhong-die-qu-jian-by-leetcode/)

#### 529    Minesweeper

[扫雷游戏 - DFS/BFS](https://leetcode-cn.com/problems/minesweeper/solution/sao-lei-you-xi-by-leetcode-solution/)

#### 329    Longest Increasing Path in a Matrix

[从任意单元格开始的最长路径 - 记忆化搜索/DP](https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer#329-longest-increasing-path-in-a-matrix)

#### 875    Koko Eating Bananas

[吃香蕉的速度 - 二分](https://leetcode-cn.com/problems/koko-eating-bananas/solution/ai-chi-xiang-jiao-de-ke-ke-by-leetcode/)

#### 548    Split Array with Equal Sum $

[将数组分为三个子数组，三者sum相同 - Cumulative Sum + HashSet](https://leetcode.com/problems/split-array-with-equal-sum/solution/)

In this approach, firstly we form a cumulative sum array sum, where \[i\]sum\[i\] stores the cumulative sum of the array numsnumsnums upto the ith index. Then, we start by traversing over the possible positions for the middle cut formed by j. For every j, firstly, we find all the left cut's positions, i, that lead to equalizing the sum of the first and the second part \(i.e. sum\[i−1\]=sum\[j−1\]−sum\[i\]\) and store such sums in the setsetset \(a new HashSet is formed for every j chosen\). Thus, the presence of a sum in set implies that such a sum is possible for having equal sum of the first and second part for the current position of the middle cut\(j\).

Then, we go for the right cut and find the position of the right cut that leads to equal sum of the third and the fourth part \(sum\[n−1\]−sum\[k\]=sum\[k−1\]−sum\[j\]\), for the same middle cut as chosen earlier. We also, look if the same sum exists in the setsetset. If so, such a triplet \(i,j,k\) exists which satisfies the required criteria, otherwise not.

O\(n^2\)

```java
class Solution {
    public boolean splitArray(int[] nums) {
        if (nums.length < 7)
            return false;
        int[] sum = new int[nums.length];
        sum[0] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            sum[i] = sum[i - 1] + nums[i];
        }
        for (int j = 3; j < nums.length - 3; j++) {
            HashSet < Integer > set = new HashSet < > ();
            for (int i = 1; i < j - 1; i++) {
                if (sum[i - 1] == sum[j - 1] - sum[i])
                    set.add(sum[i - 1]);
            }
            for (int k = j + 2; k < nums.length - 1; k++) {
                if (sum[nums.length - 1] - sum[k] == sum[k - 1] - sum[j] && set.contains(sum[k - 1] - sum[j]))
                    return true;
            }
        }
        return false;
    }
}
```

#### 1209 Remove All Adjacent Duplicates in String II

[删除只是k倍相邻并相同的字符（循环往复操作） - 栈](https://leetcode-cn.com/problems/remove-all-adjacent-duplicates-in-string-ii/solution/shan-chu-zi-fu-chuan-zhong-de-suo-you-xiang-lin--4/)

#### 408    Valid Word Abbreviation $

[理解题意，字符串数组中单词才是有效的缩写 - 双指针](https://leetcode.com/problems/valid-word-abbreviation/)

Iterate over the characters of abbr and skip number of characters of word. Then compare ith chracter of word with jth character of abbr

```java
class Solution {
    public boolean validWordAbbreviation(String word, String abbr) {
        int number = 0;
        int i = 0, j = 0;
        while (i < word.length() && j < abbr.length()) {
            if (Character.isDigit(abbr.charAt(j))) {
                number = number * 10 + abbr.charAt(j) - '0';
                if (number == 0) return false;
                j++;
            } else {
                i += number;
                if (i >= word.length() || word.charAt(i) != abbr.charAt(j)) return false;
                number = 0;
                i++; j++;
            }
        }
        i += number;
        return i == word.length() && j == abbr.length();
    }
}
```

#### 162    Find Peak Element

[二分](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zai-array-zhong-cha-xun-yuan-su#162-find-peak-element)

#### 724    Find Pivot Index

[中心索引，左右的sum相同 - 前缀和](https://leetcode-cn.com/problems/find-pivot-index/solution/xun-zhao-shu-zu-de-zhong-xin-suo-yin-by-leetcode/)

#### 1032 Stream of Characters

[设计字符流并查询是否在dict中 - Trie](https://leetcode-cn.com/problems/stream-of-characters/solution/java-trie-by-carl-x/)

#### 658    Find K Closest Elements

[排序数组中找到距离给定数绝对值最近的的k个数 - 双指针/二分](https://leetcode-cn.com/problems/find-k-closest-elements/solution/pai-chu-fa-shuang-zhi-zhen-er-fen-fa-python-dai-ma/)

#### 1344 Angle Between Hands of a Clock

[钟表上时针和分针的夹角 - 数学转换](https://leetcode-cn.com/problems/angle-between-hands-of-a-clock/solution/shi-zhong-zhi-zhen-de-jia-jiao-by-leetcode/)

#### 1411 Number of Ways to Paint N × 3 Grid

[n x 3的网格涂色，相邻格子颜色不同 - 递推](https://leetcode-cn.com/problems/number-of-ways-to-paint-n-x-3-grid/solution/gei-n-x-3-wang-ge-tu-tu-se-de-fang-an-shu-by-leetc/)

#### 772    Basic Calculator III $

[计算器的加减乘除加括号 - 两个stacks](https://leetcode.com/problems/basic-calculator-iii/)

```java
public static int calculate(String s) {
        if (s == null || s.length() == 0) return 0;
        Stack<Integer> nums = new Stack<>(); // the stack that stores numbers
        Stack<Character> ops = new Stack<>(); // the stack that stores operators (including parentheses)
        int num = 0;
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == ' ') continue;
            if (Character.isDigit(c)) {
                num = c - '0';
                // iteratively calculate each number
                while (i < s.length() - 1 && Character.isDigit(s.charAt(i+1))) {
                    num = num * 10 + (s.charAt(i+1) - '0');
                    i++;
                }
                nums.push(num);
                num = 0; // reset the number to 0 before next calculation
            } else if (c == '(') {
                ops.push(c);
            } else if (c == ')') {
                // do the math when we encounter a ')' until '('
                while (ops.peek() != '(') nums.push(operation(ops.pop(), nums.pop(), nums.pop()));
                ops.pop(); // get rid of '(' in the ops stack
            } else if (c == '+' || c == '-' || c == '*' || c == '/') {
                while (!ops.isEmpty() && precedence(c, ops.peek())) nums.push(operation(ops.pop(), nums.pop(),nums.pop()));
                ops.push(c);
            }
        }
        while (!ops.isEmpty()) {
            nums.push(operation(ops.pop(), nums.pop(), nums.pop()));
        }
        return nums.pop();
    }

    private static int operation(char op, int b, int a) {
        switch (op) {
            case '+': return a + b;
            case '-': return a - b;
            case '*': return a * b;
            case '/': return a / b; // assume b is not 0
        }
        return 0;
    }
    // helper function to check precedence of current operator and the uppermost operator in the ops stack 
    private static boolean precedence(char op1, char op2) {
        if (op2 == '(' || op2 == ')') return false;
        if ((op1 == '*' || op1 == '/') && (op2 == '+' || op2 == '-')) return false;
        return true;
    }
```

#### 939    Minimum Area Rectangle

[一组点形成最小矩形，平行于X和Y轴 - 按列排序或者对角线](https://leetcode-cn.com/problems/minimum-area-rectangle/solution/zui-xiao-mian-ji-ju-xing-by-leetcode/)

#### 1026 Maximum Difference Between Node and Ancestor

[当前结点和其某个祖先结点的最大差值](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#1026-maximum-difference-between-node-and-ancestor)

#### 1094 Car Pooling

[顺风车拼车](https://leetcode-cn.com/problems/car-pooling/solution/gou-zao-fu-zai-shu-zu-jian-cha-fu-zai-ji-ke-by-the/)

#### 974    Subarray Sums Divisible by K

[找到所有子数组，子数组所有元素的和可被K整除 - hashmap](https://leetcode-cn.com/problems/subarray-sums-divisible-by-k/solution/he-ke-bei-k-zheng-chu-de-zi-shu-zu-by-leetcode-sol/)

#### 39 Combination Sum

[背诵题](https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking#39-combination-sum)

#### 92 Reverse Linked List II

[从m到n位置进行翻转链表](https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist#92-reverse-linked-list-ii)

#### 19 Remove Nth Node From End of List

[快慢指针](https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist#19-remove-nth-node-from-end-of-list)

#### 622    Design Circular Queue

[设计循环队列 - 数组或链表实现](https://app.gitbook.com/@guilindev/s/interview/leetcode/design#622-design-circular-queue)

#### 767    Reorganize String

[重排字符让相邻字符不同](https://app.gitbook.com/@guilindev/s/interview/leetcode/tag0#767-reorganize-string)

#### 348    Design Tic-Tac-Toe

[不用绘制棋盘](https://app.gitbook.com/@guilindev/s/interview/leetcode/design#348-design-tic-tac-toe)

#### 295    Find Median from Data Stream

[两个优先队列](https://app.gitbook.com/@guilindev/s/interview/leetcode/heap#295-find-median-from-data-stream)

#### 787    Cheapest Flights Within K Stops

[最多经过K次中转的最便宜机票 - Dijkstra](https://leetcode-cn.com/problems/cheapest-flights-within-k-stops/solution/k-zhan-zhong-zhuan-nei-zui-bian-yi-de-hang-ban-b-2/)

#### 824    Goat Latin

[山羊拉丁替换字符，辅音和元音不同替换方式](https://leetcode-cn.com/problems/goat-latin/solution/shan-yang-la-ding-wen-by-leetcode/)

## 540 Single Element in a Sorted Array

找出只出现一次的元素，别的都是出现两次，二分查找

```java
class Solution {
    /**
    [1,1,2,3,3,4,4,8,8]
    */
    public int singleNonDuplicate(int[] nums) {
        int start = 0, end = nums.length - 1;
        while (start < end) {
            // We want the first element of the middle pair,
            // which should be at an even index if the left part is sorted.
            int mid = start + (end - start)/2;
            if (mid % 2 == 1) {
                mid--;
            }
            
            if (nums[mid] != nums[mid+1]) {//didn't find a pair. The single element must be on the left.
                end = mid;
            } else {// found a pair. The single element must be on the right.
                start = mid + 2;
            }
        }
        // 'start' should always be at the beginning of a pair.
        // When 'start > end', start must be the single element.
        return nums[start];
    }
}
```

## Extra: Merge Two Sorted Interval Lists

**样例1**

```text
输入: [(1,2),(3,4)] and list2 = [(2,3),(5,6)]
输出: [(1,4),(5,6)]
解释:
(1,2),(2,3),(3,4) --> (1,4)
(5,6) --> (5,6)
```

**样例2**

```text
输入: [(1,2),(3,4)] 和 list2 = [(4,5),(6,7)]
输出: [(1,2),(3,5),(6,7)]
解释:
(1,2) --> (1,2)
(3,4),(4,5) --> (3,5)
(6,7) --> (6,7)
```

```java
class Interval {
    int start;
    int end;
    public Interval(int start, int end) {
        this.start = start;
        this.end = end;
    }
}

class myComparator implements Comparator<Interval> {
    @Override
    public int compare(Interval i1, Interval i2) {
        if (i1.start == i2.start) {
            return 0;
        } else {
            return i1.start < i2.start? -1: 1;
        }
    }
}

class IntervalMerge {
    public List<Interval> mergeList(List<Interval> l1, List<Interval> l2) {
        if (l1 == null || l1.size()  == 0) {
            return l2;
        } else if (l2 == null || l2.size() == 0) {
            return l1;
        }

        Collections.sort(l1, new myComparator());
        Collections.sort(l2, new myComparator());

        List<Interval> result = new ArrayList<>();
        int ix1 = 0;
        int ix2 = 0;
        // Get the first interval
        Interval prev = null;
        if (l1.get(0).start < l2.get(0).start) {
            prev = l1.get(0);
            ix1 ++;
        } else {
            prev = l2.get(0);
            ix2 ++;
        }
        // Move two pointers to merge lists
        while (ix1 < l1.size() || ix2 < l2.size()) {
            if (ix2 == l2.size() || (ix1 < l1.size() && l1.get(ix1).start < l2.get(ix2).start)) {
                // merge prev with ix1
                if (prev.end < l1.get(ix1).start) {
                    result.add(prev);
                    prev = l1.get(ix1);
                } else {
                    prev.end = Math.max(prev.end, l1.get(ix1).end);
                }
                ix1 ++;
            } else {
                // merge prev with ix2
                if (prev.end < l2.get(ix2).start) {
                    result.add(prev);
                    prev = l2.get(ix2);
                } else {
                    prev.end = Math.max(prev.end, l2.get(ix2).end);
                }
                ix2 ++;
            }
        }
        result.add(prev);
        return result;
    }

}
```


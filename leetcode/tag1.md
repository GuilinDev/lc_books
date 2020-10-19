# tag1

## 346 Moving Average from Data Stream 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/queue\#346-moving-average-from-data-stream](https://app.gitbook.com/@guilindev/s/interview/leetcode/queue#346-moving-average-from-data-stream)

## 753 Cracking the Safe 

### 原题

有一个需要密码才能打开的保险箱。密码是 n 位数, 密码的每一位是 k 位序列 0, 1, ..., k-1 中的一个 。

你可以随意输入密码，保险箱会自动记住最后 n 位输入，如果匹配，则能够打开保险箱。

举个例子，假设密码是 "345"，你可以输入 "012345" 来打开它，只是你输入了 6 个字符.

请返回一个能打开保险箱的最短字符串。

There is a box protected by a password. The password is a sequence of `n` digits where each digit can be one of the first `k` digits `0, 1, ..., k-1`.

While entering a password, the last `n` digits entered will automatically be matched against the correct password.

For example, assuming the correct password is `"345"`, if you type `"012345"`, the box will open because the correct password matches the suffix of the entered password.

Return any password of **minimum length** that is guaranteed to open the box at some point of entering it.

**Example 1:**

```text
Input: n = 1, k = 2
Output: "01"
Note: "10" will be accepted too.
```

**Example 2:**

```text
Input: n = 2, k = 2
Output: "00110"
Note: "01100", "10011", "11001" will be accepted too.
```

**Note:**

1. `n` will be in the range `[1, 4]`.
2. `k` will be in the range `[1, 10]`.
3. `k^n` will be at most `4096`.

### 思路

寻找欧拉回路\(Euler Circuit\)

![](../.gitbook/assets/image%20%28133%29.png)

### 代码

```java
import java.util.Collections;
import java.util.TreeSet;

class Solution {
    TreeSet<String> visited;
    StringBuilder res;
    public String crackSafe(int n, int k) {
        if(n == 1 && k == 1) return "0";
        visited = new TreeSet<>();
        res = new StringBuilder();
        //  从顶点 00..0 开始
        String start = String.join("", Collections.nCopies(n-1, "0"));;
        findEuler(start, k);

        res.append(start); // 回路添加最后的end顶点，end 就是 start
        return res.toString(); // return new String(res);
    }
    public void findEuler(String curv, int k){

        for(int i = 0; i < k; i ++){
            // 往顶点的 k 条出边检查，顶点加一条出边就是一种密码可能
            String nextv = curv + i;
            if(!visited.contains(nextv)){
                visited.add(nextv);
                findEuler(nextv.substring(1), k);
                res.append(i);
            }
        }
    }
}
```

## 332 Reconstruct Itinerary

### 原题

Given a list of airline tickets represented by pairs of departure and arrival airports `[from, to]`, reconstruct the itinerary in order. All of the tickets belong to a man who departs from `JFK`. Thus, the itinerary must begin with `JFK`.

**Note:**

1. If there are multiple valid itineraries, you should return the itinerary that has the smallest lexical order when read as a single string. For example, the itinerary `["JFK", "LGA"]` has a smaller lexical order than `["JFK", "LGB"]`.
2. All airports are represented by three capital letters \(IATA code\).
3. You may assume all tickets form at least one valid itinerary.
4. One must use all the tickets once and only once.

**Example 1:**

```text
Input: [["MUC", "LHR"], ["JFK", "MUC"], ["SFO", "SJC"], ["LHR", "SFO"]]
Output: ["JFK", "MUC", "LHR", "SFO", "SJC"]
```

**Example 2:**

```text
Input: [["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]
Output: ["JFK","ATL","JFK","SFO","ATL","SFO"]
Explanation: Another possible reconstruction is ["JFK","SFO","ATL","JFK","ATL","SFO"].
             But it is larger in lexical order.
```

### 思路

DFS

![](../.gitbook/assets/image%20%28135%29.png)

![](../.gitbook/assets/image%20%28141%29.png)

![](../.gitbook/assets/image%20%28136%29.png)

### 代码

```java
import java.util.*;
class Solution {
    public List<String> findItinerary(List<List<String>> tickets) {
        // 因为逆序插入，所以用链表
        List<String> ans = new LinkedList<>();
        if (tickets == null || tickets.size() == 0)
            return ans;
        Map<String, List<String>> graph = new HashMap<>();
        for (List<String> pair : tickets) {
            // 因为涉及删除操作，我们用链表
            List<String> nbr = graph.computeIfAbsent(pair.get(0), k -> new LinkedList<>());
            nbr.add(pair.get(1));
        }
        // 按目的顶点排序
        graph.values().forEach(x -> x.sort(String::compareTo));
        visit(graph, "JFK", ans);
        return ans;
    }
    // DFS方式遍历图，当走到不能走为止，再将节点加入到答案
    private void visit(Map<String, List<String>> graph, String src, List<String> ans) {
        List<String> nbr = graph.get(src);
        while (nbr != null && nbr.size() > 0) {
            String dest = nbr.remove(0);
            visit(graph, dest, ans);
        }
        ans.add(0, src); // 逆序插入
    }
}
```

也可以不对临接点排序，而是使用小顶堆（Java可以用优先队列）。这样我们删除边的操作和访问最小字典顺序顶点可以用出队操作代替，时间复杂度应该会比排序再删除要低一些。

```java
import java.util.*;
class Solution {
    public List<String> findItinerary(List<List<String>> tickets) {
        // 因为逆序插入，所以用链表
        List<String> ans = new LinkedList<>();
        if (tickets == null || tickets.size() == 0)
            return ans;
        Map<String, PriorityQueue<String>> graph = new HashMap<>();
        for (List<String> pair : tickets) {
            // 因为涉及删除操作，我们用链表
            PriorityQueue<String> nbr = graph.computeIfAbsent(pair.get(0), k -> new PriorityQueue<>());
            nbr.add(pair.get(1));
        }
        visit(graph, "JFK", ans);
        return ans;
    }
    // DFS方式遍历图，当走到不能走为止，再将节点加入到答案
    private void visit(Map<String, PriorityQueue<String>> graph, String src, List<String> ans) {
        PriorityQueue<String> nbr = graph.get(src);
        while (nbr != null && nbr.size() > 0) {
            String dest = nbr.poll();
            visit(graph, dest, ans);
        }
        ans.add(0, src); // 逆序插入
    }
}
```

## 809 Expressive Words 

### 原题

情感丰富的文字，有时候人们会用重复写一些字母来表示额外的感受，比如 "hello" -&gt; "heeellooo", "hi" -&gt; "hiii"。我们将相邻字母都相同的一串字符定义为相同字母组，例如："h", "eee", "ll", "ooo"。

对于一个给定的字符串 S ，如果另一个单词能够通过将一些字母组扩张从而使其和 S 相同，我们将这个单词定义为可扩张的（stretchy）。扩张操作定义如下：选择一个字母组（包含字母 c ），然后往其中添加相同的字母 c 使其长度达到 3 或以上。

例如，以 "hello" 为例，我们可以对字母组 "o" 扩张得到 "hellooo"，但是无法以同样的方法得到 "helloo" 因为字母组 "oo" 长度小于 3。此外，我们可以进行另一种扩张 "ll" -&gt; "lllll" 以获得 "helllllooo"。如果 S = "helllllooo"，那么查询词 "hello" 是可扩张的，因为可以对它执行这两种扩张操作使得 query = "hello" -&gt; "hellooo" -&gt; "helllllooo" = S。

输入一组查询单词，输出其中可扩张的单词数量。

Sometimes people repeat letters to represent extra feeling, such as "hello" -&gt; "heeellooo", "hi" -&gt; "hiiii".  In these strings like "heeellooo", we have _groups_ of adjacent letters that are all the same:  "h", "eee", "ll", "ooo".

For some given string `S`, a query word is _stretchy_ if it can be made to be equal to `S` by any number of applications of the following _extension_ operation: choose a group consisting of characters `c`, and add some number of characters `c` to the group so that the size of the group is 3 or more.

For example, starting with "hello", we could do an extension on the group "o" to get "hellooo", but we cannot get "helloo" since the group "oo" has size less than 3.  Also, we could do another extension like "ll" -&gt; "lllll" to get "helllllooo".  If `S = "helllllooo"`, then the query word "hello" would be stretchy because of these two extension operations: `query = "hello" -> "hellooo" -> "helllllooo" = S`.

Given a list of query words, return the number of words that are stretchy. 

```text
Example:
Input: 
S = "heeellooo"
words = ["hello", "hi", "helo"]
Output: 1
Explanation: 
We can extend "e" and "o" in the word "hello" to get "heeellooo".
We can't extend "helo" to get "heeellooo" because the group "ll" is not size 3 or more.
```

**Constraints:**

* `0 <= len(S) <= 100`.
* `0 <= len(words) <= 100`.
* `0 <= len(words[i]) <= 100`.
* `S` and all words in `words` consist only of lowercase letters

### 思路

![](../.gitbook/assets/image%20%28134%29.png)

![](../.gitbook/assets/image%20%28139%29.png)

### 代码

```java
class Solution {
    public int expressiveWords(String s, String[] words) {
        List<Object[]> target = countWord(s);
        int ans = 0;
        for(String word : words){
            List<Object[]> list = countWord(word);
            // 如果两段不相等，跳过
            if(list.size() != target.size())
                continue;
            int i = 0;
            for(; i < target.size(); i++){
                // 该段字符不同，break
                if((char)target.get(i)[0] != (char)list.get(i)[0])
                    break;
                int t1 = (int)target.get(i)[1];
                int t2 = (int)list.get(i)[1];
                // 如果该段字母出现的次数比s还要大，无法扩张，若该条件不满足，此时t1 >= t2，
                // 此时若两者不相等且t1小于3，则s无法通过word扩张得到
                if(t1 < t2 || (t1 != t2 && t1 < 3))
                    break;
            }
            // 如果i走到了最后，则word能够扩张得到s
            if(i == target.size())
                ans++;
        }
        return ans;
    }

    // 统计字符串每段字母及其出现的次数
    // List中存储的是长度为2的数组,o[0]是该段的字母，o[1]是该段该字母出现的频次
    List<Object[]> countWord(String s){
        List<Object[]> list = new ArrayList<>();
        int i = 0;
        while(i < s.length()){
            char c = s.charAt(i);
            int count = 0;
            while(i < s.length() && c == s.charAt(i)){
                count++;
                i++;
            }
            list.add(new Object[]{c, count});
        }
        return list;
    }
}

```

## 471 Encode String with Shortest Length 

### 原题

给定一个 非空 字符串，将其编码为具有最短长度的字符串。

编码规则是：k\[encoded\_string\]，其中在方括号 encoded\_string 中的内容重复 k 次。

注：

* k 为正整数 
* 如果编码的过程不能使字符串缩短，则不要对其进行编码。如果有多种编码方式，返回 任意一种 即可

Given a **non-empty** string, encode the string such that its encoded length is the shortest.

The encoding rule is: `k[encoded_string]`, where the `encoded_string` inside the square brackets is being repeated exactly `k` times.

**Note:**

1. `k` will be a positive integer.
2. If an encoding process does not make the string shorter, then do not encode it. If there are several solutions, return **any of them**.

**Example 1:**

```text
Input: s = "aaa"
Output: "aaa"
Explanation: There is no way to encode it such that it is shorter than the input string, so we do not encode it.
```

**Example 2:**

```text
Input: s = "aaaaa"
Output: "5[a]"
Explanation: "5[a]" is shorter than "aaaaa" by 1 character.
```

**Example 3:**

```text
Input: s = "aaaaaaaaaa"
Output: "10[a]"
Explanation: "a9[a]" or "9[a]a" are also valid solutions, both of them have the same length = 5, which is the same as "10[a]".
```

**Example 4:**

```text
Input: s = "aabcaabcd"
Output: "2[aabc]d"
Explanation: "aabc" occurs twice, so one answer can be "2[aabc]d".
```

**Example 5:**

```text
Input: s = "abbbabbbcabbbabbbc"
Output: "2[2[abbb]c]"
Explanation: "abbbabbbc" occurs twice, but "abbbabbbc" can also be encoded to "2[abbb]c", so one answer can be "2[2[abbb]c]".
```

**Constraints:**

* `1 <= s.length <= 150`
* `s` consists of only lowercase English letters.

### 思路

给定一个 非空 字符串，将其编码为具有最短长度的字符串。编码规则是：k\[encoded\_string\]，其中在方括号 encoded\_string 中的内容重复 k 次。

注：k 为正整数 如果编码的过程不能使字符串缩短，则不要对其进行编码。如果有多种编码方式，返回 任意一种 即可。

![](../.gitbook/assets/image%20%28137%29.png)

### 代码

区间型DP

```java
class Solution {
    String[][] dp;
    public String encode(String s){
        int n = s.length();
        dp = new String[n][n];
        
        for(int len = 1; len <= n; len++){
            for(int i = 0; i + len - 1 < n; i++){
                int j = i + len - 1;
                dp[i][j] = lc459(s, i, j);
                if(len > 4){
                    for(int k = i; k < j; k++){
                        String split = dp[i][k] + dp[k + 1][j];
                        if(dp[i][j].length() > split.length()) dp[i][j] = split;
                    }
                }
            }
        }
        return dp[0][n - 1];
    }

    /**
     * 另 t = s + s, 从下标 1 的字符开始查找字符串s， 找到下标p，
     * 如果p != n, 存在连续重复的子字符串ps = s.substring(0, p), 个数为 n / p
     * 否则， 不存在连续重复子字符串， 无法进行编码
     */
    public String lc459(String s, int i, int j){
        s = s.substring(i, j + 1);
        if(s.length() < 5)  return s;
        int p = (s + s).indexOf(s, 1);
        if(p != s.length()){
            int cnt = s.length() / p;
            return cnt + "[" + dp[i][i + p - 1] + "]";
        }
        //否则， 无法进行编码
        return s;
    }
}
```

## 1110 Delete Nodes And Return Forest 

### 原题

删点成林，给出二叉树的根节点 root，树上每个节点都有一个不同的值。如果节点值在 to\_delete 中出现，我们就把该节点从树上删去，最后得到一个森林（一些不相交的树构成的集合）。返回森林中的每棵树。你可以按任意顺序组织答案。

Given the `root` of a binary tree, each node in the tree has a distinct value.

After deleting all nodes with a value in `to_delete`, we are left with a forest \(a disjoint union of trees\).

Return the roots of the trees in the remaining forest.  You may return the result in any order.

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/07/01/screen-shot-2019-07-01-at-53836-pm.png)

```text
Input: root = [1,2,3,4,5,6,7], to_delete = [3,5]
Output: [[1,2,null,4],[6],[7]]
```

**Constraints:**

* The number of nodes in the given tree is at most `1000`.
* Each node has a distinct value between `1` and `1000`.
* `to_delete.length <= 1000`
* `to_delete` contains distinct values between `1` and `1000`.

### 思路

算法

首先把to\_delete变成set，此处不多说； 

节点进入结果当中，有2个条件：

* 不被删除 
* 父节点不存在 

因此在遍历过程中，将parentExists标志传递给子节点，子递归就可以选择是否加入到结果。 另外，如果子节点被删除，父节点的left、right字段需要更新。

### 代码

DFS做后序遍历

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
    Set<Integer> toDelete;
    public List<TreeNode> delNodes(TreeNode root, int[] to_delete) {
        toDelete = Arrays.stream(to_delete).boxed().collect(Collectors.toSet());
        List<TreeNode> ans = new ArrayList<>();
        helper(root, ans, false);
        return ans;
    }

    boolean helper(TreeNode root, List<TreeNode> ans, boolean parentExists) {
        boolean del = false;
        if (root == null) {
            return del;
        }
        del = toDelete.contains(root.val);
        if (helper(root.left, ans, !del)) {
            root.left = null;
        }
        if (helper(root.right, ans, !del)) {
            root.right = null;
        }
        if (!del && !parentExists) {
            ans.add(root);
        }
        return del;
    }
}
```

## 777 Swap Adjacent in LR String 

### 原题

在一个由 'L' , 'R' 和 'X' 三个字符组成的字符串（例如"RXXLRXRXL"）中进行移动操作。一次移动操作指用一个"LX"替换一个"XL"，或者用一个"XR"替换一个"RX"。现给定起始字符串start和结束字符串end，请编写代码，当且仅当存在一系列移动操作使得start可以转换成end时， 返回True。

In a string composed of `'L'`, `'R'`, and `'X'` characters, like `"RXXLRXRXL"`, a move consists of either replacing one occurrence of `"XL"` with `"LX"`, or replacing one occurrence of `"RX"` with `"XR"`. Given the starting string `start` and the ending string `end`, return `True` if and only if there exists a sequence of moves to transform one string to the other.

**Example 1:**

```text
Input: start = "RXXLRXRXL", end = "XRLXXRRLX"
Output: true
Explanation: We can transform start to end following these steps:
RXXLRXRXL ->
XRXLRXRXL ->
XRLXRXRXL ->
XRLXXRRXL ->
XRLXXRRLX
```

**Example 2:**

```text
Input: start = "X", end = "L"
Output: false
```

**Example 3:**

```text
Input: start = "LLR", end = "RRL"
Output: false
```

**Example 4:**

```text
Input: start = "XL", end = "LX"
Output: true
```

**Example 5:**

```text
Input: start = "XLLR", end = "LXLX"
Output: false
```

**Constraints:**

* `1 <= start.length <= 104`
* `start.length == end.length`
* Both `start` and `end` will only consist of characters in `'L'`, `'R'`, and `'X'`.

### 思路

可以用双指针来解决这个问题，对于 i， j 两个指针，分别让他们指向 start 和 end，且保证 start\[i\] != 'X'，end\[j\] != 'X'。接下来开始移动指针，如果 start\[i\] != end\[j\]，则不满足 转换不变性，如果 start\[i\] == 'L' 且 i &lt; j，则不满足 可到达性。

* 时间复杂度：O\(N\)，其中NNN 为 `start` 和 `end` 的长度。
* 空间复杂度：O\(1\)O\(1\)O\(1\)。

### 代码

```java
class Solution {
    public boolean canTransform(String start, String end) {
        int N = start.length();
        char[] S = start.toCharArray(), T = end.toCharArray();
        int i = -1, j = -1;
        while (++i < N && ++j < N) {
            while (i < N && S[i] == 'X') i++;
            while (j < N && T[j] == 'X') j++;
            /* At this point, i == N or S[i] != 'X',
               and j == N or T[j] != 'X'.  i and j
               are the indices representing the next
               occurrences of non-X characters in S and T.
            */

            // If only one of i < N and j < N, then it isn't solid-
            // there's more people in one of the strings.
            if ((i < N) ^ (j < N)) return false;

            if (i < N && j < N) {
                // If the person isn't the same, it isn't solid.
                // Or, if the person moved backwards, it isn't accessible.
                if (S[i] != T[j] || (S[i] == 'L' && i < j) ||
                        (S[i] == 'R' && i > j) )
                    return false;
            }
        }
        return true;
    }
}
```

## 659 Split Array into Consecutive Subsequences 

### 原题

给你一个按升序排序的整数数组 num（可能包含重复数字），请你将它们分割成一个或多个子序列，其中每个子序列都由连续整数组成且长度至少为 3 。

如果可以完成上述分割，则返回 true ；否则，返回 false 。

Given an array `nums` sorted in ascending order, return `true` if and only if you can split it into 1 or more subsequences such that each subsequence consists of consecutive integers and has length at least 3.

**Example 1:**

```text
Input: [1,2,3,3,4,5]
Output: True
Explanation:
You can split them into two consecutive subsequences : 
1, 2, 3
3, 4, 5
```

**Example 2:**

```text
Input: [1,2,3,3,4,4,5,5]
Output: True
Explanation:
You can split them into two consecutive subsequences : 
1, 2, 3, 4, 5
3, 4, 5
```

**Example 3:**

```text
Input: [1,2,3,4,4,5]
Output: False
```

**Constraints:**

* `1 <= nums.length <= 10000`

### 思路

贪心算法。

**想法**

我们把 3 个或更多的连续数字称作 chain。

我们从左到右考虑每一个数字 x，如果 x 可以被添加到当前的 chain 中，我们将 x 添加到 chain 中，这一定会比创建一个新的 chain 要更好。

为什么呢？如果我们以 x 为起点新创建一个 chain ，这条新创建更短的链是可以接在之前的链上的，这可能会帮助我们避免创建一个从 x 开始的长度为 1 或者 2 的短链。

**算法**

我们将每个数字的出现次数统计好，记 tails\[x\] 是恰好在 x 之前结束的链的数目。

现在我们逐一考虑每个数字，如果有一个链恰好在 x 之前结束，我们将 x 加入此链中。否则，如果我们可以新建立一条链就新建。

我们可以优化额外空间到 O\(1\)O\(1\)O\(1\)，因为我们可以像 方法 1 一样统计数字的出现次数，而且我们只需要知道最后 3 个数字的出现次数即可。

### 代码

```java
class Solution {
    public boolean isPossible(int[] nums) {
        Counter count = new Counter();
        Counter tails = new Counter();
        for (int x: nums) count.add(x, 1);

        for (int x: nums) {
            if (count.get(x) == 0) {
                continue;
            } else if (tails.get(x) > 0) {
                tails.add(x, -1);
                tails.add(x+1, 1);
            } else if (count.get(x+1) > 0 && count.get(x+2) > 0) {
                count.add(x+1, -1);
                count.add(x+2, -1);
                tails.add(x+3, 1);
            } else {
                return false;
            }
            count.add(x, -1);
        }
        return true;
    }
}

class Counter extends HashMap<Integer, Integer> {
    public int get(int k) {
        return containsKey(k) ? super.get(k) : 0;
    }

    public void add(int k, int v) {
        put(k, get(k) + v);
    }
}
```

## 299 Bulls and Cows 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table\#299-bulls-and-cows](https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table#299-bulls-and-cows)

## 1477 Find Two Non-overlapping Sub-arrays Each With Target Sum 

### 原题

给你一个整数数组 arr 和一个整数值 target 。

请你在 arr 中找 两个互不重叠的子数组 且它们的和都等于 target 。可能会有多种方案，请你返回满足要求的两个子数组长度和的 最小值 。

请返回满足要求的最小长度和，如果无法找到这样的两个子数组，请返回 -1 。

给你一个整数数组 arr 和一个整数值 target 。

请你在 arr 中找 两个互不重叠的子数组 且它们的和都等于 target 。可能会有多种方案，请你返回满足要求的两个子数组长度和的 最小值 。

请返回满足要求的最小长度和，如果无法找到这样的两个子数组，请返回 -1 。

Given an array of integers `arr` and an integer `target`.

You have to find **two non-overlapping sub-arrays** of `arr` each with sum equal `target`. There can be multiple answers so you have to find an answer where the sum of the lengths of the two sub-arrays is **minimum**.

Return _the minimum sum of the lengths_ of the two required sub-arrays, or return _**-1**_ if you cannot find such two sub-arrays.

**Example 1:**

```text
Input: arr = [3,2,2,4,3], target = 3
Output: 2
Explanation: Only two sub-arrays have sum = 3 ([3] and [3]). The sum of their lengths is 2.
```

**Example 2:**

```text
Input: arr = [7,3,4,7], target = 7
Output: 2
Explanation: Although we have three non-overlapping sub-arrays of sum = 7 ([7], [3,4] and [7]), but we will choose the first and third sub-arrays as the sum of their lengths is 2.
```

**Example 3:**

```text
Input: arr = [4,3,2,6,2,3,4], target = 6
Output: -1
Explanation: We have only one sub-array of sum = 6.
```

**Example 4:**

```text
Input: arr = [5,5,4,4,5], target = 3
Output: -1
Explanation: We cannot find a sub-array of sum = 3.
```

**Example 5:**

```text
Input: arr = [3,1,1,1,5,1,2,1], target = 3
Output: 3
Explanation: Note that sub-arrays [1,2] and [2,1] cannot be an answer because they overlap.
```

**Constraints:**

* `1 <= arr.length <= 10^5`
* `1 <= arr[i] <= 1000`
* `1 <= target <= 10^8`

### 思路

![](../.gitbook/assets/image%20%28140%29.png)

时间复杂度 O\(n\)，一次扫描

空间复杂度 O\(n\)，用了一个dp数组

### 代码

```java
class Solution {
    public int minSumOfLengths(int[] arr, int target) {
        int n = arr.length;
        int[] dp = new int[n];
        // 注意不能设置为最大值，因为相加会溢出
        Arrays.fill(dp, Integer.MAX_VALUE / 2);

        int ans = Integer.MAX_VALUE;
        for(int i = 0, j = 0, sum = 0; j < n; j++){
            sum += arr[j];
            while(i <= j && sum > target){
                sum -= arr[i++];
            }
            // 找到满足条件的一个区间
            if(sum == target){
                dp[j] = j - i + 1;
                if(i != 0){
                    ans = Math.min(ans, dp[i-1] + j - i + 1);
                }
            }
            if(j != 0)
                dp[j] = Math.min(dp[j], dp[j-1]);
        }

        return ans > arr.length ? -1 : ans;
    }
}

```

## 752 Open the Lock 

### 原题

你有一个带有四个圆形拨轮的转盘锁。每个拨轮都有10个数字： '0', '1', '2', '3', '4', '5', '6', '7', '8', '9' 。每个拨轮可以自由旋转：例如把 '9' 变为 '0'，'0' 变为 '9' 。每次旋转都只能旋转一个拨轮的一位数字。

锁的初始数字为 '0000' ，一个代表四个拨轮的数字的字符串。

列表 deadends 包含了一组死亡数字，一旦拨轮的数字和列表里的任何一个元素相同，这个锁将会被永久锁定，无法再被旋转。

字符串 target 代表可以解锁的数字，你需要给出最小的旋转次数，如果无论如何不能解锁，返回 -1。

You have a lock in front of you with 4 circular wheels. Each wheel has 10 slots: `'0', '1', '2', '3', '4', '5', '6', '7', '8', '9'`. The wheels can rotate freely and wrap around: for example we can turn `'9'` to be `'0'`, or `'0'` to be `'9'`. Each move consists of turning one wheel one slot.

The lock initially starts at `'0000'`, a string representing the state of the 4 wheels.

You are given a list of `deadends` dead ends, meaning if the lock displays any of these codes, the wheels of the lock will stop turning and you will be unable to open it.

Given a `target` representing the value of the wheels that will unlock the lock, return the minimum total number of turns required to open the lock, or -1 if it is impossible.

**Example 1:**

```text
Input: deadends = ["0201","0101","0102","1212","2002"], target = "0202"
Output: 6
Explanation:
A sequence of valid moves would be "0000" -> "1000" -> "1100" -> "1200" -> "1201" -> "1202" -> "0202".
Note that a sequence like "0000" -> "0001" -> "0002" -> "0102" -> "0202" would be invalid,
because the wheels of the lock become stuck after the display becomes the dead end "0102".
```

**Example 2:**

```text
Input: deadends = ["8888"], target = "0009"
Output: 1
Explanation:
We can turn the last wheel in reverse to move from "0000" -> "0009".
```

**Example 3:**

```text
Input: deadends = ["8887","8889","8878","8898","8788","8988","7888","9888"], target = "8888"
Output: -1
Explanation:
We can't reach the target without getting stuck.
```

**Example 4:**

```text
Input: deadends = ["0000"], target = "8888"
Output: -1
```

**Constraints:**

* `1 <= deadends.length <= 500`
* `deadends[i].length == 4`
* `target.length == 4`
* target **will not be** in the list `deadends`.
* `target` and `deadends[i]` consist of digits only.

### 思路

BFS，类似111-求二叉树的最小深度。

BFS可以求解最值问题，当每种密码锁每次都转动一次时，总共有8个相邻的密码值，当这8个中有target值时，步数+1并返回，可以理解为树的结构，每个结点都有8个子结点，当从子结点中找到target时，步数+1并返回。所以可以用BFS队列来求解最小次数

### 代码

```java
class Solution {
    public int openLock(String[] deadends, String target) {
        //Set，用来判断密码值是否是死亡数组中的值，这样就不用for循环来遍历了
        Set<String> deads = new HashSet<>();
        for (String s : deadends) deads.add(s);
        //Set，用来判断某一密码值相邻密码值是否重复出现，防止死循环
        Set<String> visited = new HashSet<>();
        //Queue，队列，BFS要用到的，就不用多说了吧
        Queue<String> queue = new LinkedList();
        queue.add("0000");
        visited.add("0000");
        //步数
        int step = 0;

        //当所有密码值的情况都到时结束
        while (!queue.isEmpty()) {
            int len = queue.size();

            //遍历队列中的密码值
            for (int i = 0; i < len; i++) {
                String s = queue.poll();
                //如果死亡数组中包含当前密码值，跳过循环，因为这时密码值已经被锁定，不能进行下一步了
                if (deads.contains(s)) continue;
                if (s.equals(target)) return step;

                //遍历当前密码值的相邻8个密码值并作判断是否重复出现
                for (int j = 0; j < 4; j++) {
                    String up = up(s, j);
                    String down = down(s, j);
                    if (!visited.contains(up)) {
                        queue.add(up);
                        visited.add(up);
                    }
                    if (!visited.contains(down)) {
                        queue.add(down);
                        visited.add(down);
                    }

                }
            }
            //当前树的一层遍历结束，步数+1
            step++;
        }
        //无解，返回-1
        return -1;
    }

    public String up (String s, int j) {
        char[] c = s.toCharArray();
        if (c[j] == '9') {
            c[j] = '0';
        } else {
            c[j]++;
        }
        return new String(c);
    }

    public String down (String s, int j) {
        char[] c = s.toCharArray();
        if (c[j] == '0') {
            c[j] = '9';
        } else {
            c[j]--;
        }
        return new String(c);
    }
}
```

## 444 Sequence Reconstruction 

### 原题

验证原始的序列 org 是否可以从序列集 seqs 中唯一地重建。序列 org 是 1 到 n 整数的排列，其中 1 ≤ n ≤ 104。重建是指在序列集 seqs 中构建最短的公共超序列。（即使得所有 seqs 中的序列都是该最短序列的子序列）。确定是否只可以从 seqs 重建唯一的序列，且该序列就是 org 。

Check whether the original sequence `org` can be uniquely reconstructed from the sequences in `seqs`. The `org` sequence is a permutation of the integers from 1 to n, with 1 ≤ n ≤ 104. Reconstruction means building a shortest common supersequence of the sequences in `seqs` \(i.e., a shortest sequence so that all sequences in `seqs` are subsequences of it\). Determine whether there is only one sequence that can be reconstructed from `seqs` and it is the `org` sequence.

**Example 1:**

```text
Input: org = [1,2,3], seqs = [[1,2],[1,3]]
Output: false
Explanation: [1,2,3] is not the only one sequence that can be reconstructed, because [1,3,2] is also a valid sequence that can be reconstructed.
```

**Example 2:**

```text
Input: org = [1,2,3], seqs = [[1,2]]
Output: false
Explanation: The reconstructed sequence can only be [1,2].
```

**Example 3:**

```text
Input: org = [1,2,3], seqs = [[1,2],[1,3],[2,3]]
Output: true
Explanation: The sequences [1,2], [1,3], and [2,3] can uniquely reconstruct the original sequence [1,2,3].
```

**Example 4:**

```text
Input: org = [4,1,5,2,6,3], seqs = [[5,2,6,3],[4,1,5,2]]
Output: true
```

**Constraints:**

* `1 <= n <= 10^4`
* `org` is a permutation of {1,2,...,n}.
* `1 <= segs[i].length <= 10^5`
* `seqs[i][j]` fits in a 32-bit signed integer.

**UPDATE \(2017/1/8\):**  
 The seqs parameter had been changed to a list of list of strings \(instead of a 2d array of strings\). Please reload the code definition to get the latest changes.

### 思路

思路比较简单，但是出错点实在太多了 基本思路

1. 对输入做筛选 
2. 拓扑排序，同时判断入度为0的是否大于1. 
3. 根据排序结果，判断是否有环 
4. 把排序结果和org比较，查看是否相等。

### 代码

```java
class Solution {
    class EdgeNode {
        public int vertex;
        public List<Integer> next;

        public EdgeNode(int vertex) {
            this.vertex = vertex;
            this.next = new ArrayList<>();
        }
    }
    
    public boolean sequenceReconstruction(int[] org, List<List<Integer>> seqs) {
        if (seqs == null || seqs.size() == 0) return false;
        // 先对输入做筛选
        List<List<Integer>> seqq = new ArrayList<>();
        for (List<Integer> list: seqs) {
            List<Integer> l = new ArrayList<>();
            for (int i : list) {
               if (i>0&&i<=org.length){
                   l.add(i);
               }else {
                   return false;
               }
            }
            seqq.add(l);
        }
        seqs=seqq;
        /**
         * 标准的拓扑排序，判断入度为0的是否大于1.
         */
        int n = org.length;
        EdgeNode[] edgeNodes = new EdgeNode[n + 1];
        int[] inDegree = new int[n + 1];
        Arrays.fill(inDegree, -1);
        ArrayList<Integer> ans = new ArrayList<>();
        for (List<Integer> seq : seqs) {
            if (seq.size()==1){
                int t1 = seq.get(0);
                if (t1>n) return false;
                if (edgeNodes[t1] == null) edgeNodes[t1] = new EdgeNode(t1);
                if (inDegree[t1] == -1) inDegree[t1] = 0;
            }else {
                for (int i = 1; i < seq.size(); i++) {
                    int t1 = seq.get(i - 1);
                    int t2 = seq.get(i);
                    if (edgeNodes[t1] == null) edgeNodes[t1] = new EdgeNode(t1);
                    if (edgeNodes[t2] == null) edgeNodes[t2] = new EdgeNode(t2);
                    edgeNodes[t1].next.add(t2);
                    if (inDegree[t1] == -1) inDegree[t1] = 0;
                    if (inDegree[t2] == -1) inDegree[t2] = 0;
                    inDegree[t2]++;
                }
            }
        }
        ArrayDeque<EdgeNode> deque = new ArrayDeque<>();
        for (int i = 1; i < inDegree.length; i++) {
            if (inDegree[i] == 0) {
                deque.add(edgeNodes[i]);
            }
        }
        int index = 0;//用来记录出队列的元素的个数
        //用队列来判断
        while (!deque.isEmpty()) {
            int size = deque.size();
            // 如果队列中同时存在大于1个的，说明这两个元素顺序可变，则返回失败
            if (size > 1) return false;
            for (int i = 0; i < size; i++) {
                EdgeNode poll = deque.poll();
                ans.add(poll.vertex);
                index++;
                for (int k : poll.next) {
                    inDegree[k]--;
                    if (inDegree[k] == 0) {
                        if (edgeNodes[k] == null) System.out.println("k:" + k);
                        if (edgeNodes[k] != null) deque.add(edgeNodes[k]);
                    }
                }
            }
        }
        // 判断有没有环
        if (index < n) return false;
        for (int i = 0; i < org.length; i++) {
            if (org[i]!=ans.get(i)) return false;
        }
        return true;
    }
}
```

## 951 Flip Equivalent Binary Trees 

### 原题

反转等价二叉树，我们可以为二叉树 T 定义一个翻转操作，如下所示：选择任意节点，然后交换它的左子树和右子树。

只要经过一定次数的翻转操作后，能使 X 等于 Y，我们就称二叉树 X 翻转等价于二叉树 Y。

编写一个判断两个二叉树是否是翻转等价的函数。这些树由根节点 root1 和 root2 给出。

For a binary tree **T**, we can define a **flip operation** as follows: choose any node, and swap the left and right child subtrees.

A binary tree **X** is _flip equivalent_ to a binary tree **Y** if and only if we can make **X** equal to **Y** after some number of flip operations.

Given the roots of two binary trees `root1` and `root2`, return `true` if the two trees are flip equivelent or `false` otherwise.

**Example 1:** ![Flipped Trees Diagram](https://assets.leetcode.com/uploads/2018/11/29/tree_ex.png)

```text
Input: root1 = [1,2,3,4,5,6,null,null,null,7,8], root2 = [1,3,2,null,6,4,5,null,null,null,null,8,7]
Output: true
Explanation: We flipped at nodes with values 1, 3, and 5.
```

**Example 2:**

```text
Input: root1 = [], root2 = []
Output: true
```

**Example 3:**

```text
Input: root1 = [], root2 = [1]
Output: false
```

**Example 4:**

```text
Input: root1 = [0,null,1], root2 = []
Output: false
```

**Example 5:**

```text
Input: root1 = [0,null,1], root2 = [0,1]
Output: true
```

**Constraints:**

* The number of nodes in each tree is in the range `[0, 100]`.
* Each tree will have **unique node values** in the range `[0, 99]`.

### 思路

1）普通递归

**思路**

如果二叉树 root1，root2 根节点值相等，那么只需要检查他们的孩子是不是相等就可以了。

**算法**

存在三种情况：

* 如果 root1 或者 root2 是 null，那么只有在他们都为 null 的情况下这两个二叉树才等价。 
* 如果 root1，root2 的值不相等，那这两个二叉树的一定不等价。 
* 如果以上条件都不满足，也就是当 root1 和 root2 的值相等的情况下，需要继续判断 root1 的孩子节点是不是跟 root2 的孩子节点相当。因为可以做翻转操作，所以这里有两种情况需要去判断。

时间复杂度： O\(min\(N1,N2\)\)，其中 N1​，N2 分别是二叉树 root1，root2 的大小。

空间复杂度： O\(min\(H1,H2\)\)，其中 H1，H2​ 分别是二叉树 root1， root2 的高度。

2）标准态遍历

让树中所有节点的左孩子都小于右孩子，如果当前不满足就翻转。我们把这种状态的二叉树称为 标准态。所有等价二叉树在转换成标准态后都是完全一样的。

**算法**

用深度优先遍历来对比这两棵树在标准态下是否完全一致。对于两颗等价树，在标准态下遍历的结果一定是一样的。

### 代码

普通递归topdown

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
    public boolean flipEquiv(TreeNode root1, TreeNode root2) {
        if (root1 == root2)
            return true;
        if (root1 == null || root2 == null || root1.val != root2.val)
            return false;

        return (flipEquiv(root1.left, root2.left) && flipEquiv(root1.right, root2.right) ||
                flipEquiv(root1.left, root2.right) && flipEquiv(root1.right, root2.left));
    }
}

```

标准态

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
    public boolean flipEquiv(TreeNode root1, TreeNode root2) {
        List<Integer> vals1 = new ArrayList();
        List<Integer> vals2 = new ArrayList();
        dfs(root1, vals1);
        dfs(root2, vals2);
        return vals1.equals(vals2);
    }

    public void dfs(TreeNode node, List<Integer> vals) {
        if (node != null) {
            vals.add(node.val);
            int L = node.left != null ? node.left.val : -1;
            int R = node.right != null ? node.right.val : -1;

            if (L < R) {
                dfs(node.left, vals);
                dfs(node.right, vals);
            } else {
                dfs(node.right, vals);
                dfs(node.left, vals);
            }

            vals.add(null);
        }
    }
}
```

## 1376 Time Needed to Inform All Employees 

### 原题

公司里有 n 名员工，每个员工的 ID 都是独一无二的，编号从 0 到 n - 1。公司的总负责人通过 headID 进行标识。

在 manager 数组中，每个员工都有一个直属负责人，其中 manager\[i\] 是第 i 名员工的直属负责人。对于总负责人，manager\[headID\] = -1。题目保证从属关系可以用树结构显示。

公司总负责人想要向公司所有员工通告一条紧急消息。他将会首先通知他的直属下属们，然后由这些下属通知他们的下属，直到所有的员工都得知这条紧急消息。

第 i 名员工需要 informTime\[i\] 分钟来通知它的所有直属下属（也就是说在 informTime\[i\] 分钟后，他的所有直属下属都可以开始传播这一消息）。

返回通知所有员工这一紧急消息所需要的 分钟数 。

A company has `n` employees with a unique ID for each employee from `0` to `n - 1`. The head of the company has is the one with `headID`.

Each employee has one direct manager given in the `manager` array where `manager[i]` is the direct manager of the `i-th` employee, `manager[headID] = -1`. Also it's guaranteed that the subordination relationships have a tree structure.

The head of the company wants to inform all the employees of the company of an urgent piece of news. He will inform his direct subordinates and they will inform their subordinates and so on until all employees know about the urgent news.

The `i-th` employee needs `informTime[i]` minutes to inform all of his direct subordinates \(i.e After informTime\[i\] minutes, all his direct subordinates can start spreading the news\).

Return _the number of minutes_ needed to inform all the employees about the urgent news.

**Example 1:**

```text
Input: n = 1, headID = 0, manager = [-1], informTime = [0]
Output: 0
Explanation: The head of the company is the only employee in the company.
```

**Example 2:** ![](https://assets.leetcode.com/uploads/2020/02/27/graph.png)

```text
Input: n = 6, headID = 2, manager = [2,2,-1,2,2,2], informTime = [0,0,1,0,0,0]
Output: 1
Explanation: The head of the company with id = 2 is the direct manager of all the employees in the company and needs 1 minute to inform them all.
The tree structure of the employees in the company is shown.
```

**Example 3:** ![](https://assets.leetcode.com/uploads/2020/02/28/1730_example_3_5.PNG)

```text
Input: n = 7, headID = 6, manager = [1,2,3,4,5,6,-1], informTime = [0,6,5,4,3,2,1]
Output: 21
Explanation: The head has id = 6. He will inform employee with id = 5 in 1 minute.
The employee with id = 5 will inform the employee with id = 4 in 2 minutes.
The employee with id = 4 will inform the employee with id = 3 in 3 minutes.
The employee with id = 3 will inform the employee with id = 2 in 4 minutes.
The employee with id = 2 will inform the employee with id = 1 in 5 minutes.
The employee with id = 1 will inform the employee with id = 0 in 6 minutes.
Needed time = 1 + 2 + 3 + 4 + 5 + 6 = 21.
```

**Example 4:**

```text
Input: n = 15, headID = 0, manager = [-1,0,0,1,1,2,2,3,3,4,4,5,5,6,6], informTime = [1,1,1,1,1,1,1,0,0,0,0,0,0,0,0]
Output: 3
Explanation: The first minute the head will inform employees 1 and 2.
The second minute they will inform employees 3, 4, 5 and 6.
The third minute they will inform the rest of employees.
```

**Example 5:**

```text
Input: n = 4, headID = 2, manager = [3,3,-1,2], informTime = [0,0,162,914]
Output: 1076
```

**Constraints:**

* `1 <= n <= 10^5`
* `0 <= headID < n`
* `manager.length == n`
* `0 <= manager[i] < n`
* `manager[headID] == -1`
* `informTime.length == n`
* `0 <= informTime[i] <= 1000`
* `informTime[i] == 0` if employee `i` has no subordinates.
* It is **guaranteed** that all the employees can be informed.

### 思路

BFS或DFS自底向上。

### 代码

朴素BFS

```java
class Solution {
 
    //定义结点类，表示员工的id和被通知时的时间
    class Node{
        public int id;
        public int time;
        public Node(int i,int time){
            this.id = i;
            this.time = time;
        }
    }
    public int numOfMinutes(int n, int headID, int[] manager, int[] informTime) {
        int result=0;

        //HashMap索引为员工编号，值为存放下属id的List
        Map<Integer, List<Integer>> map = new HashMap<>();
        for(int i=0;i<n;i++) {
            List<Integer> ls = map.getOrDefault(manager[i],new ArrayList<Integer>());
            ls.add(i);
            map.put(manager[i],ls);
        }

        Queue<Node> q = new LinkedList<>();
        q.offer(new Node(headID,0));
        while(!q.isEmpty()) {
            Node node= q.poll();
            //遍历当前员工的HashMap，将下属的员工加入队列
            for(Integer i:map.getOrDefault(node.id,new ArrayList<>())){
                q.offer(new Node(i,node.time+informTime[node.id]));
            }
            Math.max(node.time,result);
        }
        return result;
    }
}
```

DFS Bottom up

```java
class Solution {
    public int numOfMinutes(int n, int headID, int[] manager, int[] informTime) {
        int[] arr=new int[n];
        int ans=0;
        // 从0开始遍历
        for(int i=0;i<n;i++){
            int index=i;
            int sum=0;
            if(informTime[index]==0)    //表明是叶子节点
                while((index=manager[index])!=-1){  //循环往上找，直到头结点
                    sum+=informTime[index];         //记录每次的和
                    if(sum<=arr[index]) break;      //如果某个叶结点走到这个结点时和小于之前一个叶结点走到这里的和，那么接下来不用走了，因为肯定比之前的小（why? 因为这个类似相交链表，他们后面一段路都是一样的。都是同一个manager）
                    arr[index]=sum;                 //否则就更新这个值
                    ans=Math.max(sum,ans);          //记录这个数组的最大值
                }
        }
        return ans;
    }
}
```

## 715 Range Module 

### 原题

Range模块，Range 模块是跟踪数字范围的模块。你的任务是以一种有效的方式设计和实现以下接口。

* addRange\(int left, int right\) 添加半开区间 \[left, right\)，跟踪该区间中的每个实数。添加与当前跟踪的数字部分重叠的区间时，应当添加在区间 \[left, right\) 中尚未跟踪的任何数字到该区间中。 
* queryRange\(int left, int right\) 只有在当前正在跟踪区间 \[left, right\) 中的每一个实数时，才返回 true。 
* removeRange\(int left, int right\) 停止跟踪区间 \[left, right\) 中当前正在跟踪的每个实数。

A Range Module is a module that tracks ranges of numbers. Your task is to design and implement the following interfaces in an efficient manner.

`addRange(int left, int right)` Adds the half-open interval `[left, right)`, tracking every real number in that interval. Adding an interval that partially overlaps with currently tracked numbers should add any numbers in the interval `[left, right)` that are not already tracked.

`queryRange(int left, int right)` Returns true if and only if every real number in the interval `[left, right)` is currently being tracked.

`removeRange(int left, int right)` Stops tracking every real number currently being tracked in the interval `[left, right)`.

**Example 1:**  


```text
addRange(10, 20): null
removeRange(14, 16): null
queryRange(10, 14): true (Every number in [10, 14) is being tracked)
queryRange(13, 15): false (Numbers like 14, 14.03, 14.17 in [13, 15) are not being tracked)
queryRange(16, 17): true (The number 16 in [16, 17) is still being tracked, despite the remove operation)
```

**Note:**

A half open interval `[left, right)` denotes all real numbers `left <= x < right`.

`0 < left < right < 10^9` in all calls to `addRange, queryRange, removeRange`.

The total number of calls to `addRange` in a single test case is at most `1000`.

The total number of calls to `queryRange` in a single test case is at most `5000`.

The total number of calls to `removeRange` in a single test case is at most `1000`.

### 思路

使用基于平衡树的集合（TreeSet）ranges 来维护这个数据结构。ranges 内部的区间按照右端点从小到大排序。

* addRange\(\)，removeRange\(\): 和 Python 的实现方法相同，我们遍历 ranges 里的所有区间，找到其中所有与 \[left,right\] 重合的区间。如果需要添加区间 \[left,right\]，就删除这些重合的区间，并将 \[left,right\]添加到 ranges 中。如果需要删除区间，就在删除这些重合的区间的同时记录下出现的新区间，并在删除操作结束后把这 0，1，2 个新区间添加到 ranges 中。
* queryRange\(\)：由于 ranges 是一颗平衡树，我们可以在对数时间复杂度内找出是否有一个区间包含 \[left,right\]。

### 代码

```java
class RangeModule {
    TreeSet<Interval> ranges;
    public RangeModule() {
        ranges = new TreeSet();
    }

    public void addRange(int left, int right) {
        Iterator<Interval> itr = ranges.tailSet(new Interval(0, left - 1)).iterator();
        while (itr.hasNext()) {
            Interval iv = itr.next();
            if (right < iv.left) break;
            left = Math.min(left, iv.left);
            right = Math.max(right, iv.right);
            itr.remove();
        }
        ranges.add(new Interval(left, right));
    }

    public boolean queryRange(int left, int right) {
        Interval iv = ranges.higher(new Interval(0, left));
        return (iv != null && iv.left <= left && right <= iv.right);
    }

    public void removeRange(int left, int right) {
        Iterator<Interval> itr = ranges.tailSet(new Interval(0, left)).iterator();
        ArrayList<Interval> todo = new ArrayList();
        while (itr.hasNext()) {
            Interval iv = itr.next();
            if (right < iv.left) break;
            if (iv.left < left) todo.add(new Interval(iv.left, left));
            if (right < iv.right) todo.add(new Interval(right, iv.right));
            itr.remove();
        }
        for (Interval iv: todo) ranges.add(iv);
    }
}

class Interval implements Comparable<Interval>{
    int left;
    int right;

    public Interval(int left, int right){
        this.left = left;
        this.right = right;
    }

    public int compareTo(Interval that){
        if (this.right == that.right) return this.left - that.left;
        return this.right - that.right;
    }
}

/**
 * Your RangeModule object will be instantiated and called as such:
 * RangeModule obj = new RangeModule();
 * obj.addRange(left,right);
 * boolean param_2 = obj.queryRange(left,right);
 * obj.removeRange(left,right);
 */
```

## 833 Find And Replace in String

### 原题

字符串中的查找和替换

对于某些字符串 S，我们将执行一些替换操作，用新的字母组替换原有的字母组（不一定大小相同）。

每个替换操作具有 3 个参数：起始索引 i，源字 x 和目标字 y。规则是如果 x 从原始字符串 S 中的位置 i 开始，那么我们将用 y 替换出现的 x。如果没有，我们什么都不做。

举个例子，如果我们有 S = “abcd” 并且我们有一些替换操作 i = 2，x = “cd”，y = “ffff”，那么因为 “cd” 从原始字符串 S 中的位置 2 开始，我们将用 “ffff” 替换它。

再来看 S = “abcd” 上的另一个例子，如果我们有替换操作 i = 0，x = “ab”，y = “eee”，以及另一个替换操作 i = 2，x = “ec”，y = “ffff”，那么第二个操作将不执行任何操作，因为原始字符串中 S\[2\] = 'c'，与 x\[0\] = 'e' 不匹配。

所有这些操作同时发生。保证在替换时不会有任何重叠： S = "abc", indexes = \[0, 1\], sources = \["ab","bc"\] 不是有效的测试用例。

To some string `S`, we will perform some replacement operations that replace groups of letters with new ones \(not necessarily the same size\).

Each replacement operation has `3` parameters: a starting index `i`, a source word `x` and a target word `y`.  The rule is that if `x` starts at position `i` in the **original** **string** **`S`**, then we will replace that occurrence of `x` with `y`.  If not, we do nothing.

For example, if we have `S = "abcd"` and we have some replacement operation `i = 2, x = "cd", y = "ffff"`, then because `"cd"` starts at position `2` in the original string `S`, we will replace it with `"ffff"`.

Using another example on `S = "abcd"`, if we have both the replacement operation `i = 0, x = "ab", y = "eee"`, as well as another replacement operation `i = 2, x = "ec", y = "ffff"`, this second operation does nothing because in the original string `S[2] = 'c'`, which doesn't match `x[0] = 'e'`.

All these operations occur simultaneously.  It's guaranteed that there won't be any overlap in replacement: for example, `S = "abc", indexes = [0, 1], sources = ["ab","bc"]` is not a valid test case.

**Example 1:**

```text
Input: S = "abcd", indexes = [0,2], sources = ["a","cd"], targets = ["eee","ffff"]
Output: "eeebffff"
Explanation: "a" starts at index 0 in S, so it's replaced by "eee".
"cd" starts at index 2 in S, so it's replaced by "ffff".
```

**Example 2:**

```text
Input: S = "abcd", indexes = [0,2], sources = ["ab","ec"], targets = ["eee","ffff"]
Output: "eeecd"
Explanation: "ab" starts at index 0 in S, so it's replaced by "eee". 
"ec" doesn't starts at index 2 in the original S, so we do nothing.
```

Notes:

1. `0 <= indexes.length = sources.length = targets.length <= 100`
2. `0 < indexes[i] < S.length <= 1000`
3. All characters in given inputs are lowercase letters.

### 思路

直接模拟过程。

在 Java 的代码中，我们根据替换操作得到数组 match，其中 match\[ix\] = j 表示字符串 S 从第 ix 位开始和 sources\[j\] 匹配，并且会被替换成 target\[j\]，也就是说 sources\[j\] 是字符串 S\[ix:\] 的前缀。在得到 match 数组后，我们对 S 从左到右进行扫描，对于每个位置 ix，如果 match\[ix\] 有值 j，那么在 ans 尾部添加字符串 targets\[j\]，并将 ix 增加 sources\[j\].length\(\)；否则在 ans 尾部添加字符 S\[ix\]，并将 ix 增加 1。

时间复杂度：O\(NQ\)，其中 NN 是字符串 S 的长度，Q 是替换操作的数量。

空间复杂度：O\(N\)，我们认为 sources 和 targets 中的字符串长度均为常数。

### 代码

```java
class Solution {
    public String findReplaceString(String S, int[] indexes, String[] sources, String[] targets) {
        int N = S.length();
        int[] match = new int[N];
        Arrays.fill(match, -1);

        for (int i = 0; i < indexes.length; ++i) {
            int ix = indexes[i];
            if (S.substring(ix, ix + sources[i].length()).equals(sources[i]))
                match[ix] = i;
        }

        StringBuilder ans = new StringBuilder();
        int ix = 0;
        while (ix < N) {
            if (match[ix] >= 0) {
                ans.append(targets[match[ix]]);
                ix += sources[match[ix]].length();
            } else {
                ans.append(S.charAt(ix++));
            }
        }
        return ans.toString();
    }
}

```

## 652 Find Duplicate Subtrees

### 原题

Given the `root` of a binary tree, return all **duplicate subtrees**.

For each kind of duplicate subtrees, you only need to return the root node of any **one** of them.

Two trees are **duplicate** if they have the **same structure** with the **same node values**.

**Example 1:**![](https://assets.leetcode.com/uploads/2020/08/16/e1.jpg)

```text
Input: root = [1,2,3,4,null,2,4,null,null,4]
Output: [[2,4],[4]]
```

**Example 2:**![](https://assets.leetcode.com/uploads/2020/08/16/e2.jpg)

```text
Input: root = [2,1,1]
Output: [[1]]
```

**Example 3:**![](https://assets.leetcode.com/uploads/2020/08/16/e33.jpg)

```text
Input: root = [2,2,2,3,null,3,null]
Output: [[2,3],[3]]
```

**Constraints:**

* The number of the nodes in the tree will be in the range `[1, 10^4]`
* `-200 <= Node.val <= 200`

### 思路

![](../.gitbook/assets/image%20%28165%29.png)

![](../.gitbook/assets/image%20%28151%29.png)

![](../.gitbook/assets/image%20%28177%29.png)

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
import java.util.LinkedList;
class Solution {
    public LinkedList<TreeNode> result = new LinkedList<>();
    public HashMap<String,Integer> map = new HashMap<>();
    public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
        if(root == null){
            return result;
        }
        serialize(root);
        return result;
    }

    public String serialize(TreeNode root){
        if(root == null){
            return "null";
        }
        //序列化以当前节点为根节点的二叉树
        String str = serialize(root.left) + ","+ root.val + ","+ serialize(root.right);
        //使用一个HashMap来记录所有的子树的序列
        map.put(str,map.getOrDefault(str,0)+1);
        //当其value为2时，则表示出现了重复结构，将这个子树的根节点放入到结果当中。
        if(map.get(str) == 2){
            result.add(root);
        }
        return str;
    }
}
```

## 846 Hand of Straights

[https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table\#846-hand-of-straights](https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table#846-hand-of-straights)

## 1296 Divide Array in Sets of K Consecutive Numbers

### 原题

Given an array of integers `nums` and a positive integer `k`, find whether it's possible to divide this array into sets of `k` consecutive numbers  
Return `True` if its possible ****otherwise return `False`.

**Example 1:**

```text
Input: nums = [1,2,3,3,4,4,5,6], k = 4
Output: true
Explanation: Array can be divided into [1,2,3,4] and [3,4,5,6].
```

**Example 2:**

```text
Input: nums = [3,2,1,2,3,4,3,4,5,9,10,11], k = 3
Output: true
Explanation: Array can be divided into [1,2,3] , [2,3,4] , [3,4,5] and [9,10,11].
```

**Example 3:**

```text
Input: nums = [3,3,2,2,1,1], k = 3
Output: true
```

**Example 4:**

```text
Input: nums = [1,2,3,4], k = 3
Output: false
Explanation: Each array should be divided in subarrays of size 3.
```

**Constraints:**

* `1 <= nums.length <= 10^5`
* `1 <= nums[i] <= 10^9`
* `1 <= k <= nums.length`

**Note:** This question is the same as 846: [https://leetcode.com/problems/hand-of-straights/](https://leetcode.com/problems/hand-of-straights/)

### 思路

解法同846 Hand of Straights。

1. 如果数组个数len不是k的倍数，说明不可能划分； 
2. 拿一个Map统计每个元素出现的次数，然后从小到大遍历数组，每次拿第一个count不能0（遍历过程中，若元素被归到前面集合，count要更新的）的元素为起点，看看后面是否能构成k个连续的集合。若一发现不能构成k个连续集合，就立马reture false。 
3. 为了更加快速的结束，可以定义一个集合数，总的需要划分的集合数为 len / klen/k，如果遍历过程中的集合数达到这个总的集合数，就可以立马结束。

### 代码

```java
class Solution {
 public boolean isPossibleDivide(int[] nums, int k) {
        int len = nums.length;

        if (len % k != 0) {
            return false;
        }

        Arrays.sort(nums);
        Map<Integer, Integer> countMap = new HashMap<>();
        for (int num : nums) {
            countMap.put(num, countMap.getOrDefault(num, 0) + 1);
        }

        int needKSetCount = len / k;  // 需要的集合数（每个集合个数为k）
        int kCount = 0;   // 以下遍历过程集合数
        for (int num: nums) {
            int curNumCount = countMap.get(num);
            if (curNumCount == 0) {
                // 等于0说明被归到前面集合中了
                continue;
            }

            countMap.put(num, curNumCount - 1);
            for (int i = 1; i < k; i++) {
                int count = countMap.getOrDefault(num + i, 0);
                if (count == 0) {
                    // 等于0就说明以当前num为起点，找不到k个大小的连续集合
                    return false;
                }

                countMap.put(num + i, count - 1);
            }

            kCount++;
            // 当集合数已经达到需要的集合数，说明已经成功了
            if (kCount == needKSetCount) {
                return true;
            }
        }

        return true;
    }
}
```

## 1548 The Most Similar Path in a Graph 

### 原题

We have `n` cities and `m` bi-directional `roads` where `roads[i] = [ai, bi]` connects city `ai` with city `bi`. Each city has a name consisting of exactly 3 upper-case English letters given in the string array `names`. Starting at any city `x`, you can reach any city `y` where `y != x` \(i.e. the cities and the roads are forming an undirected connected graph\).

You will be given a string array `targetPath`. You should find a path in the graph of the **same length** and with the **minimum edit distance** to `targetPath`.

You need to return _the order of the nodes in the path with the minimum edit distance_, The path should be of the same length of `targetPath` and should be valid \(i.e. there should be a direct road between `ans[i]` and `ans[i + 1]`\). If there are multiple answers return any one of them.

The **edit distance** is defined as follows:

![](https://assets.leetcode.com/uploads/2020/08/08/edit.jpg)

**Follow-up:** If each node can be visited only once in the path, What should you change in your solution?

**Example 1:**![](https://assets.leetcode.com/uploads/2020/08/08/e1.jpg)

```text
Input: n = 5, roads = [[0,2],[0,3],[1,2],[1,3],[1,4],[2,4]], names = ["ATL","PEK","LAX","DXB","HND"], targetPath = ["ATL","DXB","HND","LAX"]
Output: [0,2,4,2]
Explanation: [0,2,4,2], [0,3,0,2] and [0,3,1,2] are accepted answers.
[0,2,4,2] is equivalent to ["ATL","LAX","HND","LAX"] which has edit distance = 1 with targetPath.
[0,3,0,2] is equivalent to ["ATL","DXB","ATL","LAX"] which has edit distance = 1 with targetPath.
[0,3,1,2] is equivalent to ["ATL","DXB","PEK","LAX"] which has edit distance = 1 with targetPath.
```

**Example 2:**![](https://assets.leetcode.com/uploads/2020/08/08/e2.jpg)

```text
Input: n = 4, roads = [[1,0],[2,0],[3,0],[2,1],[3,1],[3,2]], names = ["ATL","PEK","LAX","DXB"], targetPath = ["ABC","DEF","GHI","JKL","MNO","PQR","STU","VWX"]
Output: [0,1,0,1,0,1,0,1]
Explanation: Any path in this graph has edit distance = 8 with targetPath.
```

**Example 3:**

![](https://assets.leetcode.com/uploads/2020/08/09/e3.jpg)

```text
Input: n = 6, roads = [[0,1],[1,2],[2,3],[3,4],[4,5]], names = ["ATL","PEK","LAX","ATL","DXB","HND"], targetPath = ["ATL","DXB","HND","DXB","ATL","LAX","PEK"]
Output: [3,4,5,4,3,2,1]
Explanation: [3,4,5,4,3,2,1] is the only path with edit distance = 0 with targetPath.
It's equivalent to ["ATL","DXB","HND","DXB","ATL","LAX","PEK"]
```

**Constraints:**

* `2 <= n <= 100`
* `m == roads.length`
* `n - 1 <= m <= (n * (n - 1) / 2)`
* `0 <= ai, bi <= n - 1`
* `ai != bi` 
* The graph is guaranteed to be **connected** and each pair of nodes may have **at most one** direct road.
* `names.length == n`
* `names[i].length == 3`
* `names[i]` consists of upper-case English letters.
* There can be two cities with **the same** name.
* `1 <= targetPath.length <= 100`
* `targetPath[i].length == 3`
* `targetPath[i]` consists of upper-case English letters.

### 思路

DFS + memoization

### 代码

```java
class Solution {
    List<List<Integer>> adjMatrix;
    String[] names;
    String[] targetPath;
    int[][] visited;
    int[][] nextChoiceForMin;
    public List<Integer> mostSimilar(int n, int[][] roads, String[] names, String[] targetPath) {
        // INITIALIZE VARIABLES
        this.visited = new int[names.length][targetPath.length];
        this.nextChoiceForMin = new int[names.length][targetPath.length];
        this.targetPath = targetPath;
        this.names = names;
        this.adjMatrix = new ArrayList<List<Integer>>();
       
        for (int[] x : visited) Arrays.fill(x, -1);
        
        // BUILD ADJACENCY MATRIX
        for (int i = 0;i < n; i++) adjMatrix.add(new ArrayList<Integer>());
        for (int[] road : roads) {
            adjMatrix.get(road[0]).add(road[1]);
            adjMatrix.get(road[1]).add(road[0]);
        }
        
        // FROM EACH NODE, CALCULATE MIN COST AND THE CITY THAT GAVE THE MIN COST
        int min = Integer.MAX_VALUE;
        int start = 0;
        for (int i = 0;i < names.length; i++) {
            int resp = dfs(i, 0);
            if (resp < min) {
                min = resp;
                start = i;
            }
        }
        
        // BUILD THE ANSWER BASED ON WHATS THE BEST NEXT CHOICE 
        List<Integer> ans = new ArrayList<Integer>();
        while (ans.size() < targetPath.length) {
            ans.add(start);
            start = nextChoiceForMin[start][ans.size()-1];
        }
        return ans;
    }
    
    public int dfs(int namesIdx, int currPathIdx) {
        // IF WE VISITED IT ALREADY, RETURN THE PREVIOUS COUNT
        if (visited[namesIdx][currPathIdx] != -1) return visited[namesIdx][currPathIdx];
        
        // IF ITS DIFFERENT, ADD 1 ELSE ADD 0.
        int editDist = (names[namesIdx].equals(targetPath[currPathIdx])) ? 0 : 1;
        
        // IF WE FILLED UP THE PATH, WE'RE DONE
        if (currPathIdx == targetPath.length-1) return editDist;
        
        // FROM EACH NEIGHBOR, CALCULATE MIN COST AND SAVE THE CITY THAT GAVE THE MIN COST  
        int min = Integer.MAX_VALUE;
        for (int neighbor : adjMatrix.get(namesIdx)) {
            int neighborCost = dfs(neighbor, currPathIdx+1);
            if (neighborCost < min) {
                min = neighborCost;
                nextChoiceForMin[namesIdx][currPathIdx] = neighbor; // HERE IS WHERE WE SAVE
            }
        }
        
        
        editDist += min; // UPDATE OUR EDIT DISTANCE
        visited[namesIdx][currPathIdx] = editDist; // SAVE OUR EDIT DISTANCE
        return editDist; // RETURN OUR EDIT DISTANCE
    }
}    

/*
                0.     1.   2.   3.   4
  names         [ATL, PEK, LAX, DXB, HND]
    
                           0.      1.         2.         3
   targetPath   ["ATL","DXB","HND","LAX"]
    
    DO THIS STARTING AT EVERY NODE
    ATL at idx = 0 is it different? no, so add to cost + 0 = 0
        3
        DXB at idx = 1 is it different? no, so add to cost + 0 = 0 
            ATL at idx = 2 is it different? yes, so add to cost + 1 = 1
                DXB at idx = 3 is it different? yes, so add to cost + 1 AND RETURN = 2
                LAX at idx = 3 is it different? no, so add to cost + 0 AND RETURN = 1
            PEK at idx = 2 is it different? yes, so add to cost + 1 = 1
                DXB at idx = 3 SOLVED ALREADY RETURN PREV   RETURN 
                LAX at idx = 3 SOLVED ALREADY RETURN PREV
                HND at idx = 3 is it different? yes, so add to cost + 1 AND RETURN
        LAX at idx = 1 is it different? yes, so add to cost + 1
            ATL at idx = 2, SOLVED ALREADY RETURN PREV
            PEK at idx = 2, SOLVED ALREADY RETURN PREV
            HND at idx = 2 is it different? no, so addto cost + 0
                PEK at idx = 3 is it diffferent? yes, so add to cost + 
                LAX at idx = 3 SOLVED ALREADY RETURN PREV
    */
```

## 


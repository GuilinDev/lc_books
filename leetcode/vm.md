# VM

## 146 LRU Cache 

[https://app.gitbook.com/@guilindev/s/interview/~/drafts/-MkF3RlZmVCDwo1nRFTf/leetcode/gao-ji-de-shu-ju-jie-gou-he-suan-fa\#146-lru-cache](https://app.gitbook.com/@guilindev/s/interview/~/drafts/-MkF3RlZmVCDwo1nRFTf/leetcode/gao-ji-de-shu-ju-jie-gou-he-suan-fa#146-lru-cache)

## 255 Verify Preorder Sequence in Binary Search Tree 

给一个数组，验证里面的数字是否符合BST的前序遍历顺序

正常O\(n\)解法

```java
class Solution {
    public boolean verifyPreorder(int[] preorder) {
        int low = Integer.MIN_VALUE;
        Stack<Integer> path = new Stack();
        for (int p : preorder) {
            if (p < low) {
                return false;
            }
            while (!path.empty() && p > path.peek()) {
                low = path.pop();
            }
            path.push(p);
        }
        return true;
    }
}
```

利用给定的参数的array，来存中间结果，O\(1\)解法

```java
class Solution {
    public boolean verifyPreorder(int[] preorder) {
        int low = Integer.MIN_VALUE, i = -1;
        for (int p : preorder) {
            if (p < low)
                return false;
            while (i >= 0 && p > preorder[i])
                low = preorder[i--];
            preorder[++i] = p;
        }
        return true;
    }
}
```

## 460 LFU Cache

[https://app.gitbook.com/@guilindev/s/interview/leetcode/gao-ji-de-shu-ju-jie-gou-he-suan-fa\#460-lfu](https://app.gitbook.com/@guilindev/s/interview/leetcode/gao-ji-de-shu-ju-jie-gou-he-suan-fa#460-lfu)

## 49 Group Anagrams

[https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#49-group-anagrams](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#49-group-anagrams)

## 56 Merge Intervals

[https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou\#56-merge-intervals](https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou#56-merge-intervals)

## 1328 Break a Palindrome

给一个回文串，replace其中一个character，使其不为回文串且 **lexicographically** 最小，贪心算法O\(n\) O\(n\)，从左到右，替换任何非a的字母变成a，如果只有一个字母，变为空串，否则把最后一个字母替换为b\(全a的情况\)。

```java
class Solution {
    public String breakPalindrome(String palindrome) {
        char[] s = palindrome.toCharArray();
        int n = s.length;
        
        for(int i = 0; i < n/2; i++) { //回文串只需遍历一半
            if(s[i] != 'a') {
                s[i] = 'a';
                return String.valueOf(s);
            }
        }
        s[n-1] = 'b'; 
        return n < 2 ? "" : String.valueOf(s);
    }
}
```

## 697 Degree of an Array

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array\#697-degree-of-an-array](https://app.gitbook.com/@guilindev/s/interview/leetcode/array#697-degree-of-an-array)

## 20 Valid Parentheses

[https://app.gitbook.com/@guilindev/s/interview/leetcode/stack\#20-valid-parentheses](https://app.gitbook.com/@guilindev/s/interview/leetcode/stack#20-valid-parentheses)

## 1228 Missing Number In Arithmetic Progression 

等差数列的数组，找出缺失的那个

正常O\(n\)用公式

```java
public int missingNumber(int[] A) {
    int first = A[0], last = A[0], sum = 0, n = A.length;
    for (int a : A) {
        first = Math.min(first, a);
        last = Math.max(last, a);
        sum += a;
    }
    return (first + last) * (n + 1) / 2 - sum;
}
```

二分搜索O\(log\(n\)\)

```java
public int missingNumber(int[] A) {
    int n = A.length, d = (A[n - 1] - A[0]) / n, left = 0, right = n;
    while (left < right) {
        int mid = (left + right) / 2;
        if (A[mid] == A[0] + d * mid)
            left = mid + 1;
        else
            right = mid;
    }
    return A[0] + d * left;
}
```

## 564 Find the Closest Palindrome

给一个字符串形式的数字，找离这个数字最近的回文数字，以回文数12321为例，下一个较大的回文应该是12421，下一个较小的应该是12221。

也就是说，如果我们将 123 定义为 12321 的 palindromeRoot，则下一个较大的回文数的 palindromeRoot 是palindromeRoot + 1（124），而下一个较小的回文数的 palindromeRoot 是 palindromeRoot - 1（122）。

对于偶数位的回文数，例如 123321，palindromeRoot 的变化遵循相同的模式。

最接近的回文应该是这 2 个回文根形成的回文数中的一个。

对于不是回文的数字，例如 12345，我们仍然关注数字的前半部分，即示例中的 palindromeRoot 为 123。除了 palindromeRoot + 1\(124\) 形成的较大的一个，palindromeRoot - 1\(122\) 形成的较小的一个之外，应该还有一种可能，即 palindromeRoot \(123\) 形成的数。我们选择了这 3 个回文数中最接近的一个。

特殊情况， case 1. &lt;= 10, OR 等于 100, 1000, 10000, ... 我们只需将 n 减 1。 case 2. 11, 101, 1001, 10001, 100001, ... 我们只需将 n 减 2。 case 3. 99, 999, 9999, 99999, ... 我们只需将 n 增加 2。

```java
public String nearestPalindromic(String n) {
        long nl = Long.parseLong(n);
        int len = n.length();
        
        //
        // Corner cases
        //
        
        // <= 10 or equal to 100, 1000, 10000, ... 
        if (nl <= 10 || (nl % 10 == 0 
                         && Long.parseLong(n.substring(1)) == 0)) {
            return "" + (nl - 1);
        }
        
        // 11 or 101, 1001, 10001, 100001, ... 
        if (nl == 11 || (nl % 10 == 1 
                         && n.charAt(0) == '1' 
                         && Long.parseLong(n.substring(1, len - 1)) == 0)) {
            return "" + (nl - 2);
        }
        
        // 99, 999, 9999, 99999, ...  
        if (isAllDigitNine(n)) {
            return "" + (nl + 2);
        }
        
        //
        // Construct the closest palindrome and calculate absolute difference with n
        //
        boolean isEvenDigits = len % 2 == 0;
        
        String palindromeRootStr
            = (isEvenDigits) ? n.substring(0, len / 2) : n.substring(0, len / 2 + 1);
        
        int palindromeRoot = Integer.valueOf(palindromeRootStr); 
        long equal = toPalindromeDigits("" + palindromeRoot, isEvenDigits);
        long diffEqual = Math.abs(nl - equal);
            
        long bigger = toPalindromeDigits("" + (palindromeRoot + 1), isEvenDigits);
        long diffBigger = Math.abs(nl - bigger);
        
        long smaller = toPalindromeDigits("" + (palindromeRoot - 1), isEvenDigits);
        long diffSmaller = Math.abs(nl - smaller);
         
        //
        // Find the palindrome with minimum absolute differences
        // If tie, return the smaller one
        //
        long closest = (diffBigger < diffSmaller) ? bigger : smaller;
        long minDiff = Math.min(diffBigger, diffSmaller);
        
        if (diffEqual != 0) { // n is not a palindrome, diffEqual should be considered
            if (diffEqual == minDiff) { // if tie
                closest = Math.min(equal, closest);
            } else if (diffEqual < minDiff){
                closest = equal;
            }
        }
        
        return "" + closest;
    }
    
    private long toPalindromeDigits(String num, boolean isEvenDigits) {
        StringBuilder reversedNum = new StringBuilder(num).reverse();
        String palindromeDigits = isEvenDigits
            ? num + reversedNum.toString()
            : num + (reversedNum.deleteCharAt(0)).toString();
        return Long.parseLong(palindromeDigits);
    }
    
    private boolean isAllDigitNine(String n) {
        for (char ch : n.toCharArray()) {
            if (ch != '9') {
                return false;
            }
        }
        return true;
    }
```

## 93 Restore IP Addresses

[https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking\#93-restore-ip-addresses](https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking#93-restore-ip-addresses)

##  33 Search in Rotated Sorted Array 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zai-array-zhong-cha-xun-yuan-su\#33-search-in-rotated-sorted-array](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zai-array-zhong-cha-xun-yuan-su#33-search-in-rotated-sorted-array)

## 138 Copy List with Random Pointer 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist\#138-copy-list-with-random-pointer](https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist#138-copy-list-with-random-pointer)

## 295 Find Median from Data Stream

[https://app.gitbook.com/@guilindev/s/interview/leetcode/heap\#295-find-median-from-data-stream](https://app.gitbook.com/@guilindev/s/interview/leetcode/heap#295-find-median-from-data-stream)

## 1048 Longest String Chain 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/tag0\#1048-longest-string-chain](https://app.gitbook.com/@guilindev/s/interview/leetcode/tag0#1048-longest-string-chain)

## 1451 Rearrange Words in a Sentence 

给定一个句子文本（一个句子是一串空格分隔的单词），格式如下：

第一个字母大写。 文本中的每个单词都由一个空格分隔。 您的任务是重新排列文本中的单词，以便所有单词按长度递增的顺序重新排列。如果两个单词的长度相同，则按原来的顺序排列它们。返回新文本。

Native Sort

```java
public String arrangeWords(String text) {
    String []s = text.toLowerCase().split(" ");
    Arrays.sort(s, (a,b) ->  a.length() - b.length());
    String ans = String.join(" ", s);
    return Character.toUpperCase(ans.charAt(0)) + ans.substring(1);
}
```

Custom Sort

```java
public static String arrangeWords(String text) {
    String[] s = text.split(" ");
    Arrays.sort(s, (s1, s2) -> {
      return s1.length() - s2.length();
    });
    StringBuilder v = new StringBuilder();
    for (String value : s) {
      v.append(" ").append(value.toLowerCase());
    }
    char[] ans = v.substring(1).toCharArray();// remove space from front
    ans[0] = (char) (ans[0] + 'A' - 'a');//change uppercase
    return new String(ans);
}
```

TreeMap

```java
public String arrangeWords(String text) {
    TreeMap<Integer, StringBuilder> map=new TreeMap<>();
    String[] strs=text.split(" ");
    for(int i=0;i<strs.length;i++) {
        StringBuilder tmp=map.getOrDefault(strs[i].length(), new StringBuilder());
        tmp.append(strs[i]).append(" ");
        map.put(strs[i].length(), tmp);
    }
    StringBuilder sb = new StringBuilder();
    for(int k : map.keySet()) {
        StringBuilder tmp=map.get(k);
        if(tmp.charAt(0)<'a') tmp.setCharAt(0, (char)('a'+tmp.charAt(0) -'A'));
        sb.append(tmp);
    }
    if(sb.charAt(0) > 'Z') sb.setCharAt(0, (char)('A' + sb.charAt(0) - 'a'));
    return sb.deleteCharAt(sb.length()-1).toString();
}
```

## 85 Maximal Rectangle 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array/array-zhong-de-shu-zi-zu-cheng-san-jiao-xing\#85-maximum-rectangle](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/array-zhong-de-shu-zi-zu-cheng-san-jiao-xing#85-maximum-rectangle)

## 609 Find Duplicate File in System

[https://app.gitbook.com/@guilindev/s/interview/leetcode/tag\#609-find-duplicate-file-in-system](https://app.gitbook.com/@guilindev/s/interview/leetcode/tag#609-find-duplicate-file-in-system)

## 221 Maximal Square 

{% embed url="https://leetcode-cn.com/problems/maximal-square/solution/zui-da-zheng-fang-xing-by-leetcode-solution/" %}

```java
class Solution {
    public int maximalSquare(char[][] matrix) {
        int maxSide = 0;
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return maxSide;
        }
        int rows = matrix.length, columns = matrix[0].length;
        int[][] dp = new int[rows][columns];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < columns; j++) {
                if (matrix[i][j] == '1') {
                    if (i == 0 || j == 0) {
                        dp[i][j] = 1;
                    } else {
                        dp[i][j] = Math.min(Math.min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
                    }
                    maxSide = Math.max(maxSide, dp[i][j]);
                }
            }
        }
        int maxSquare = maxSide * maxSide;
        return maxSquare;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/maximal-square/solution/zui-da-zheng-fang-xing-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 4 Median of Two Sorted Arrays

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array\#4-median-of-two-sorted-arrays](https://app.gitbook.com/@guilindev/s/interview/leetcode/array#4-median-of-two-sorted-arrays)

## 1 Two Sum

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array/ksum\#1-2sum](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/ksum#1-2sum)

## 1192 Critical Connections in a Network

有编号从 0 到 n - 1 的 n 个服务器通过无向服务器到服务器连接连接形成一个网络，其中 connection\[i\] = \[ai, bi\] 表示服务器 ai 和 bi 之间的连接。任何服务器都可以通过网络直接或间接到达其他服务器。

关键连接是一种连接，如果删除，将使某些服务器无法访问其他服务器。以任何顺序返回网络中的所有关键连接。

Java DFS -&gt; Tarjan

```java
class Solution {
    int T = 1;
    public List<List<Integer>> criticalConnections(int n, List<List<Integer>> connections) {
        // use a timestamp, for each node, check the samllest timestamp that can reach from the node
        // construct the graph first
        List[] graph = new ArrayList[n];
        for (int i = 0; i < n; i++) {
            graph[i] = new ArrayList<Integer>();
        }
        for (List<Integer> conn : connections) {
            graph[conn.get(0)].add(conn.get(1));
            graph[conn.get(1)].add(conn.get(0));
        }
        
        int[] timestamp = new int[n]; // an array to save the timestamp that we meet a certain node
        
        // for each node, we need to run dfs for it, and return the smallest timestamp in all its children except its parent
        List<List<Integer>> criticalConns = new ArrayList<>();
        dfs(n, graph, timestamp, 0, -1, criticalConns);
        return criticalConns;
    }
    
    // return the minimum timestamp it ever visited in all the neighbors
    private int dfs(int n, List[] graph, int[] timestamp, int i, int parent, List<List<Integer>> criticalConns) {
        if (timestamp[i] != 0) return timestamp[i];
        timestamp[i] = T++;

        int minTimestamp = Integer.MAX_VALUE;
        for (int neighbor : (List<Integer>) graph[i]) {
            if (neighbor == parent) continue; // no need to check the parent
            int neighborTimestamp = dfs(n, graph, timestamp, neighbor, i, criticalConns);
            minTimestamp = Math.min(minTimestamp, neighborTimestamp);
        }
        
        if (minTimestamp >= timestamp[i]) {
            if (parent >= 0) criticalConns.add(Arrays.asList(parent, i));
        }
        return Math.min(timestamp[i], minTimestamp);
    }
}
```

## 706 Design HashMap 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedin\#706-design-hashmap-62-3-easy](https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedin#706-design-hashmap-62-3-easy)

## 97 Interleaving String 

![](../.gitbook/assets/image%20%28195%29.png)

{% embed url="https://leetcode-cn.com/problems/interleaving-string/solution/jiao-cuo-zi-fu-chuan-by-leetcode-solution/" %}

## 76 Minimum Window Substring

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-2\#76-minimum-window-substring](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-2#76-minimum-window-substring)

## 621 Task Scheduler 

[https://app.gitbook.com/@guilindev/s/interview/~/drafts/-MkFApqjA3myahwE70yz/leetcode/queue\#621-task-scheduler](https://app.gitbook.com/@guilindev/s/interview/~/drafts/-MkFApqjA3myahwE70yz/leetcode/queue#621-task-scheduler)

## 277 Find the Celebrity 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/graph\#277-find-the-celebrity](https://app.gitbook.com/@guilindev/s/interview/leetcode/graph#277-find-the-celebrity)

## 412 Fizz Buzz

[https://leetcode-cn.com/problems/fizz-buzz/solution/fizz-buzz-by-leetcode/](https://leetcode-cn.com/problems/fizz-buzz/solution/fizz-buzz-by-leetcode/)

## 2 Add Two Numbers 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist\#2-add-two-numbers](https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist#2-add-two-numbers)

## 863 All Nodes Distance K in Binary Tree 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#863-all-nodes-distance-k-in-binary-tree](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#863-all-nodes-distance-k-in-binary-tree)

## 300 Longest Increasing Subsequence 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#300-longest-increasing-subsequence](https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer#300-longest-increasing-subsequence)

## 23 Merge k Sorted Lists

[https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist\#23-merge-k-sorted-lists](https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist#23-merge-k-sorted-lists)

## 121 Best Time to Buy and Sell Stock

[https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#121-best-time-to-buy-and-sell-stock](https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer#121-best-time-to-buy-and-sell-stock)

## 322 Coin Change

[https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#322-coin-change](https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer#322-coin-change)

## 98 Validate Binary Search Tree

[https://app.gitbook.com/@guilindev/s/interview/leetcode/dfs\#98-validate-binary-search-tree](https://app.gitbook.com/@guilindev/s/interview/leetcode/dfs#98-validate-binary-search-tree)

## 1010 Pairs of Songs With Total Durations Divisible by 60 

每首歌的时间为描述，返回所有的两首歌的总时间能被60整除的pair， **Two Sum with K = 60**

\(60 - t % 60\) % 60 表达式的原因：如果HashMap已经有 30，我们需要寻找这个数字是否有余数，这可以用 60 - t%60 来实现。例如，如果数字是 210。60 - 210 % 60 返回 30。30 已经在列表中，这可以配对形成 \(30,210\)。

超过 % 60 \(60 - t % 60\) 的原因。如果 t = 60，则表达式 60 - t % 60 返回 60。对于 60，这超出了我们的剩余范围 \(0,59\)。为了使其在 60 和 60 的倍数的情况下处于范围内，我们正在 ufing在 \(60 - t % 60\) 之上额外增加 %60。这使得余数为 0。这在 60\(0,59\) 的余数范围内

map.put\(t % 60, map.getOrDefault\(t % 60, 0\) + 1\) 的说明； 每次访问一个新号码时，先添加然后递增。例如：如果我有 30,150,90 当我读 30 HashMap有 30, 1 ... cntr\(counter\) 是 0 当我读到 150 时，counter increments\(30,150\) 映射有 30,2 ... cntr\(counter\) 是 1The map 应该有 2，推理当我读到 90 时，这可以形成 2 对 \(90,30\),90,150。 这使得计数器将增加 2 ..... cntr = 1+2 == 3

```java
class Solution {
    public int numPairsDivisibleBy60(int[] time) {
        Map<Integer, Integer> map = new HashMap<>();
        int cntr = 0;
        for (int t : time) {
            cntr += map.getOrDefault((60 - t % 60) % 60, 0);
            map.put(t % 60, map.getOrDefault(t % 60, 0) + 1);
        }
        return cntr;
    }
}
```

## 15 3 Sum 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array/ksum\#15-3sum](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/ksum#15-3sum)

## 1046 Last Stone Weight

有一堆石头，每块石头的重量都是正整数。每一回合，从中选出两块 最重的 石头，然后将它们一起粉碎。假设石头的重量分别为 x 和 y，且 x &lt;= y。那么粉碎的可能结果如下：

如果 x == y，那么两块石头都会被完全粉碎； 如果 x != y，那么重量为 x 的石头将会完全粉碎，而重量为 y 的石头新重量为 y-x。 最后，最多只会剩下一块石头。返回此石头的重量。如果没有石头剩下，就返回 0。

输入：\[2,7,4,1,8,1\] 输出：1 解释： 先选出 7 和 8，得到 1，所以数组转换为 \[2,4,1,1,1\]， 再选出 2 和 4，得到 2，所以数组转换为 \[2,1,1,1\]， 接着是 2 和 1，得到 1，所以数组转换为 \[1,1,1\]， 最后选出 1 和 1，得到 0，最终数组转换为 \[1\]，这就是最后剩下那块石头的重量。

提示：

1 &lt;= stones.length &lt;= 30 1 &lt;= stones\[i\] &lt;= 1000

```java
class Solution {
    public int lastStoneWeight(int[] stones) {
        PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
        for (int stone : stones) {
            pq.offer(stone);
        }
        while (pq.size() > 1) {
            int temp = pq.poll() - pq.poll();
            pq.offer(temp);
        }
        return pq.poll();
    }
}
```

## 238 Product of Array Except Self 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array\#238-product-of-array-except-self](https://app.gitbook.com/@guilindev/s/interview/leetcode/array#238-product-of-array-except-self)

## 151 Reverse Words in a String

[https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#151-reverse-words-in-a-string](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#151-reverse-words-in-a-string)

## 297 Serialize and Deserialize Binary Tree

[https://app.gitbook.com/@guilindev/s/interview/~/drafts/-MkFApqjA3myahwE70yz/leetcode/untitled-1\#297-serialize-and-deserialize-binary-tree](https://app.gitbook.com/@guilindev/s/interview/~/drafts/-MkFApqjA3myahwE70yz/leetcode/untitled-1#297-serialize-and-deserialize-binary-tree)

## 200 Number of Islands 

[https://app.gitbook.com/@guilindev/s/interview/~/drafts/-MkFApqjA3myahwE70yz/leetcode/dfs\#200-number-of-islands](https://app.gitbook.com/@guilindev/s/interview/~/drafts/-MkFApqjA3myahwE70yz/leetcode/dfs#200-number-of-islands)

## 8 String to Integer \(atoi\) 



## 3 Longest Substring Without Repeating Characters 



## 720 Longest Word in Dictionary 



## 17 Letter Combinations of a Phone Number 



## 5 Longest Palindromic Substring



## 1669 Merge In Between Linked Lists 



## 1347 Minimum Number of Steps to Make Two Strings Anagram 

## 981 Time Based Key-Value Store 

## 179 Largest Number 

## 206 Reverse Linked List



## 99 Recover Binary Search Tree 

## 314 Binary Tree Vertical Order Traversal 



## 150 Evaluate Reverse Polish Notation 



## 907 Sum of Subarray Minimums

## 41 First Missing Positive 



## 127 Word Ladder 21 Merge Two Sorted Lists 



## 155 Min Stack



## 1071 Greatest Common Divisor of Strings 



## 42 Trapping Rain Water 



## 426 Convert Binary Search Tree to Sorted Doubly Linked List 



## 905 Sort Array By Parity 



## 733 Flood Fill



## 236 Lowest Common Ancestor of a Binary Tree 38 Count and Say 

## 232 Implement Queue using Stacks

##  168 Excel Sheet Column Title

##  62 Unique Paths

##  103 Binary Tree Zigzag Level Order Traversal

##  1136 Parallel Courses 226 Invert Binary Tree

##  355 Design Twitter 



## 347 Top K Frequent Elements 



## 54 Spiral Matrix



## 88 Merge Sorted Array



##  269 Alien Dictionary 



## 139 Word Break 



## 253 Meeting Rooms II 



## 421 Maximum XOR of Two Numbers in an Array 



## 856 Score of Parentheses



## 39 Combination Sum



## 394 Decode String 



## 977 Squares of a Sorted Array



## 162 Find Peak Element 



## 124 Binary Tree Maximum Path Sum 



## 380 Insert Delete GetRandom O\(1\) 



## 133 Clone Graph



## 242 Valid Anagram 



## 50 Pow\(x, n\) 63 Unique Paths II 



## 108 Convert Sorted Array to Binary Search Tree



## 328 Odd Even Linked List 



## 987 Vertical Order Traversal of a Binary Tree 



## 283 Move Zeroes 



## 443 String Compression 



## 332 Reconstruct Itinerary 



## 532 K-diff Pairs in an Array 



## 14 Longest Common Prefix



## 11 Container With Most Water 



## 349 Intersection of Two Arrays



## 230 Kth Smallest Element in a BST 



## 811 Subdomain Visit Count 



## 199 Binary Tree Right Side View 



## 438 Find All Anagrams in a String 



## 32 Longest Valid Parentheses



## 102 Binary Tree Level Order Traversal 



## 1209 Remove All Adjacent Duplicates in String II 



## 79 Word Search 



## 80 Remove Duplicates from Sorted Array II 



## 105 Construct Binary Tree from Preorder and Inorder Traversal 



## 252 Meeting Rooms



## 287 Find the Duplicate Number 

## 53 Maximum Subarray 



## 278 First Bad Version 



## 19 Remove Nth Node From End of List 



## 210 Course Schedule II 

## 71 Simplify Path 

## 36 Valid Sudoku 

## 416 Partition Equal Subset Sum 

## 28 Implement strStr\(\)

##  994 Rotting Oranges

##  1143 Longest Common Subsequence

##  70 Climbing Stairs

##  204 Count Primes 



## 509 Fibonacci Number 



## 73 Set Matrix Zeroes 



## 35 Search Insert Position




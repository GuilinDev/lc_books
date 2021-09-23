# VM

## 146 LRU Cache 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/gao-ji-de-shu-ju-jie-gou-he-suan-fa\#146-lru-cache](https://app.gitbook.com/@guilindev/s/interview/leetcode/gao-ji-de-shu-ju-jie-gou-he-suan-fa#146-lru-cache)

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

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#297-serialize-and-deserialize-binary-tree](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#297-serialize-and-deserialize-binary-tree)

## 200 Number of Islands 

[https://app.gitbook.com/@guilindev/s/interview/~/drafts/-MkFApqjA3myahwE70yz/leetcode/dfs\#200-number-of-islands](https://app.gitbook.com/@guilindev/s/interview/leetcode/dfs#200-number-of-islands)

## 8 String to Integer \(atoi\) 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#8-string-to-integer-atoi](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#8-string-to-integer-atoi)

## 3 Longest Substring Without Repeating Characters 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#3-longest-substring-without-repeating-characters](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#3-longest-substring-without-repeating-characters)

## 720 Longest Word in Dictionary 

给出一个字符串数组words组成的一本英语词典。从中找出最长的一个单词，该单词是由words词典中其他单词逐步添加一个字母组成。若其中有多个可行的答案，则返回答案中字典序最小的单词。

若无答案，则返回空字符串。

示例 1：

输入： words = \["w","wo","wor","worl", "world"\] 输出："world" 解释： 单词"world"可由"w", "wo", "wor", 和 "worl"添加一个字母组成。 示例 2：

输入： words = \["a", "banana", "app", "appl", "ap", "apply", "apple"\] 输出："apple" 解释： "apply"和"apple"都能由词典中的单词组成。但是"apple"的字典序小于"apply"。

提示：

所有输入的字符串都只包含小写字母。 words数组长度范围为\[1,1000\]。 words\[i\]的长度范围为\[1,30\]。

{% embed url="https://leetcode-cn.com/problems/longest-word-in-dictionary/solution/ci-dian-zhong-zui-chang-de-dan-ci-by-leetcode/" %}

## 17 Letter Combinations of a Phone Number 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#17-letter-combinations-of-a-phone-number](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#17-letter-combinations-of-a-phone-number)

## 5 Longest Palindromic Substring

[https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#5-longest-palindromic-substring](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#5-longest-palindromic-substring)

## 1669 Merge In Between Linked Lists 

![](../.gitbook/assets/image%20%28196%29.png)

{% embed url="https://leetcode-cn.com/problems/merge-in-between-linked-lists/solution/zhong-deng-nan-du-jian-dan-huan-ying-nin-pg43/" %}

## 1347 Minimum Number of Steps to Make Two Strings Anagram 

![](../.gitbook/assets/image%20%28197%29.png)

{% embed url="https://leetcode-cn.com/problems/minimum-number-of-steps-to-make-two-strings-anagram/solution/shuang-100-by-wei-yu-13-3/" %}

## 981 Time Based Key-Value Store 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedin\#981-time-based-key-value-store-53-8-medium](https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedin#981-time-based-key-value-store-53-8-medium)

## 179 Largest Number 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou\#179-largest-number](https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou#179-largest-number)

## 206 Reverse Linked List

[https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist\#206-reverse-linked-list](https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist#206-reverse-linked-list)

## 99 Recover Binary Search Tree 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#99-recover-binary-search-tree](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#99-recover-binary-search-tree)

## 314 Binary Tree Vertical Order Traversal 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook\#314-binary-tree-vertical-order-traversal-usd](https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook#314-binary-tree-vertical-order-traversal-usd)

## 150 Evaluate Reverse Polish Notation 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/stack\#150-evaluate-reverse-polish-notation](https://app.gitbook.com/@guilindev/s/interview/leetcode/stack#150-evaluate-reverse-polish-notation)

## 907 Sum of Subarray Minimums

![](../.gitbook/assets/image%20%28203%29.png)

{% embed url="https://leetcode-cn.com/problems/sum-of-subarray-minimums/solution/leetcodebi-ji-java-py-si-ke-yi-dao-ti-907-zi-shu-3/" %}

## 41 First Missing Positive 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook\#41-first-missing-positive](https://app.gitbook.com/@guilindev/s/interview/leetcode/array#41-first-missing-positive)

## 127 Word Ladder 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-3\#127-word-ladder](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-3#127-word-ladder)

## 21 Merge Two Sorted Lists 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist\#21-merge-two-sorted-lists](https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist#21-merge-two-sorted-lists)

## 155 Min Stack

[https://app.gitbook.com/@guilindev/s/interview/leetcode/stack\#155-min-stack](https://app.gitbook.com/@guilindev/s/interview/leetcode/stack#155-min-stack)

## 1071 Greatest Common Divisor of Strings 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#1071-greatest-common-divisor-of-strings](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#1071-greatest-common-divisor-of-strings)

## 42 Trapping Rain Water 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/stack\#42-trapping-rain-water](https://app.gitbook.com/@guilindev/s/interview/leetcode/stack#42-trapping-rain-water)

## 426 Convert Binary Search Tree to Sorted Doubly Linked List 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#426-convert-binary-search-tree-to-sorted-doubly-linked-list](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#426-convert-binary-search-tree-to-sorted-doubly-linked-list)

## 905 Sort Array By Parity 

按奇偶排序数组，给定一个非负整数数组 A，返回一个数组，在该数组中， A 的所有偶数元素之后跟着所有奇数元素。

你可以返回满足此条件的任何数组作为答案。

输入：\[3,1,2,4\] 输出：\[2,4,3,1\] 输出 \[4,2,3,1\]，\[2,4,1,3\] 和 \[4,2,1,3\] 也会被接受。

提示：

1 &lt;= A.length &lt;= 5000 0 &lt;= A\[i\] &lt;= 5000

{% embed url="https://leetcode-cn.com/problems/sort-array-by-parity/solution/an-qi-ou-pai-xu-shu-zu-by-leetcode/" %}

## 733 Flood Fill

图像渲染

![](../.gitbook/assets/image%20%28208%29.png)

{% embed url="https://leetcode-cn.com/problems/flood-fill/solution/tu-xiang-xuan-ran-by-leetcode-solution/" %}

## 236 Lowest Common Ancestor of a Binary Tree 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#236-lowest-common-ancestor-of-binary-tree](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#236-lowest-common-ancestor-of-binary-tree)

## 38 Count and Say 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#38-count-and-say](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#38-count-and-say)

## 232 Implement Queue using Stacks

[https://app.gitbook.com/@guilindev/s/interview/leetcode/stack\#232-implement-queue-using-stacks](https://app.gitbook.com/@guilindev/s/interview/leetcode/stack#232-implement-queue-using-stacks)

## 168 Excel Sheet Column Title

[https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook\#168-excel-sheet-column-title](https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook#168-excel-sheet-column-title)

## 62 Unique Paths

[https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#62-unique-paths](https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer#62-unique-paths)

##  103 Binary Tree Zigzag Level Order Traversal

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#103-binary-tree-zigzag-level-order-traversal](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#103-binary-tree-zigzag-level-order-traversal)

## 1136 Parallel Courses $

给定一个整数 n，它表示有 n 个标记为 1 到 n 的课程。您还会得到一个数组关系，其中关系 \[i\] = \[prevCoursei, nextCoursei\]，代表课程 prevCoursei 和课程 nextCoursei 之间的先决条件关系：课程 prevCoursei 必须在课程 nextCoursei 之前进行。

在一个学期内，您可以选修任意数量的课程，只要您已经完成了上一学期所修课程的所有先决条件。

返回学习所有课程所需的最少学期数。如果没有办法走完所有课程，返回-1。

![](../.gitbook/assets/image%20%28206%29.png)

拓扑排序，Time & space: O\(V + E\).

```java
 public int minimumSemesters(int N, int[][] relations) {
        Map<Integer, List<Integer>> g = new HashMap<>(); // key: prerequisite, value: course list. 
        int[] inDegree = new int[N + 1]; // inDegree[i]: number of prerequisites for i.
        for (int[] r : relations) {
            g.computeIfAbsent(r[0], l -> new ArrayList<>()).add(r[1]); // construct graph.
            ++inDegree[r[1]]; // count prerequisites for r[1].
        }
        Queue<Integer> q = new LinkedList<>(); // save current 0 in-degree vertices.
        for (int i = 1; i <= N; ++i)
            if (inDegree[i] == 0)
                q.offer(i);
        int semester = 0;
        while (!q.isEmpty()) { // BFS traverse all currently 0 in degree vertices.
            for (int sz = q.size(); sz > 0; --sz) { // sz is the search breadth.
                int c = q.poll();
                --N;
                if (!g.containsKey(c)) continue; // c's in-degree is currently 0, but it has no prerequisite.
                for (int course : g.remove(c))
                    if (--inDegree[course] == 0) // decrease the in-degree of course's neighbors.
                        q.offer(course); // add current 0 in-degree vertex into Queue.
            }
            ++semester; // need one more semester.
        }
        return N == 0 ? semester : -1;
    }
```

## 226 Invert Binary Tree

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#226-invert-binary-tree](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#226-invert-binary-tree)

## 355 Design Twitter 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/design\#355-design-twitter](https://app.gitbook.com/@guilindev/s/interview/leetcode/design#355-design-twitter)

## 347 Top K Frequent Elements 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table\#347-top-k-frequent-elements](https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table#347-top-k-frequent-elements)

## 54 Spiral Matrix

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array\#54-spiral-matrix](https://app.gitbook.com/@guilindev/s/interview/leetcode/array#54-spiral-matrix)

## 88 Merge Sorted Array

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array/he-bing-you-xu-de-arrays\#88-merge-sorted-array](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/he-bing-you-xu-de-arrays#88-merge-sorted-array)

## 269 Alien Dictionary 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/graph\#269-alien-dictionary](https://app.gitbook.com/@guilindev/s/interview/leetcode/graph#269-alien-dictionary)

## 139 Word Break 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#139-word-break](https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer#139-word-break)

## 253 Meeting Rooms II 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou\#253-meeting-rooms-ii](https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou#253-meeting-rooms-ii)

## 421 Maximum XOR of Two Numbers in an Array 

**数组中两个数的最大异或值**

![](../.gitbook/assets/image%20%28201%29.png)

{% embed url="https://leetcode-cn.com/problems/maximum-xor-of-two-numbers-in-an-array/solution/shu-zu-zhong-liang-ge-shu-de-zui-da-yi-h-n9m9/" %}

## 856 Score of Parentheses

括号的分数

![](../.gitbook/assets/image%20%28202%29.png)

{% embed url="https://leetcode-cn.com/problems/score-of-parentheses/solution/gua-hao-de-fen-shu-by-leetcode/" %}

## 39 Combination Sum

[https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking\#39-combination-sum](https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking#39-combination-sum)

## 394 Decode String 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/tag4\#394-decode-string](https://app.gitbook.com/@guilindev/s/interview/leetcode/tag4#394-decode-string)

## 977 Squares of a Sorted Array

![](../.gitbook/assets/image%20%28204%29.png)

{% embed url="https://leetcode-cn.com/problems/squares-of-a-sorted-array/solution/you-xu-shu-zu-de-ping-fang-by-leetcode-solution/" %}

## 162 Find Peak Element 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zai-array-zhong-cha-xun-yuan-su\#162-find-peak-element](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zai-array-zhong-cha-xun-yuan-su#162-find-peak-element)

## 124 Binary Tree Maximum Path Sum 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#124-binary-tree-maximum-path-sum](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#124-binary-tree-maximum-path-sum)

## 380 Insert Delete GetRandom O\(1\) 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/design\#380-insert-delete-getrandom-o-1](https://app.gitbook.com/@guilindev/s/interview/leetcode/design#380-insert-delete-getrandom-o-1)

## 133 Clone Graph

[https://app.gitbook.com/@guilindev/s/interview/leetcode/graph\#133-clone-graph](https://app.gitbook.com/@guilindev/s/interview/leetcode/graph#133-clone-graph)

## 242 Valid Anagram 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou\#242-valid-anagram](https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou#242-valid-anagram)

## 50 Pow\(x, n\) 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/er-fen-sou-suo\#50-power-x-n](https://app.gitbook.com/@guilindev/s/interview/leetcode/er-fen-sou-suo#50-power-x-n)

## 63 Unique Paths II 

机器人走路，有障碍物，打出所有路径

![](../.gitbook/assets/image%20%28199%29.png)

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid == null || obstacleGrid.length == 0) {
            return 0;
        }
        
        // 定义 dp 数组并初始化第 1 行和第 1 列。
        int m = obstacleGrid.length, n = obstacleGrid[0].length;
        int[][] dp = new int[m][n];
        for (int i = 0; i < m && obstacleGrid[i][0] == 0; i++) {
            dp[i][0] = 1;
        }
        for (int j = 0; j < n && obstacleGrid[0][j] == 0; j++) {
            dp[0][j] = 1;
        }

        // 根据状态转移方程 dp[i][j] = dp[i - 1][j] + dp[i][j - 1] 进行递推。
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (obstacleGrid[i][j] == 0) {
                    dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
                }
            }
        }
        return dp[m - 1][n - 1];
    }
}
```

## 108 Convert Sorted Array to Binary Search Tree

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#108-convert-sorted-array-to-binary-search-tree](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#108-convert-sorted-array-to-binary-search-tree)

## 328 Odd Even Linked List 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist\#328-odd-even-linked-list](https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist#328-odd-even-linked-list)

## 987 Vertical Order Traversal of a Binary Tree 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook\#987-vertical-order-traversal-of-a-binary-tree](https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook#987-vertical-order-traversal-of-a-binary-tree)

## 283 Move Zeroes 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array\#283-move-zeroes](https://app.gitbook.com/@guilindev/s/interview/leetcode/array#283-move-zeroes)

## 443 String Compression 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook\#443-string-compression](https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook#443-string-compression)

## 332 Reconstruct Itinerary 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/tag1\#332-reconstruct-itinerary](https://app.gitbook.com/@guilindev/s/interview/leetcode/tag1#332-reconstruct-itinerary)

## 532 K-diff Pairs in an Array 

**数组中的 k-diff 数对**

![](../.gitbook/assets/image%20%28198%29.png)

```java
class Solution {
    public int findPairs(int[] nums, int k) {
        int ans = 0;
        //先对数组从大到小排序；
        Arrays.sort(nums);
        //定义一个map，用来存放k-diff对
        HashMap<Integer,Integer> map = new HashMap<Integer,Integer>();
        //set用来存放已经访问过的数组元素
        HashSet<Integer> set = new HashSet<Integer>();
        for(int i = 0;i < nums.length;i ++){
            //用pre表示nums[j]，因为已经排过序，所以如果存在，那么nums[j]一定在set中
            int pre = nums[i] - k;
            if(set.contains(pre)){
                map.put(nums[i],pre);
            }
            //将访问过的nums[i]放入set;
            set.add(nums[i]);
        }
        //keySet的大小即为答案;
        return map.keySet().size();
    }
}
```

## 14 Longest Common Prefix

\*\*\*\*[**https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook\#14-longest-common-prefix**](https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook#14-longest-common-prefix)\*\*\*\*

## 11 Container With Most Water 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array\#11-container-with-most-water](https://app.gitbook.com/@guilindev/s/interview/leetcode/array#11-container-with-most-water)

## 349 Intersection of Two Arrays

[https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table\#349-intersection-of-two-arrays](https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table#349-intersection-of-two-arrays)

## 230 Kth Smallest Element in a BST 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#230-kth-smallest-element-in-bst](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#230-kth-smallest-element-in-bst)

## 811 Subdomain Visit Count 

![](../.gitbook/assets/image%20%28200%29.png)

{% embed url="https://leetcode-cn.com/problems/subdomain-visit-count/solution/zi-yu-ming-fang-wen-ji-shu-by-leetcode/" %}

## 199 Binary Tree Right Side View 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-3\#199-binary-tree-right-side-view](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-3#199-binary-tree-right-side-view)

## 438 Find All Anagrams in a String 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook\#438-find-all-anagrams-in-a-string](https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook#438-find-all-anagrams-in-a-string)

## 32 Longest Valid Parentheses

[https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook\#32-longest-valid-parentheses](https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook#32-longest-valid-parentheses)

## 102 Binary Tree Level Order Traversal 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#102-binary-tree-level-order-traversal](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#102-binary-tree-level-order-traversal)

## 1209 Remove All Adjacent Duplicates in String II 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook\#1209-remove-all-adjacent-duplicates-in-string-ii](https://app.gitbook.com/@guilindev/s/interview/leetcode/facebook#1209-remove-all-adjacent-duplicates-in-string-ii)

## 79 Word Search 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking\#79-word-search](https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking#79-word-search)

## 80 Remove Duplicates from Sorted Array II 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array/26-27-80-zai-array-zhong-yi-chu-yuan-su\#80-remove-duplicates-from-sorted-array-ii](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/26-27-80-zai-array-zhong-yi-chu-yuan-su#80-remove-duplicates-from-sorted-array-ii)

## 105 Construct Binary Tree from Preorder and Inorder Traversal 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1\#105-construct-binary-tree-from-preorder-and-inorder-traversal](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-1#105-construct-binary-tree-from-preorder-and-inorder-traversal)

## 252 Meeting Rooms

[https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou\#252-meeting-rooms](https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou#252-meeting-rooms)

## 287 Find the Duplicate Number 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array\#287-find-the-duplicate-number](https://app.gitbook.com/@guilindev/s/interview/leetcode/array#287-find-the-duplicate-number)

## 53 Maximum Subarray 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zi-shu-zu-subarray\#53-maximum-subarray](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zi-shu-zu-subarray#53-maximum-subarray)

## 278 First Bad Version 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/er-fen-sou-suo\#278-first-bad-version](https://app.gitbook.com/@guilindev/s/interview/leetcode/er-fen-sou-suo#278-first-bad-version)

## 19 Remove Nth Node From End of List 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist\#19-remove-nth-node-from-end-of-list](https://app.gitbook.com/@guilindev/s/interview/leetcode/linkedlist#19-remove-nth-node-from-end-of-list)

## 210 Course Schedule II 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/graph\#210-course-schedule-ii](https://app.gitbook.com/@guilindev/s/interview/leetcode/graph#210-course-schedule-ii)

## 71 Simplify Path 

[把Unix风格路径规范和简化 - 顺序栈](https://leetcode-cn.com/problems/simplify-path/solution/2020040371medianzhan-zi-fu-shu-zu-ke-bian-zi-fu-ch/)

## 36 Valid Sudoku 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table\#36-valid-sudoku](https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table#36-valid-sudoku)

## 416 Partition Equal Subset Sum 

[正整数数组分成两个子集让二者和相等 - DP](https://leetcode-cn.com/problems/partition-equal-subset-sum/solution/fen-ge-deng-he-zi-ji-by-leetcode-solution/)

## 28 Implement strStr\(\)

[KMP了解下](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#28-implement-substr)

## 994 Rotting Oranges

[https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-3\#994-rotting-oranges](https://app.gitbook.com/@guilindev/s/interview/leetcode/untitled-3#994-rotting-oranges)

## 1143 Longest Common Subsequence

[https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#1143-longest-common-subsequence](https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer#1143-longest-common-subsequence)

## 70 Climbing Stairs

[https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#70-climb-stairs](https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer#70-climb-stairs)

## 204 Count Primes 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/math\#204-count-primes](https://app.gitbook.com/@guilindev/s/interview/leetcode/math#204-count-primes)

## 509 Fibonacci Number 

![](../.gitbook/assets/image%20%28207%29.png)

![](../.gitbook/assets/image%20%28205%29.png)

## 73 Set Matrix Zeroes 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array\#73-set-matrix-zeroes](https://app.gitbook.com/@guilindev/s/interview/leetcode/array#73-set-matrix-zeroes)

## 35 Search Insert Position

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zai-array-zhong-cha-xun-yuan-su\#35-search-insertion-position](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zai-array-zhong-cha-xun-yuan-su#35-search-insertion-position)


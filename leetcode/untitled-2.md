# Two Pointers

## 76 Minimum Window Substring

### 原题概述

Given a string S and a string T, find the minimum window in S which will contain all the characters in T in complexity O\(n\).

**Example:**

```text
Input: S = "ADOBECODEBANC", T = "ABC"
Output: "BANC"
```

**Note:**

* If there is no such window in S that covers all characters in T, return the empty string `""`.
* If there is such window, you are guaranteed that there will always be only one unique minimum window in S.

### 题意和分析

要求在字符串s中找到最小的子字符串w（window），其中包含T中所有的字符，如果没有就返回空字符串，如果有，最小的字符串只有唯一的一个。

### 代码

Labuladuodong的[双指针模板](https://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247485141&idx=1&sn=0e4583ad935e76e9a3f6793792e60734&chksm=9bd7f8ddaca071cbb7570b2433290e5e2628d20473022a5517271de6d6e50783961bebc3dd3b&scene=21#wechat_redirect)，用两个HashMap，比较好理解的做法

```java
class Solution {
    public String minWindow(String s, String t) {
        HashMap<Character, Integer> needs = new HashMap<>(); // target - t中字符的出现次数
        HashMap<Character, Integer> window = new HashMap<>(); // 窗口中相应“有效”字符的出现次数
        
        for (char ch : t.toCharArray()) {
            needs.put(ch, needs.getOrDefault(ch, 0) + 1);
        }
        
        int left = 0, right = 0; // 窗口的左右边界初始为零[left, right)
        int valid = 0; // 窗口中满足在needs的字符个数
        
        // 记录最小覆盖字串的起始索引及长度
        int start = 0, len = Integer.MAX_VALUE;
        while (right < s.length()) {
            // ch是准备进入窗口的字符
            char ch = s.charAt(right);
            // 得到ch后右移窗口，相当于寻找一个【可行解】
            right++;
            
            // 更新窗口内的一系列数据
            if (needs.containsKey(ch)) {//target-t中需要该字符
                window.put(ch, window.getOrDefault(ch, 0) + 1);
                
                // 该字符在window中的数量已经等于（满足）了target-t中该字符
                if (window.get(ch).equals(needs.get(ch))) { 
                    valid++;
                }
            }
            
            // 判断窗口左侧是否需要收紧，以此来优化【可行解】，得到最优解
            // valid已经达到target中所有字符的数量，表示找到一个s中的子字符串包含全部t中字符
            // 注意这里是hashmap的size而不是t的leng，因为可能有重复元素
            while(valid == needs.size()) { 
                //更新最小覆盖字串
                if (right - left < len) { //遇到更小的字串
                    start = left;
                    len = right - left;
                }
                // 准备移除窗口的字符
                char de = s.charAt(left);
                // 移动窗口左侧缩小窗口
                left++;
                
                // 更新窗口内的一系列数据，窗口左边遇到一个“有效”字符
                if (needs.containsKey(de)) {
                    if (window.get(de).equals(needs.get(de))) {
                        valid--;
                    }
                    window.put(de, window.get(de) - 1); // 该有效字符减少一次
                }
            }
        }
        // 返回最小覆盖字串
        return len == Integer.MAX_VALUE ? "" : s.substring(start, start + len);
    }
}
```

使用HashMap和Deque

```java
class Solution {
    public String minWindow(String s, String t) {
        
        // By default the window is the entire string...
        int bestMin = 0;
        int bestMax = s.length() -1;
        
        // This holds the amount of each character we need to see
        Map<Character, Integer> charCount = new HashMap<>();
        
        for (int i = 0; i < t.length(); i++) {
            char c = t.charAt(i);
            Integer count = charCount.getOrDefault(c, 0);
            charCount.put(c, count+1);
        }
        
        // This holds characters and their last seen indexes in the string
        Map<Character, Deque<Integer>> lastSeen = new HashMap<>();
        // This holds characters that we have seen the minimum amount of
        Set<Character> seenMin = new HashSet<>();
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i); 
            
            // check if this is a character we are looking for
            if (charCount.containsKey(c)) {
                
                Deque<Integer> positions = lastSeen.getOrDefault(c, new LinkedList<>());
                
                // If we have stored the maximum positions, remove the earliest and add the newest to the end
                if (positions.size() == charCount.get(c)) {
                    positions.removeFirst();
                }      
                
                positions.addLast(i);
                
                if (positions.size() == charCount.get(c)) {
                    seenMin.add(c);
                }
                
                lastSeen.put(c, positions);
                
                // If we have seen all our characters the minimum amount of times
                // we can try and calculate the minimum total length that holds all those characters
                if (seenMin.size() == charCount.size()) {
                    int min = Integer.MAX_VALUE;
                    int max = Integer.MIN_VALUE;
                    
                    // Iterate over the positions for each character and grab their min/max positions
                    for (Deque<Integer> values : lastSeen.values()) {
                        min = Math.min(min, values.getFirst());
                        max = Math.max(max, values.getLast());
                    }  
                    
                    // If we have a smaller length than our current best length, save it.
                    if (max - min < bestMax - bestMin) {
                        bestMax = max;
                        bestMin = min;
                    }
                }
            }      
        }
        
        // If we have seen all of our characters the minimum amount of times we know we will have a valid solution
        if (seenMin.size() == charCount.size()) {
            return s.substring(bestMin, bestMax+1);  
        } else {
            return "";
        }
    }
}
```

## 159 Longest Substring with At Most Two Distinct Characters

### 原题概述

Given a string _**s**_ , find the length of the longest substring _**t**_  that contains **at most** 2 distinct characters.

**Example 1:**

```text
Input: "eceba"
Output: 3
Explanation: t is "ece" which its length is 3.
```

**Example 2:**

```text
Input: "ccaabbb"
Output: 5
Explanation: t is "aabbb" which its length is 5.
```

### 题意和分析

所有滑动窗口的题目的总结在[这里](https://leetcode.com/problems/longest-substring-with-at-most-two-distinct-characters/discuss/49708/Sliding-Window-algorithm-template-to-solve-all-the-Leetcode-substring-search-problem.)。维护一个hashmap，保存字符和其位置索引，通过判断其size来确定现在滑动窗口有几个字符，如果大于2个，则找到最左边的位置索引删除，同时保存最大子字符串的长度。

### 代码

```java
class Solution {
    public int lengthOfLongestSubstringTwoDistinct(String s) {
        if (s == null && s.length() == 0) {
            return -1;
        }
        int slow = 0, fast = 0;
        int maxLength = 0;
        HashMap<Character, Integer> map = new HashMap<>();
        while (fast < s.length()) {
            if (map.size() <= 2) {
                map.put(s.charAt(fast), fast);
                fast++;
            }
            if (map.size() > 2) {
                int leftMost = s.length();//初始值只要不是在0到s.length()-1之间就行
                for (int i : map.values()) {
                    leftMost = Math.min(leftMost, i);
                }
                char toBeDeleted = s.charAt(leftMost);
                map.remove(toBeDeleted);
                slow = leftMost + 1;//删除的字符的下一个
            }
            maxLength = Math.max(maxLength, fast - slow);
        }
        return maxLength;
    }
}
```

另外一种写法，比较高效但不好扩展

```java
class Solution {
    public int lengthOfLongestSubstringTwoDistinct(String s) {
        char[] tree = s.toCharArray();
        int result = 0;
        int[] basket = {-1, -1};
        for(int i = 0; i < tree.length; i++){
            if(basket[0] == -1){
                basket[0] = i;
            }
            if(basket[1] == -1){
                basket[1] = i;
            }else if(tree[i] != tree[basket[1]] && tree[i] != tree[basket[0]]){
                basket[1] = i;
                int j = i - 1;
                while(j > 0 && tree[j - 1] == tree[j]){//从i前面一个数字开始查找，直到找到不同的字符（因为滑动窗口最多两个字符u）
                    j--;
                }
                basket[0] = j;
            }
            result = Math.max(result, i - Math.min(basket[0], basket[1]) + 1);
        }
        return result;
    }
}
```

## 30 Substring with Concatenation of All Words

### 原题概述

You are given a string, **s**, and a list of words, **words**, that are all of the same length. Find all starting indices of substring\(s\) in **s** that is a concatenation of each word in **words** exactly once and without any intervening characters.

**Example 1:**

```text
Input:
  s = "barfoothefoobarman",
  words = ["foo","bar"]
Output: [0,9]
Explanation: Substrings starting at index 0 and 9 are "barfoor" and "foobar" respectively.
The output order does not matter, returning [9,0] is fine too.
```

**Example 2:**

```text
Input:
  s = "wordgoodstudentgoodword",
  words = ["word","student"]
Output: []
```

### 题意和分析

这道题跟上一道题76 Minimum Window Substring的找子字符串的方法类似。给定一个目标字符串s，一个单词集合words。 要求使得words集合中所有元素连续出现在s中的首位置组成的集合（元素顺序不考虑）。

正如所给实例，目标字符串s: “barfoothefoobarman” 对比单词集合words: \[“foo”, “bar”\] 我们发现，在pos=0 ~ 5时“barfoo”恰好匹配，则0存入结果；在pos=9 ~ 14时“foobar”恰好匹配，则9存入结果；

参考了discuss的解法，利用两个map，一个来记录每个单词的次数，另一个来记录看到的次数。

### 代码

```java
class Solution {
    public List<Integer> findSubstring(String s, String[] words) {
        List<Integer> res = new ArrayList<>();
        if (words.length == 0) {
            return res;
        }
        Map<String, Integer> dict = new HashMap<>();
        Map<String, Integer> found = new HashMap<>();
        for (String word : words) {
            dict.put(word, dict.getOrDefault(word, 0) + 1);
        }
        int wordLen = words[0].length(), totalLen = words.length * wordLen, strLen = s.length();
        for (int start = 0; start < strLen - totalLen + 1; start++) {
            int end = start;
            while (end < start + totalLen) {
                String substr = s.substring(end, end + wordLen);
                found.put(substr, found.getOrDefault(substr, 0) + 1);
                if (found.get(substr) > dict.getOrDefault(substr, 0)) { break; }
                end += wordLen;
            }
            if (end == start + totalLen) { res.add(start); }
            found.clear();
        }
        return res;
    }
}
```

## 125 Valid Palindrome

### 原题概述

Given a string, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.

**Note:** For the purpose of this problem, we define empty string as valid palindrome.

**Example 1:**

```text
Input: "A man, a plan, a canal: Panama"
Output: true
```

**Example 2:**

```text
Input: "race a car"
Output: false
```

### 题意和分析

判断一个字符串是否是回文字符串，忽略大小写和空格标点等，只考虑字母，难度比较小，用一下Java的api里的Character的isLetterOrDigit\(\)和toLowerCase\(\)/toUpperCase\(\)方法即可。

### 代码

```java
public class Solution {
    public boolean isPalindrome(String s) {
        if (s.isEmpty()) {
            return true;
        }

        int left = 0, right = s.length() - 1;
        while (left < right) {
            char c1 = s.charAt(left);
            char c2 = s.charAt(right);
            if (!Character.isLetterOrDigit(c1)) {
                left++;
            } else if (!Character.isLetterOrDigit(c2)) {
                right--;
            } else {
                if (Character.toLowerCase(c1) != Character.toLowerCase(c2)) {
                    return false;
                }
                left++;
                right--;
            }
        }
        return true;
    }
}
```

## 904 Fruits Into Basket

### 原题概述

In a row of trees, the `i`-th tree produces fruit with type `tree[i]`.

You **start at any tree of your choice**, then repeatedly perform the following steps:

1. Add one piece of fruit from this tree to your baskets.  If you cannot, stop.
2. Move to the next tree to the right of the current tree.  If there is no tree to the right, stop.

Note that you do not have any choice after the initial choice of starting tree: you must perform step 1, then step 2, then back to step 1, then step 2, and so on until you stop.

You have two baskets, and each basket can carry any quantity of fruit, but you want each basket to only carry one type of fruit each.

What is the total amount of fruit you can collect with this procedure?

**Example 1:**

```text
Input: [1,2,1]
Output: 3
Explanation: We can collect [1,2,1].
```

**Example 2:**

```text
Input: [0,1,2,2]
Output: 3
Explanation: We can collect [1,2,2].
If we started at the first tree, we would only collect [0, 1].
```

**Example 3:**

```text
Input: [1,2,3,2,2]
Output: 4
Explanation: We can collect [2,3,2,2].
If we started at the first tree, we would only collect [1, 2].
```

**Example 4:**

```text
Input: [3,3,3,1,2,1,1,2,3,3,4]
Output: 5
Explanation: We can collect [1,2,1,1,2].
If we started at the first tree or the eighth tree, we would only collect 4 fruits.
```

**Note:**

1. `1 <= tree.length <= 40000`
2. `0 <= tree[i] < tree.length`

### 题意和分析

Two Pointers问题，主要的思路是利用两个指针来构造一个滑动窗口，且要求窗口中的元素类型不得超过2种。

### 代码

```java
class Solution {
    public int totalFruit(int[] tree) {
        int lastFruit = -1;
        int secondLastFruit = -1;
        int lastFruitCount = 0;
        int currentMax = 0;
        int max = 0;

        for (int fruit : tree) {
            if (fruit == lastFruit || fruit == secondLastFruit) {
                currentMax++;
            } else {
                currentMax = lastFruitCount + 1;// last fruit + new fruit
            }

            if (fruit == lastFruit) {
                lastFruitCount++;
            } else {
                lastFruitCount = 1;
            }

            if (fruit != lastFruit) {
                secondLastFruit = lastFruit;
                lastFruit = fruit;
            }

            max = Math.max(max, currentMax);
        }
        return max;
    }
}
```

## 986 Interval List Intersections

### 题目

Given two lists of **closed** intervals, each list of intervals is pairwise disjoint and in sorted order.

Return the intersection of these two interval lists.

_\(Formally, a closed interval `[a, b]` \(with `a <= b`\) denotes the set of real numbers `x` with `a <= x <= b`.  The intersection of two closed intervals is a set of real numbers that is either empty, or can be represented as a closed interval.  For example, the intersection of \[1, 3\] and \[2, 4\] is \[2, 3\].\)_

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/01/30/interval1.png)

```text
Input: A = [[0,2],[5,10],[13,23],[24,25]], B = [[1,5],[8,12],[15,24],[25,26]]
Output: [[1,2],[5,5],[8,10],[15,23],[24,24],[25,25]]
```

**Note:**

1. `0 <= A.length < 1000`
2. `0 <= B.length < 1000`
3. `0 <= A[i].start, A[i].end, B[i].start, B[i].end < 10^9`

### 分析

我们称 b 为区间 \[a, b\] 的末端点。在两个数组给定的所有区间中，假设拥有最小末端点的区间是 A\[0\]，然后，在数组 B 的区间中， A\[0\] 只可能与数组 B 中的至多一个区间相交。（想象一下，如果 B 中存在两个区间均与 A\[0\] 相交，那么B中这两个区间都将包含 A\[0\] 的末端点，但是 B 中的区间应该是不相交的，所以B中最多只有一个区间跟A相交，反之亦然）。

所以如果 A\[0\] 拥有最小的末端点，那么它只可能与 B\[0\] 相交。然后我们就可以跳过区间 A\[0\]，因为它不能与其他任何区间再相交了。相似的，如果 B\[0\] 拥有最小的末端点，那么它只可能与区间 A\[0\] 相交，然后我们就可以将 B\[0\] 删除，因为它无法再与其他区间相交了。

用两个指针来模拟完成跳过A\[0\] 或 B\[0\] 的操作。

### 代码

```java
class Solution {
    public int[][] intervalIntersection(int[][] A, int[][] B) {
        if (A == null || A.length == 0 || A[0].length == 0) {
            return new int[0][0];
        }
        List<int[]> results = new ArrayList<>();
        int AIndex = 0, BIndex = 0;
        
        while (AIndex < A.length && BIndex < B.length) {
            int low = Math.max(A[AIndex][0], B[BIndex][0]);
            int high = Math.min(A[AIndex][1], B[BIndex][1]);
            
            if (low <= high) { // 这个条件看是否有相交
                results.add(new int[]{low, high});
            }
            
            // 跳过拥有较小末端点的元素
            if (A[AIndex][1] < B[BIndex][1]) {
                AIndex++;
            } else {
                BIndex++;
            }
        }
        return results.toArray(new int[results.size()][]);
    }
}
```


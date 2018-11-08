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

[这里](https://leetcode.com/problems/minimum-window-substring/discuss/26808/Here-is-a-10-line-template-that-can-solve-most-'substring'-problems)有大神总结的找子字符串的模板，利用了ASCII码创建数组来查询子字符串，维护两个索引来完成。

### 代码

```java
class Solution {
    public String minWindow(String s, String t) {
        if (s == null || t == null) return "";
        // total number of character in p to be contained in s
        int countToBeContained = t.length(), minLen = Integer.MAX_VALUE, startIndex = -1;
        // calculate the number of each character to be contained in S
        int[] dict = new int[128];
        for (char c : t.toCharArray()) dict[c]++;

        int fast = 0, slow = 0;
        while (fast < s.length()) {
            // if need to be contained, include it and minus the counter
            if (dict[s.charAt(fast++)]-- > 0)
                countToBeContained--;

            // all included, move the slow pointer to minimize the window
            while (countToBeContained == 0) {
                // current window contains same number of the current character as in t, cannot move forward anymore
                if (dict[s.charAt(slow++)]++ == 0) countToBeContained++;

                // update minLen
                if (fast - slow + 1 < minLen) {
                    startIndex = slow - 1;
                    minLen = fast - slow + 1;
                }
            }
        }
        return startIndex == -1 ? "" : s.substring(startIndex, startIndex + minLen);
    }
}
```

清爽一点的写法

```java
class Solution {
    public String minWindow(String s, String t) {
        int[] count = new int[128];
        for (char c : t.toCharArray()) {
            count[c]++;
        }
        int from = 0;
        //total数目表示当前尚未找到的t字符串里面的字符数，比如total==2表示当前该子字符串还有2个字符尚未在s中找到，
        //具体哪两个字符则由count数组的相应出现字符的数目来确定的
        int total = t.length();
        int min = Integer.MAX_VALUE;//最小子字符串的长度
        for (int i = 0, j = 0; i < s.length(); i++) {//双指针，i在前，正常遍历；j在后，记录包含所有t字符串的字符的起始位置
            if (count[s.charAt(i)]-- > 0) {//i位置的字符在之前出现过（count数组的初始化），再出现就是重复的了，把total减1；每个字符的位置也得次数也得-1
                total--;
            }
            while (total == 0) {//t中的字符都找到了
                if (i - j + 1 < min) {//比较一下是否有更短的子字符串
                    min = i - j + 1;
                    from = j;
                }
                count[s.charAt(j)]++;//移动j把刚才减的1加回来，促使前面的指针i继续往前走寻找t中字符
                if (count[s.charAt(j++)] > 0) {
                    total++;
                }
            }
        }
        return (min == Integer.MAX_VALUE) ? "" : s.substring(from, from + min);
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



### 代码



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


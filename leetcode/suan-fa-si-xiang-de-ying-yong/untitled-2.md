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


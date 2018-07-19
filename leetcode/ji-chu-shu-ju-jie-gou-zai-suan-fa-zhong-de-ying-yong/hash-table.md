# Hash Table

## 500 - Keyboard Row

### 原题概述

Given a List of words, return the words that can be typed using letters of **alphabet** on only one row's of American keyboard like the image below.  


![American keyboard](https://leetcode.com/static/images/problemset/keyboard.png)

**Example 1:**  


```text
Input: ["Hello", "Alaska", "Dad", "Peace"]
Output: ["Alaska", "Dad"]
```

**Note:**  


1. You may use one character in the keyboard more than once.
2. You may assume the input string will only contain letters of alphabet.

### 题意和分析

检查一个字符串数组里面的所有字符串，返回一些字符串，这些字符串只用键盘中的一行就可以写出来。可以用regex或者hashmap。把在同row的char标记成同index存在HashMap中；然后判定word中的每一个char是否有相同的index；Time Complexity: O\(n\), n是words中每个word的char总和.

### 代码

```java
class Solution {
    public String[] findWords(String[] words) {
        if (words == null || words.length == 0) {
            return words;
        }
        String[] keyboard = {"qwertyuiop","asdfghjkl","zxcvbnm"};
        HashMap<Character, Integer> hm = new HashMap<>();
        for (int i = 0; i < keyboard.length; i++) {
            for (char c : keyboard[i].toCharArray()) {
                hm.put(c, i);//每一列的i不一样0,1,2
            }
        }
        List<String> result = new ArrayList<>();
        for (String word : words) {
            if (word == "") {
                continue;
            }
            int index = hm.get(word.toLowerCase().charAt(0));//查一下第一个字符的index
            //检查字符串里面所有字符的index
            for (char c : word.toLowerCase().toCharArray()) {
                if (index != hm.get(c)) {//如果有字符的index和第一个字符不相同
                    index = -1;
                }
            }
            if (index != -1) {
                result.add(word);
            }
        }
        return result.toArray(new String[0]);//new String[0]确定是字符串数组
    }
}
```

## 463 - Island Perimeter

### 原题概述

You are given a map in form of a two-dimensional integer grid where 1 represents land and 0 represents water. Grid cells are connected horizontally/vertically \(not diagonally\). The grid is completely surrounded by water, and there is exactly one island \(i.e., one or more connected land cells\). The island doesn't have "lakes" \(water inside that isn't connected to the water around the island\). One cell is a square with side length 1. The grid is rectangular, width and height don't exceed 100. Determine the perimeter of the island.

**Example:**

```text
[[0,1,0,0],
 [1,1,1,0],
 [0,1,0,0],
 [1,1,0,0]]

Answer: 16
Explanation: The perimeter is the 16 yellow stripes in the image below:

```

![](../../.gitbook/assets/image%20%2828%29.png)

### 题意和分析

### 代码


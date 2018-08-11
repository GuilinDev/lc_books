# String

## 13 - Roman to Integer

### 原题概述

Roman numerals are represented by seven different symbols: `I`, `V`, `X`, `L`, `C`, `D` and `M`.

```text
Symbol       Value
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```

For example, two is written as `II` in Roman numeral, just two one's added together. Twelve is written as, `XII`, which is simply `X` + `II`. The number twenty seven is written as `XXVII`, which is `XX` + `V` + `II`.

Roman numerals are usually written largest to smallest from left to right. However, the numeral for four is not `IIII`. Instead, the number four is written as `IV`. Because the one is before the five we subtract it making four. The same principle applies to the number nine, which is written as `IX`. There are six instances where subtraction is used:

* `I` can be placed before `V` \(5\) and `X` \(10\) to make 4 and 9. 
* `X` can be placed before `L` \(50\) and `C` \(100\) to make 40 and 90. 
* `C` can be placed before `D` \(500\) and `M` \(1000\) to make 400 and 900.

Given a roman numeral, convert it to an integer. Input is guaranteed to be within the range from 1 to 3999.

**Example 1:**

```text
Input: "III"
Output: 3
```

**Example 2:**

```text
Input: "IV"
Output: 4
```

**Example 3:**

```text
Input: "IX"
Output: 9
```

**Example 4:**

```text
Input: "LVIII"
Output: 58
Explanation: C = 100, L = 50, XXX = 30 and III = 3.
```

**Example 5:**

```text
Input: "MCMXCIV"
Output: 1994
Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.
```

### 题意和分析

罗马数字转换为数字，参考[Grandyang的解释](http://www.cnblogs.com/grandyang/p/4120857.html)

1、相同的数字连写，所表示的数等于这些数字相加得到的数，如：Ⅲ = 3；

2、小的数字在大的数字的右边，所表示的数等于这些数字相加得到的数， 如：Ⅷ = 8；Ⅻ = 12；

3、小的数字，（限于Ⅰ、X 和C）在大的数字的左边，所表示的数等于大数减小数得到的数，如：Ⅳ= 4；Ⅸ= 9；

4、正常使用时，连写的数字重复不得超过三次。（表盘上的四点钟“IIII”例外）

5、在一个数的上面画一条横线，表示这个数扩大1000倍。 

**有几条须注意掌握：**

1、基本数字Ⅰ、X 、C 中的任何一个，自身连用构成数目，或者放在大数的右边连用构成数目，都不能超过三个；放在大数的左边只能用一个。

2、不能把基本数字V 、L 、D 中的任何一个作为小数放在大数的左边采用相减的方法构成数目；放在大数的右边采用相加的方式构成数目，只能使用一个。

3、V 和X 左边的小数字只能用Ⅰ。

4、L 和C 左边的小数字只能用X。

5、D 和M 左边的小数字只能用C。 而这道题好就好在没有让我们来验证输入字符串是不是罗马数字，这样省掉不少功夫。我们需要用到map数据结构，来将罗马数字的字母转化为对应的整数值，因为输入的一定是罗马数字，那么我们只要考虑两种情况即可：第一，如果当前数字是最后一个数字，或者之后的数字比它小的话，则加上当前数字第二，其他情况则减去这个数字.

 每次跟后面的数字比较，如果小于等于后面的数字，我们先减去之前加上的数字，如果大于的后面的数字，直接加上当前的数，最后一个数后面没有数了，不会被循环到，直接加上。

### 代码

```java
class Solution {
    public int romanToInt(String s) {
        int nums[] = new int[s.length()];//创建一个s包含的字符的长度的数组
        //给数组每个位置赋值为相对应的值
        for (int i = 0; i < s.length(); i++) {
            switch (s.charAt(i)) {
                case 'M':
                    nums[i] = 1000;
                    break;
                case 'D':
                    nums[i] = 500;
                    break;
                case 'C':
                    nums[i] = 100;
                    break;
                case 'L':
                    nums[i] = 50;
                    break;
                case 'X':
                    nums[i] = 10;
                    break;
                case 'V':
                    nums[i] = 5;
                    break;
                case 'I':
                    nums[i] = 1;
                    break;
            }
        }

        int result = 0;
        for(int i = 0;i < nums.length-1; i++){
            if(nums[i] < nums[i+1])
                result -= nums[i];
            else
                result += nums[i];
        }
        return result + nums[nums.length-1];
    }
}
```

## 12 - Integer to Roman

### 原题概述

Roman numerals are represented by seven different symbols: `I`, `V`, `X`, `L`, `C`, `D` and `M`.

```text
Symbol       Value
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```

For example, two is written as `II` in Roman numeral, just two one's added together. Twelve is written as, `XII`, which is simply `X` + `II`. The number twenty seven is written as `XXVII`, which is `XX` + `V` + `II`.

Roman numerals are usually written largest to smallest from left to right. However, the numeral for four is not `IIII`. Instead, the number four is written as `IV`. Because the one is before the five we subtract it making four. The same principle applies to the number nine, which is written as `IX`. There are six instances where subtraction is used:

* `I` can be placed before `V` \(5\) and `X` \(10\) to make 4 and 9. 
* `X` can be placed before `L` \(50\) and `C` \(100\) to make 40 and 90. 
* `C` can be placed before `D` \(500\) and `M` \(1000\) to make 400 and 900.

Given an integer, convert it to a roman numeral. Input is guaranteed to be within the range from 1 to 3999.

**Example 1:**

```text
Input: 3
Output: "III"
```

**Example 2:**

```text
Input: 4
Output: "IV"
```

**Example 3:**

```text
Input: 9
Output: "IX"
```

**Example 4:**

```text
Input: 58
Output: "LVIII"
Explanation: C = 100, L = 50, XXX = 30 and III = 3.
```

**Example 5:**

```text
Input: 1994
Output: "MCMXCIV"
Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.
```

### 题意和分析

上一道题反过来，数字转换为罗马数字，输入的限制为1～3999，

| 基本字符 | I | V | X | L | C | D | M |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 相应的数字 | 1 | 5 | 10 | 50 | 100 | 500 | 1000  |

因为有输入的限制，所以投机取巧可以建立一个数表，每次查表找出最大的当前最大的数，然后减去再继续查表

### 代码

```java
class Solution {
    public String intToRoman(int num) {
        String result = "";
        int[] val = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        String[] str = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};
        for (int i = 0; i < val.length; i++) {
            while (num >= val[i]) {
                num -= val[i];
                result += str[i];
            }
        }
        return result;
    }
}
```

正常的做法，例如整数 1437 的罗马数字为 MCDXXXVII，千位，百位，十位和个位上的数分别用罗马数字表示了。 1000 - M, 400 - CD, 30 - XXX, 7 - VII。所以我们要做的就是用取商法分别提取各个位上的数字，然后分别表示出来：

100 - C

200 - CC

300 - CCC

400 - CD

500 - D

600 - DC

700 - DCC

800 - DCCC

900 - CM

可以分为四类，100到300一类，400一类，500到800一类，900最后一类。每一位上的情况都是类似的。

```java
class Solution {
    public String intToRoman(int num) {
        String result = "";
        char[] roman = {'M', 'D', 'C', 'L', 'X', 'V', 'I'};
        int[] value = {1000, 500, 100, 50, 10, 5, 1};

        for (int n = 0; n < 7; n += 2) {
            int x = num / value[n];
            if (x < 4) {
                for (int i = 1; i <= x; i++) {
                    result += roman[n];
                }
            } else if (x == 4) {
                result = result + roman[n] + roman[n-1];
            } else if (x > 4 && x < 9) {
                result += roman[n-1];
                for (int i = 6; i <= x; i++) {
                    result += roman[n];
                }
            } else if (x == 9) {
                result = result + roman[n] + roman[n-2];
            }
            num %= value[n];
        }
        return result;
    }
}
```

## 344 - Reverse String

### 原题概述

Write a function that takes a string as input and returns the string reversed.

**Example:**  
Given s = "hello", return "olleh".

### 题意和分析

转换成字符数组，两个索引往中间走，如果相等或者left大于right就停止。

### 代码

```java
class Solution {
    public String reverseString(String s) {
        if (s == null || s.length() == 0 || s.length() == 1) {
            return s;
        }
        char[] result = s.toCharArray();
        int left = 0, right = result.length - 1;
        while (left < right) {
            char temp = result[left];
            result[left] = result[right];
            result[right] = temp;
            left++;
            right--;
        }
        return new String(result);
    }
}
```

## 14 - Longest Common Prefix

### 原题概述

Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string `""`.

**Example 1:**

```text
Input: ["flower","flow","flight"]
Output: "fl"
```

**Example 2:**

```text
Input: ["dog","racecar","car"]
Output: ""
Explanation: There is no common prefix among the input strings.
```

**Note:**

All given inputs are in lowercase letters `a-z`.

### 题意和分析

求一个字符串数组的最长的共同前缀，字符串都是小写字母，这个只能把所有单词排成纵列挨个查了，如果查找的过程中某一个字符串没有了，或者某个字符串的字符不同，那就直接上一轮保存的最长公共前缀。

### 代码

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) {
            return "";
        }
        for (int i = 0; i < strs[0].length(); i++) {//以第一个字符串的长度来遍历，排成纵列
            for (int j = 0; j < strs.length - 1; j++) {//比较到倒数第二个字符串
                if (i >= strs[j].length() || i >= strs[j+1].length() || strs[j].charAt(i) != strs[j+1].charAt(i)) {
                    return strs[j].substring(0, i);//不包括i位置的字符
                }
            }
        }
        return strs[0];//如果都遍历结束了还没有return，那第一个字符串就是最短的字符串（之一），本省就是最长前缀
    }
}
```

## 6 - ZigZag Conversion

### 原题概述

The string `"PAYPALISHIRING"` is written in a zigzag pattern on a given number of rows like this: \(you may want to display this pattern in a fixed font for better legibility\)

```text
P   A   H   N
A P L S I I G
Y   I   R
```

And then read line by line: `"PAHNAPLSIIGYIR"`

Write the code that will take a string and make this conversion given a number of rows:

```text
string convert(string s, int numRows);
```

**Example 1:**

```text
Input: s = "PAYPALISHIRING", numRows = 3
Output: "PAHNAPLSIIGYIR"
```

**Example 2:**

```text
Input: s = "PAYPALISHIRING", numRows = 4
Output: "PINALSIGYAHRPI"
Explanation:

P     I    N
A   L S  I G
Y A   H R
P     I
```

### 题意和分析

Z型打印，从左到右横着读变成"从上到下-斜上-从上到下"这样来读，涉及坐标变化和分块处理。

比如有一个字符串 “0123456789ABCDEF”，转为zigzag

当 n = 2 时：

0 2 4 6 8 A C E

1 3 5 7 9 B D F

当 n = 3 时：

0   4    8     C

1 **3** 5 **7** 9 **B** D **F**

2    6   A     E

当 n = 4 时：

0      6        C

1   **5** 7   **B**  D

2 **4**   8 **A**    E

3      9       F

除了第一行和最后一行没有中间形成之字型的数字外，其他行都有，而首尾两行中相邻两个元素的index之差跟行数是相关的，为 2\*numRows- 2 （注意空格也算一个位置），根据这个特点，我们可以按顺序找到所有的正常元素在原字符串的位置，将他们按顺序加到新字符串里面。对于在正常字元素出现的位置也是有规律的，每个黑体字元素的位置为 j + 2\*numRows-2 - 2\*i，（其中，j为前一个正常字体元素的列数，i为当前行数）。 比如当n = 4中的黑体字5，它的位置为 1 + 2\*4-2 - 2\*1 = 5，为原字符串的正确位置。当我们知道所有正常字体元素和黑体字元素位置的正确算法，那就可以一次性的把它们按顺序都加到新的字符串里面。

### 代码

```java
class Solution {
    public String convert(String s, int numRows) {
        if (numRows <= 1) {
            return s;
        }
        String result = "";
        int size = 2 * numRows - 2;
        for (int i = 0; i < numRows; i++) {//一行一行打印
            for (int j = i; j < s.length(); j += size) {
                result += s.charAt(j);//打印正常字体的字符
                int temp = j + size - 2 * i;
                if ( i != 0 && i != numRows - 1 && temp < s.length()) { //打印斜上的黑体字的字符
                    result += s.charAt(temp);
                }
            }
        }
        return result;
    }
}
```

## 3 - Longest Substring Without Repeating Characters

### 原题概述

Given a string, find the length of the **longest substring** without repeating characters.

**Examples:**

Given `"abcabcbb"`, the answer is `"abc"`, which the length is 3.

Given `"bbbbb"`, the answer is `"b"`, with the length of 1.

Given `"pwwkew"`, the answer is `"wke"`, with the length of 3. Note that the answer must be a **substring**, `"pwke"` is a subsequence and not a substring.

### 题意和分析

给一个字符串，找到其最长的子串（不是子序列），返回这个子串的长度。维护一个滑动窗口，窗口里面的字符都是不重复的。1）首先可以用一个HashMap来记录窗口内的字符和这些字符最后出现的位置，如果窗口右侧移动后发现有重复的字符，那就将left索引指向HashMap里面保存的该字符的位置的下一位，窗口右侧继续移动，同时保持len的最长的值；2）使用HashSet，出现过的字符都放入set中，遇到set中没有的字符就加入set并更新结果result，如果有重复的，从左边开始删除字符，知道删到重复的字符为止。

### 代码

HashMap

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if (s.length() == 0) {
            return 0;
        }
        HashMap<Character, Integer> map = new HashMap<>();
        int result = 0;
        for (int i = 0, j = 0; i < s.length(); i++) {//右边索引遍历字符串,左边记录窗口左边
            if (map.containsKey(s.charAt(i))) {//如果滑动窗口出现重复的字符
                j = Math.max(j, map.get(s.charAt(i)) + 1);
            }
            map.put(s.charAt(i), i);//不管是否移动左边的索引，都将当前的字符存入hashmap
            result = Math.max(result, i - j + 1);
        }
        return result;
    }
}
```

HashSet

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int result = 0, left = 0, right = 0;
        HashSet<Character> ch = new HashSet<>();
        while (right < s.length()) {
            if (!ch.contains(s.charAt(right))) {
                ch.add(s.charAt(right));
                right++;
                result = Math.max(result, ch.size());
            } else {
                ch.remove(s.charAt(left));
                left++;
            }
        }
        return result;
    }
}
```

## 5 - Longest Palindromic Substring

### 原题概述

Given a string **s**, find the longest palindromic substring in **s**. You may assume that the maximum length of **s** is 1000.

**Example 1:**

```text
Input: "babad"
Output: "bab"
Note: "aba" is also a valid answer.
```

**Example 2:**

```text
Input: "cbbd"
Output: "bb"
```

### 题意和分析

找最长回文子串，给定的字符串最长为1000，1\)

1）首先可以O\(n^2\)，以每个字符为中心然后向两边延伸来检查，保留一个最长的子串即可，这个需要注意一下奇偶（以自己为中心以及以自己旁边的两个字符为中心）；

2）优化的办法是用dp，维护一个二维dp，其中dp\[i\]\[j\]表示字符串区间\[i,j\]是否为回文串，当i==j时，只有一个字符，肯定是回文串；如果i = j + 1，那二者是相邻字符，此时判断s\[i\]是否等于s\[j\]，如果i和j不相邻，i-j&gt;=2的时候，除了判断s\[i\]和s\[j\]相等，还得保证dp\[j+1\]\[i-1\]也得为真，才是回文串，所以递推式为

dp\[i, j\] = 1                                               if i == j为回文串

           = s\[i\] == s\[j\]                                if j = i + 1

           = s\[i\] == s\[j\] && dp\[i + 1\]\[j - 1\]    if j &gt; i + 1     

3）O\(n\)的马拉车算法Manacher's Algorithm，[这里](http://www.cnblogs.com/grandyang/p/4475985.html)有详细介绍

### 代码

O\(n^2\)

```java
class Solution {
    private int lo, maxLen;

    public String longestPalindrome(String s) {
        int len = s.length();
        if (len < 2)
            return s;

        for (int i = 0; i < len-1; i++) {
            extendPalindrome(s, i, i);  //assume odd length, try to extend Palindrome as possible
            extendPalindrome(s, i, i+1); //assume even length.
        }
        return s.substring(lo, lo + maxLen);
    }

    private void extendPalindrome(String s, int j, int k) {
        while (j >= 0 && k < s.length() && s.charAt(j) == s.charAt(k)) {
            j--;
            k++;
        }
        if (maxLen < k - j - 1) {
            lo = j + 1;
            maxLen = k - j - 1;
        }
    }
}
```

DP

```java
class Solution {
    public String longestPalindrome(String s) {
        if (s == null || s.length() == 0) {
            return "";
        }
        
        int n = s.length();
        String res = null;

        boolean[][] dp = new boolean[n][n];

        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                dp[i][j] = s.charAt(i) == s.charAt(j) && (j - i < 3 || dp[i + 1][j - 1]);

                if (dp[i][j] && (res == null || j - i + 1 > res.length())) {
                    res = s.substring(i, j + 1);
                }
            }
        }

        return res;
    }
}
```

 Manacher's Algorithm \(to be continued\)



## 38 - Count and Say

### 原题概述

The count-and-say sequence is the sequence of integers with the first five terms as following:

```text
1.     1
2.     11
3.     21
4.     1211
5.     111221
```

`1` is read off as `"one 1"` or `11`.  
`11` is read off as `"two 1s"` or `21`.  
`21` is read off as `"one 2`, then `one 1"` or `1211`.  


Given an integer n, generate the nth term of the count-and-say sequence.

Note: Each term of the sequence of integers will be represented as a string.

**Example 1:**

```text
Input: 1
Output: "1"
```

**Example 2:**

```text
Input: 4
Output: "1211"
```

### 题意和分析

对于前一个数，找出相同元素的个数，把这个“个数”和该元素存到新的string里面， [**字符串中永远只会出现1,2,3这三个字符**，假设第k个字符串中出现了4，那么第k-1个字符串必定有四个相同的字符连续出现，假设这个字符为1，则第k-1个字符串为x1111y。第k-1个字符串是第k-2个字符串的读法，即第k-2个字符串可以读为“x个1,1个1,1个y” 或者“\*个x,1个1,1个1,y个\*”，这两种读法分别可以合并成“x+1个1,1个y” 和 “\*个x，2个1，y个\*”，代表的字符串分别是“\(x+1\)11y” 和 "x21y"，即k-1个字符串为“\(x+1\)11y” 或 "x21y"，不可能为“x1111y”。](http://www.cnblogs.com/TenosDoIt/p/3776356.html)

### 代码

```java
class Solution {
    public String countAndSay(int n) {
        if (n <= 0) {
            return "";
        }
        String result = "1";//如果不为空，至少是1
        while (--n > 0) {
            String cur = "";
            for (int i = 0; i < result.length(); i++) {//遍历result每个字符
                int count = 1;
                while (i + 1 < result.length() && result.charAt(i) == result.charAt(i + 1)) {//数一下总共有几个相同的字符，可以一起说
                    count++;
                    i++;
                }
                cur = cur + count + result.charAt(i);
            }
            result = cur;
        }
        return result;
    }
}
```

## 151 - Reverse Words in a String

### 原题概述

Given an input string, reverse the string word by word.

**Example:**  

```text
Input: "the sky is blue",
Output: "blue is sky the".
```

**Note:**

* A word is defined as a sequence of non-space characters.
* Input string may contain leading or trailing spaces. However, your reversed string should not contain leading or trailing spaces.
* You need to reduce multiple spaces between two words to a single space in the reversed string.

**Follow up:** For C programmers, try to solve it _in-place_ in _O_\(1\) space.

### 题意和分析

只是翻转单词间的顺序，单词自身的字符是不翻转的，可以先翻转整个字符串，然后再翻转每一个单词（当然也可以先分别翻转每一个单词，然后再整个字符串翻转一遍），遇到空格就知道是否一个单词结束了。

如果用stack呢？用空格区分单词，最后一个单词应该怎么处理。。

### 代码

```java
public class Solution {
    public String reverseWords(String s) {
        int storeIndex = 0, n = s.length();
        StringBuilder sb = new StringBuilder(s).reverse();//先翻转整个字符串一次
        for (int i = 0; i < n; i++) {
            if (sb.charAt(i) != ' ') {
                if (storeIndex != 0) sb.setCharAt(storeIndex++, ' ');
                int j = i;
                while (j < n && sb.charAt(j) != ' ') sb.setCharAt(storeIndex++, sb.charAt(j++));
                String t = new StringBuilder(sb.substring(storeIndex - (j - i), storeIndex)).reverse().toString();
                sb.replace(storeIndex - (j - i), storeIndex, t);
                i = j;
            }
        }
        sb.setLength(storeIndex);
        return sb.toString();
    }
}
```

不用StringBuilder

```java
public class Solution {

    public String reverseWords(String s) {
        if (s == null) return null;

        char[] a = s.toCharArray();
        int n = a.length;

        // 第一步，倒转整个字符串
        reverse(a, 0, n - 1);
        // 第二步，倒转每个单词
        reverseWords(a, n);
        // 第三步，清除多余的空格
        return cleanSpaces(a, n);
    }

    void reverseWords(char[] a, int n) {
        int i = 0, j = 0;

        while (i < n) {
            while (i < j || i < n && a[i] == ' ') i++; // skip spaces
            while (j < i || j < n && a[j] != ' ') j++; // skip non spaces
            reverse(a, i, j - 1);                      // reverse the word
        }
    }

    // trim leading, trailing and multiple spaces
    String cleanSpaces(char[] a, int n) {
        int i = 0, j = 0;

        while (j < n) {
            while (j < n && a[j] == ' ') j++;             // skip spaces
            while (j < n && a[j] != ' ') a[i++] = a[j++]; // keep non spaces
            while (j < n && a[j] == ' ') j++;             // skip spaces
            if (j < n) a[i++] = ' ';                      // keep only one space
        }

        return new String(a).substring(0, i);
    }

    // reverse a[] from a[i] to a[j]
    private void reverse(char[] a, int i, int j) {
        while (i < j) {
            char t = a[i];
            a[i++] = a[j];
            a[j--] = t;
        }
    }

}
```

用split来做

```java
public class Solution {

    public String reverseWords(String s) {
        String result = "";
        String[] words = s.trim().split("\\s+");
        for (int i = words.length - 1; i > 0; i--) {
            result += words[i] + " ";
        }
        return result + words[0];//最后加上words[0]，因为上面的循环没有加否则多了个空格
    }
}
```

用Java8的join方法直接拼接字符串

```java
public class Solution {
    public String reverseWords(String s) {
        String[] words = s.trim().split(" +");//用空格符来分隔每一个单词，而不是用正则
        Collections.reverse(Arrays.asList(words));
        return String.join(" ", words);
    }
}
```

## 10 - Regular Expression Matching

### 原题概述

Given an input string \(`s`\) and a pattern \(`p`\), implement regular expression matching with support for `'.'` and `'*'`.

```text
'.' Matches any single character.
'*' Matches zero or more of the preceding element.
```

The matching should cover the **entire** input string \(not partial\).

**Note:**

* `s` could be empty and contains only lowercase letters `a-z`.
* `p` could be empty and contains only lowercase letters `a-z`, and characters like `.` or `*`.

**Example 1:**

```text
Input:
s = "aa"
p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
```

**Example 2:**

```text
Input:
s = "aa"
p = "a*"
Output: true
Explanation: '*' means zero or more of the precedeng element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".
```

**Example 3:**

```text
Input:
s = "ab"
p = ".*"
Output: true
Explanation: ".*" means "zero or more (*) of any character (.)".
```

**Example 4:**

```text
Input:
s = "aab"
p = "c*a*b"
Output: true
Explanation: c can be repeated 0 times, a can be repeated 1 time. Therefore it matches "aab".
```

**Example 5:**

```text
Input:
s = "mississippi"
p = "mis*is*p*."
Output: false
```

### 题意和分析

求两个字符串是否能完全cover。跟44-Wildcard Matching类似，\*的意思略有不同，这道题\*表示0个，1个或者多个，因此a\*b可以表示b，aaab，即任意个a。

递归的办法

如果p为空，s也为空，返回true，否则返回false；p的第二个字符为\*，因为\*之前的字符可以任意，也可以为0，先用递归调用为0的情况，也就是直接把这两个字符去掉再比较；或者当s不为空的时候，并且第一个字符和p的第一个字符相同，把s去掉首字符再与p调用递归（p不能去掉首字符，因为\*前的字符可以无限个）；如果p的第二个字符不为\*，那就比较第一个字符，然后对后面的字符串调用递归。

### 代码

```java
class Solution {
    public boolean isMatch(String s, String p) {
        if (p.length() == 0) {
            return s.length() == 0;
        }
        if (p.length() > 1 && p.charAt(1) == '*') {
            return isMatch(s, p.substring(2)) || (s.length() != 0 && (s.charAt(0) == p.charAt(0) || p.charAt(0) == '.') && isMatch(s.substring(1), p));
        } else {
            return s.length() != 0 && (s.charAt(0) == p.charAt(0) || p.charAt(0) == '.') && isMatch(s.substring(1), p.substring(1));
        }
    }
}
```

DP的方法，

1, If p.charAt\(j\) == s.charAt\(i\) : dp\[i\]\[j\] = dp\[i-1\]\[j-1\]; 

2, If p.charAt\(j\) == '.' : dp\[i\]\[j\] = dp\[i-1\]\[j-1\]; 

3, If p.charAt\(j\) == '_':_ 

_here are two sub conditions:_ 

_1 if p.charAt\(j-1\) != s.charAt\(i\) : dp\[i\]\[j\] = dp\[i\]\[j-2\] //in this case, a_ only counts as empty 

2 if p.charAt\(i-1\) == s.charAt\(i\) or p.charAt\(i-1\) == '.': dp\[i\]\[j\] = dp\[i-1\]\[j\] //in this case, a _counts as multiple a or dp\[i\]\[j\] = dp\[i\]\[j-1\] // in this case, a_ counts as single a or dp\[i\]\[j\] = dp\[i\]\[j-2\] // in this case, a\* counts as empty

```java
class Solution {
    public boolean isMatch(String s, String p) {
        if (s == null || p == null) {
            return false;
        }
        boolean[][] dp = new boolean[s.length()+1][p.length()+1];
        dp[0][0] = true;
        for (int i = 0; i < p.length(); i++) {
            if (p.charAt(i) == '*' && dp[0][i-1]) {
                dp[0][i+1] = true;
            }
        }
        for (int i = 0; i < s.length(); i++) {
            for (int j = 0; j < p.length(); j++) {
                if (p.charAt(j) == '.') {
                    dp[i+1][j+1] = dp[i][j];
                }
                if (p.charAt(j) == s.charAt(i)) {
                    dp[i+1][j+1] = dp[i][j];
                }
                if (p.charAt(j) == '*') {
                    if (p.charAt(j-1) != s.charAt(i) && p.charAt(j-1) != '.') {
                        dp[i+1][j+1] = dp[i+1][j-1];
                    } else {
                        dp[i+1][j+1] = (dp[i+1][j] || dp[i][j+1] || dp[i+1][j-1]);
                    }
                }
            }
        }
        return dp[s.length()][p.length()];
    }
}
```

## 17 - Letter Combinations of a Phone Number

### 原题概述

Given a string containing digits from `2-9` inclusive, return all possible letter combinations that the number could represent.

A mapping of digit to letters \(just like on the telephone buttons\) is given below. Note that 1 does not map to any letters.

![](http://upload.wikimedia.org/wikipedia/commons/thumb/7/73/Telephone-keypad2.svg/200px-Telephone-keypad2.svg.png)

**Example:**

```text
Input: "23"
Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```

**Note:**

Although the above answer is in lexicographical order, your answer could be in any order you want.

### 题意和分析

求电话号码的字母组合，即数字2到9中每个数字可以代表按键上的数个字母，输入是一串数字，求出所有可能的组合，最后返回的结果的顺序不重要。类似题目有90 -  Subsets II, 113 - Path Sum II, 46 - Permutations, 47 - Permutionas II, 77 - Combinations, 39 - Combination Sum, 40 Combination Sum II等。

这道题是经典的 backtracking回溯算法，

> 当一个题目，存在各种满足条件的组合，并且需要把它们全部列出来，就可以考虑backtracking了，不过注意，backtracking一定程度上属于穷举，当数据特别大的时候吧不合适，这时通常应该考虑DP。

先创建一个mapping，对应所有的键，比如输入"23"，2对应"abc"，3对应"def"，在递归的时候先确定2对应的字母之一，比如a，然后

### 代码

```java
class Solution {
    public List<String> letterCombinations(String digits) {

        //把电话键盘上数字对应的字母列出来，比如输入为2，对应"abc"
        String[] mapping = new String[] {"0","1","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"};
        List<String> list = new ArrayList<String>();

        letterCombinations(list,digits,"",0,mapping);//index从0开始

        return list;
    }

    private void letterCombinations (List<String> list, String digits,
                                     String curr, int index,String[] mapping) {
        //最后一层退出条件
        if (index == digits.length()) {
            if(curr.length() != 0) list.add(curr);
            return;
        }

        //找到数字对应的字符串
        String temp = mapping[digits.charAt(index) - '0'];
        for (int i = 0; i < temp.length(); i++) {
            //每次循环把不同字符串加到当前curr之后
            String next = curr + temp.charAt(i);
            //进入下一层
            letterCombinations(list,digits,next,index+1,mapping);
        }
    }
}
```

迭代的办法则可以用BFS或者DFS来做，下面是BFS是使用队列

```java
class Solution {
    public List<String> letterCombinations(String digits) {
        LinkedList<String> result = new LinkedList<>();
        if (digits.length() == 0) {
            return result;
        }
        //把电话键盘上数字对应的字母列出来，比如输入为2，对应"abc"
        String[] mapping = new String[]{"0","1","abc","def","ghi","mno","pqrs","tuv","wxyz"};
        result.add("");
        while(result.peek().length() != digits.length()) {
            String remove = result.remove();
            String map = mapping[digits.charAt(remove.length()) - '0'];
            for (char c : map.toCharArray()) {
                result.addLast(remove + c);
            }
        }
        return result;
    }
}class Solution {
    public List<String> letterCombinations(String digits) {
        LinkedList<String> result = new LinkedList<>();
        if (digits.length() == 0) {
            return result;
        }
        //把电话键盘上数字对应的字母列出来，比如输入为2，对应"abc"
        String[] mapping = new String[]{"0","1","abc","def","ghi","mno","pqrs","tuv","wxyz"};
        result.add("");
        while(result.peek().length() != digits.length()) {
            String remove = result.remove();
            String map = mapping[digits.charAt(remove.length()) - '0'];
            for (char c : map.toCharArray()) {
                result.addLast(remove + c);
            }
        }
        return result;
    }
}
```

## 22 - Generate Parentheses

### 原题概述

Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

For example, given n = 3, a solution set is:

```text
[
  "((()))",
  "(()())",
  "(())()",
  "()(())",
  "()()()"
]
```

### 题意和分析

给一个数字n，让生成n个括号的所有正确形式，列出所有组合的形式一般可以用递归（而不是动态规划）， 比如n为3，由于字符串只有左括号和右括号两种字符，而且最终结果必定是左括号3个，右括号3个，所以定义两个变量left和right分别表示剩余左右括号的个数，如果在某次递归时，左括号的个数大于右括号的个数，说明此时生成的字符串中右括号的个数大于左括号的个数，即会出现'\)\('这样的非法串，所以这种情况直接返回，不继续处理。如果left和right都为0，则说明此时生成的字符串已有3个左括号和3个右括号，且字符串合法，则存入结果中后返回；如果以上两种情况都不满足，若此时left大于0，则调用递归函数，注意参数的更新，若right大于0，则调用递归函数，同样要更新参数。

### 代码

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> result = new ArrayList<>();
        helper(n, n, "", result);//先传入初始状态，再递归
        return result;
    }
    private void helper(int left, int right, String out, List<String> result) {
        if (left < 0 || right < 0 || left > right) {//已经取完甚至超过了，所以直接返回，left>right表示')('成对
            return;
        }
        if (left == 0 && right == 0) {//左右括号都取完了
            result.add(out);
            return;
        }

        //仍在继续下一层
        helper(left-1, right, out + '(', result);//这一层拿出左括号，下一层少一个左括号递归
        helper(left, right-1, out + ')', result);//这一层拿出右括号，下一层少一个右括号递归
    }
}
```

CareerCup书上给的方法，这种方法的思想是找左括号，每找到一个左括号，就在其后面加一个完整的括号，最后再在开头加一个\(\)，就形成了所有的情况，需要注意的是，有时候会出现重复的情况，所以用set数据结构，这样如果遇到重复项，不会加入到结果中，最后我们再把set转为List即可，

n＝1:    \(\)

n=2:    \(\(\)\)    \(\)\(\)

n=3:    \(\(\)\(\)\)    \(\(\(\)\)\)    \(\)\(\(\)\)    \(\(\)\)\(\)    \(\)\(\)\(\)   

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        Set<String> result = new HashSet<>();
        if (n == 0) {
            result.add("");
        } else {
            List<String> pre = generateParenthesis(n - 1);//在递归前一层的基础上加上()
            for (String str : pre) {
                for (int i = 0; i < str.length(); i++) {
                    if (str.charAt(i) == '(') {
                        str = str.substring(0, i + 1) + "()" + str.substring(i+1, str.length());
                        result.add(str);
                        str = str.substring(0, i+1) + str.substring(i + 3, str.length());
                    }
                }
                result .add("()" + str);
            }
        }
        return new ArrayList(result);
    }
}
```

## 67 - Add Binary

### 原题概述

Given two binary strings, return their sum \(also a binary string\).

The input strings are both **non-empty** and contains only characters `1` or `0`.

**Example 1:**

```text
Input: a = "11", b = "1"
Output: "100"
```

**Example 2:**

```text
Input: a = "1010", b = "1011"
Output: "10101"
```

### 题意和分析

将两个string形式的二进制数相加，并且保存在string中，要注意的是如何将string和int之间互相转换，并且每位相加时，会有进位的可能，会影响之后相加的结果。而且两个输入string的长度也可能会不同。创建一个StringBuilder来存相加的结果，从两个string的末尾开始取出字符，然后转为数字，相加，如果大于等于2，则标记进位标志carry，最后将StringBuilder转换成String，因为是从后面开始加，翻转一下。

Time：O\(m\)； Space：O\(m\)，m是较长的那个string的长度。

### 代码

```java
class Solution {
    public String addBinary(String a, String b) {
        if (a == null || b == null) {
            return a == null ? b : a;
        }
        int aIndex = a.length() - 1, bIndex = b.length() - 1, carry = 0;
        StringBuilder sb = new StringBuilder();
        while (aIndex >= 0 || bIndex >= 0) {
            int sum = carry;//每轮两个数相加的和
            if (aIndex >= 0) {
                sum += a.charAt(aIndex) - '0';//转换成二进制数
                aIndex--;
            }
            if (bIndex >= 0) {
                sum += b.charAt(bIndex) - '0';//转换成二进制数
                bIndex--;
            }
            sb.append(sum % 2);
            carry = sum / 2;
        }
        if (carry != 0) {
            sb.append(carry);
        }

        return sb.reverse().toString();
    }
}
```

## 49 - Group Anagrams

### 原题概述

Given an array of strings, group anagrams together.

**Example:**

```text
Input: ["eat", "tea", "tan", "ate", "nat", "bat"],
Output:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```

**Note:**

* All inputs will be in lowercase.
* The order of your output does not matter.

### 题意和分析

给一个字符串数组，把所有的anagrams异构体归在一起，最后返回每个anagram的组，首先可以遍历整个个字符串数组，每遇到一个字符串就对其排序，如果同为anagrams排序后会是同样的字符串，依次字符串为key，以一个anagram下的 字符串列表为value，然后从hashmap中打印出所有的值即可；

改进的办法不用排序， 每个字母对应一个质数，将一个字符串中的字母对应的质数做乘法，异构体所得的乘积一定相同。

### 代码

排序的办法

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        if (strs == null || strs.length == 0) {
            return new ArrayList<List<String>>();
        }

        HashMap<String, List<String>> map = new HashMap<>();
        for (String s : strs) {
            char[] cha = s.toCharArray();
            Arrays.sort(cha);//对字符串转换的字符数组进行排序
            String keyStr = String.valueOf(cha);//字符数组转换成字符串的key
            if (!map.containsKey(keyStr)) {//第一次出现就新增一个key
                map.put(keyStr, new ArrayList<String>());
            }
            map.get(keyStr).add(s);
        }
        return new ArrayList<List<String>>(map.values());//map.values()获得HashMap的所有的值
    }
}
```

利用质数做乘积找key

```java
class Solution {
    public static List<List<String>> groupAnagrams(String[] strs) {
        if (strs == null || strs.length == 0) {
            return new ArrayList<List<String>>();
        }

        // 用质数作为每个字母的唯一标识key，然后做乘法，异构体所的一定相同，最多10609个
        int[] prime = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101, 103};

        HashMap<Integer, List<String>> map = new HashMap<>();
        for (String s : strs) {
            int key = 1;
            for (char c : s.toCharArray()) {//获得质数乘积做成的key，异构体的key一样
                key *= prime[c - 'a'];//找到prime中对应的质数
            }

            if (!map.containsKey(key)) {
                map.put(key, new ArrayList<String>());
            }
            map.get(key).add(s);
        }
        return new ArrayList<List<String>>(map.values());
    }
}
```

## 657 Judge Route Cycle

### 原题概述

Initially, there is a Robot at position \(0, 0\). Given a sequence of its moves, judge if this robot makes a circle, which means it moves back to **the original place**.

The move sequence is represented by a string. And each move is represent by a character. The valid robot moves are `R` \(Right\), `L`\(Left\), `U` \(Up\) and `D` \(down\). The output should be true or false representing whether the robot makes a circle.

**Example 1:**  


```text
Input: "UD"
Output: true
```

**Example 2:**  


```text
Input: "LL"
Output: false
```

### 题意和分析

找是否绕圈，有多少个U就得有多少个D，有多少个L就得有多少个R，跟20 - Valid Parentheses类似，那道题用stack来解，这个题更简单，两个计数器来看最后是否为0就行了。

### 代码

```java
class Solution {
    public boolean judgeCircle(String moves) {
        if (moves == null || moves.length() == 0) {
            return true;
        }
        int countH = 0, countV = 0;
        for (int i = 0; i < moves.length(); i++) {
            if (moves.charAt(i) == 'L') countH++;
            if (moves.charAt(i) == 'R') countH--;
            if (moves.charAt(i) == 'U') countV++;
            if (moves.charAt(i) == 'D') countV--;
        }
        return countH == 0 && countV == 0;
    }
}
```

## 8 - String to Integer\(atoi\)

### 原题概述

Implement `atoi` which converts a string to an integer.

The function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character, takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.

The string can contain additional characters after those that form the integral number, which are ignored and have no effect on the behavior of this function.

If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.

If no valid conversion could be performed, a zero value is returned.

**Note:**

* Only the space character `' '` is considered as whitespace character.
* Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: \[−231,  231 − 1\]. If the numerical value is out of the range of representable values, INT\_MAX \(231 − 1\) or INT\_MIN \(−231\) is returned.

**Example 1:**

```text
Input: "42"
Output: 42
```

**Example 2:**

```text
Input: "   -42"
Output: -42
Explanation: The first non-whitespace character is '-', which is the minus sign.
             Then take as many numerical digits as possible, which gets 42.
```

**Example 3:**

```text
Input: "4193 with words"
Output: 4193
Explanation: Conversion stops at digit '3' as the next character is not a numerical digit.
```

**Example 4:**

```text
Input: "words and 987"
Output: 0
Explanation: The first non-whitespace character is 'w', which is not a numerical 
             digit or a +/- sign. Therefore no valid conversion could be performed.
```

**Example 5:**

```text
Input: "-91283472332"
Output: -2147483648
Explanation: The number "-91283472332" is out of the range of a 32-bit signed integer.
             Thefore INT_MIN (−231) is returned.
```

### 题意和分析

实现一个atoi函数（AscII to Interger），主要考虑各种边界条件，包括正负号，越界，空格，精度等。

### 代码

```java
class Solution {
    public int myAtoi(String str) {
        if (str == null) {
            return 0;
        }
        //处理空格
        str = str.trim();

        //输入有可能全是空格，经过trim处理后变成了空
        if (str.length() == 0) {
            return 0;
        }

        //处理正负号（可能有可能也没有）
        char flag = '+';
        int i = 0;//记录字符串的索引
        if (str.charAt(0) == '-') {
            flag = '-';
            i++;//挪到正/负号的下一位
        } else if (str.charAt(0) == '+') {
            i++;
        }

        //使用double来保存结果
        double result = 0;
        //检查条件，i索引不越界，且值在0到9之间
        while (str.length() > i && str.charAt(i) >= '0' && str.charAt(i) <= '9') {
            result = result * 10 + (str.charAt(i) - '0');//从左到右每次取一位字符来计算
            i++;//向右移动索引
        }

        //如果flag是'-'，是负数，在前面加上负号
        if (flag == '-') {
            result = -result;
        }

        //处理越界问题
        if (result > Integer.MAX_VALUE) {
            return Integer.MAX_VALUE;
        }
        if (result < Integer.MIN_VALUE) {
            return Integer.MIN_VALUE;
        }

        return (int)result;
    }
}
```

可以把判断是否确界的条件写得精妙一点，基于两条：

1. Integer.MAX\_VALUE/10 &lt; result; //当前转换结果比Integer中最大值除以10还大（因为这个判断放在while循环最开始，之后还要对result进行\*10+当前遍历元素的操作，所以如果还乘10的result已经比Integer.MAX\_VALUE/10还大，可想而知，乘了10更大）；
2. Integer.MAX\_VALUE/10 == result && Integer.MAX\_VALUE%10 &lt; \(str.charAt\(i\) - '0'\) //另外的一个情况就是，当前result恰好跟 Integer.MAX\_VALUE/10相等，那么就判断当前遍历的元素值跟最大值的最后一位的大小关系即可。

```java
class Solution {
    public int myAtoi(String str) {
        if (str == null) {
            return 0;
        }
        //处理空格
        str = str.trim();

        //输入有可能全是空格，经过trim处理后变成了空
        if (str.length() == 0) {
            return 0;
        }

        //处理正负号（可能有可能也没有）
        char flag = '+';
        int i = 0;//记录字符串的索引
        if (str.charAt(0) == '-') {
            flag = '-';
            i++;//挪到正/负号的下一位
        } else if (str.charAt(0) == '+') {
            i++;
        }

        //使用double来保存结果
        double result = 0;
        while (str.length() > i && str.charAt(i) >= '0' && str.charAt(i) <= '9') {
            result = result * 10 + (str.charAt(i) - '0');//从左到右每次取一位字符来计算
            i++;//向右移动索引到末尾
        }

        //处理越界问题，这里把正负号处理挪到后面，就暂时只用和最大值比较，下一步再加上可能的负号
        while (str.length() > i && str.charAt(i) >= '0' && str.charAt(i) <= '9') {
            if (Integer.MAX_VALUE/10 < result || //与最大值除以10比较
                    (Integer.MAX_VALUE/10 == result && Integer.MAX_VALUE%10 < (str.charAt(i) - '0'))) {//与最大值的最后一位比较看是否相同
                return flag == '-' ? Integer.MIN_VALUE : Integer.MAX_VALUE;//越界，返回最大/最小值
            }
            result = result * 10 + (str.charAt(i) - '0');//不越界，正常计算
            i++;
        }

        //如果flag是'-'，是负数，在前面加上负号
        if (flag == '-') {
            result = -result;
        }

        return (int)result;
    }
}
```

## 28 - Implement substr

### 原题概述

Implement [strStr\(\)](http://www.cplusplus.com/reference/cstring/strstr/).

Return the index of the first occurrence of needle in haystack, or **-1** if needle is not part of haystack.

**Example 1:**

```text
Input: haystack = "hello", needle = "ll"
Output: 2
```

**Example 2:**

```text
Input: haystack = "aaaaa", needle = "bba"
Output: -1
```

**Clarification:**

What should we return when `needle` is an empty string? This is a great question to ask during an interview.

For the purpose of this problem, we will return 0 when `needle` is an empty string. This is consistent to C's [strstr\(\)](http://www.cplusplus.com/reference/cstring/strstr/) and Java's [indexOf\(\)](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#indexOf%28java.lang.String%29).

### 题意和分析

在一个字符串中找另一个字符串第一次出现的位置，有以下两种边界情况：如果子字符串为空，则返回0；如果子字符串长度大于母字符串长度，则返回-1。 

开始遍历母字符串，我们并不需要遍历整个母字符串，而是遍历到剩下的长度和子字符串相等的位置即可，这样可以提高运算效率。对于遍历到的每一个字符，都遍历一遍子字符串，一个一个字符的对应比较，如果对应位置有不等的，则跳出循环，如果一直都没有跳出循环，则说明子字符串出现了，则返回起始位置即可。

至于KMP算法这里就不说了。

### 代码

```java
class Solution {
    public int strStr(String haystack, String needle) {
        if (needle == null || needle.length() == 0) {
            return 0;
        }
        int m = haystack.length(), n = needle.length();
        if (n > m) {
            return -1;
        }
        for (int i = 0; i <= m - n; i++) {//注意这里是<=，想想比如n==1，那就可能检查到m的最后一位
            int j = 0;
            for (j = 0; j < n; j++) {
                if (haystack.charAt(i+j) != needle.charAt(j)) {
                    break;//break里面的for循环
                }
            }
            if (j == n) {//没有break
                return i;
            }
        }
        return -1;//没找到
    }
}
```

## 557 - Reverse Words in a String III

### 原题概述

Given a string, you need to reverse the order of characters in each word within a sentence while still preserving whitespace and initial word order.

**Example 1:**  


```text
Input: "Let's take LeetCode contest"
Output: "s'teL ekat edoCteeL tsetnoc"
```

**Note:** In the string, each word is separated by single space and there will not be any extra space in the string.

### 题意和分析

只翻转字符串中每个单词的顺序，每个单词都只由一个空格隔开。用split分成一个字符串数组，然后用StringBuilder分别翻转每个单词；

如果不用StringBuilder的内置reverse方法也可以用setCharAt手动实现reverse，或者toCharArray来用数组翻转。

### 代码

```java
class Solution {
    public String reverseWords(String s) {
        String[] words = s.split(" ");
        for (int i = 0; i < words.length; i++) {
            words[i] = new StringBuilder(words[i]).reverse().toString();//用内置方法进行翻转
        }
        StringBuilder result = new StringBuilder();
        for (String word : words) {
            result.append(word + " ");
        }
        return result.toString().trim();//去掉最后一个单词后面的空格
    }
}
```

## 383 - Ransom Note

### 原题概述



### 题意和分析

### 代码

## 804 - Unique Morse Code Words

## 43 - Multiply Strings

## 387 - First Unique Character in a String

## 345 - Reverse Vowels of a String

## 227 - Basic Calculator II

### 原题概述

Implement a basic calculator to evaluate a simple expression string.

The expression string contains only **non-negative** integers, `+`, `-`, `*`, `/` operators and empty spaces . The integer division should truncate toward zero.

**Example 1:**

```text
Input: "3+2*2"
Output: 7
```

**Example 2:**

```text
Input: " 3/2 "
Output: 1
```

**Example 3:**

```text
Input: " 3+5 / 2 "
Output: 5
```

**Note:**

* You may assume that the given expression is always valid.
* **Do not** use the `eval` built-in library function.

### 题意和分析

设计一个计算器，输入一个有效的字符串表达式，加减乘除后输出一个整数，除法是地板除法，表达式里的数字都是正整数。eval\(\)是脚本语言中的方法，Java并没有。

相对于I中只有加减，这道题多了乘除，并且没有括号，所以简化了。使用stack来存储计算的中间结果，每次遇到加减法就直接压入；遇到乘除法就先计算一下再压入。

时间上一遍扫过去O\(n\)，空间上开了一个stack，O\(n\)。

当然这道题不用stack也可以，可以把string先转换成char array，然后从左到右运算，维持一个result负责最终结果和一个pre负责遇到乘法除法的时候重算一下先做乘除。复杂度一样。

### 代码

Stack

```java
class Solution {
    public int calculate(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        Stack<Integer> stack = new Stack<>();
        int num = 0;//转换数字所用，初始为0
        char sign = '+'; //记录本次数据之前的运算符号，初始值为'+'
        for (int i = 0; i <s.length(); i++) {
            if (Character.isDigit(s.charAt(i))) {//如果是数字，连续出现就会每次乘以10，比如8+123*5
                num = num * 10 + s.charAt(i) - '0';
            }

            if ((!Character.isDigit(s.charAt(i))//非数字则是运算符，先乘除再加减
                    && ' ' != s.charAt(i)) //空格就跳过
                    || i == s.length() - 1) { //到达字符串的最后，这时候是数字，但是需要计算最后一次的结果
                //加减在这个时候不计算结果，直接压入stack，到最后的时候再计算
                if (sign == '+') {
                    stack.push(num);
                }
                if (sign == '-') {//待会加一个负数即可
                    stack.push(-num);
                }

                //乘除从stack中弹出一个数字先计算结果后，再将这个结果压入stack，因为没有括号，所以把乘除运算符的前后两个数拿来运算即可
                if (sign == '*') {
                    stack.push(stack.pop() * num);
                }
                if (sign == '/') {
                    stack.push(stack.pop() / num);
                }
                sign = s.charAt(i);//这时候的字符是运算符，记录下准备下一轮遇到数字再判断
                num = 0; //遇到运算符将num归零，下次循环遇到数字再来换算
            }
        }

        int result = 0;
        //这时候stack里面都是正数（+）或负数（-），直接全部加起来即可
        for (int i : stack) {
            result += i;
        }
        return result;
    }
}
```

用变量来维持

```java
class Solution {
    public int calculate(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }
        int result = 0, previous = 0, num = 0;
        char sign = '+';
        char[] array = s.trim().toCharArray();
        for (int i = 0; i <= array.length; i++) {//<=表示最后还得运算一次
            if (i != array.length && Character.isDigit(array[i])) {
                num = num * 10 + array[i] - '0';
            } else {
                if (i != array.length && array[i] == ' ') {
                    continue;
                }
                if (sign == '+') {
                    result += num;
                    previous = num;
                }
                if (sign == '-') {
                    result -= num;
                    previous = -num;
                }
                if (sign == '*') {
                    result = result - previous + previous * num;//之前多加了一次num，因为之前不知道后面的运算符，所以先加了这里再减了再做乘法
                    previous = previous * num;//乘法运算符两边的数相乘为下一轮当前数的previous
                }
                if (sign == '/') {
                    result = result - previous + previous / num;
                    previous = previous / num;
                }
                num = 0;
                if (i != array.length) {
                    sign = array[i];
                }
            }
        }

        return result;
    }
}
```



## 316 - Remove Duplicate Letters

### 原题概述

Given a string which contains only lowercase letters, remove duplicate letters so that every letter appear once and only once. You must make sure your result is the smallest in lexicographical order among all possible results.

**Example 1:**

```text
Input: "bcabc"
Output: "abc"
```

**Example 2:**

```text
Input: "cbacdcbc"
Output: "acdb"
```

### 题意和分析

移除重复字母，使每个字母只出现一次，结果按照字母顺序排，而且不能打乱原来的相对位置。

递归的办法，先用哈希表记录每个字母出现的次数，再遍历给定字符串s，找出最小的字母，每比较一个字母，在哈希表中的值减1，如果此时为0了，则不继续遍历了，此时我们记录了一个位置，把字符串s中该位置左边的字符都删掉，右边的所有再出现的该字母也删掉，递归调用此函数即可。时间： O\(26 \* n\) = O\(n\)。

### 代码

```java
class Solution {
    public String removeDuplicateLetters(String s) {
        int[] count = new int[26];
        int pos = 0;//最小的s[i]的位置
        for (int i = 0; i < s.length(); i++) {
            count[s.charAt(i) - 'a']++;
        }
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) < s.charAt(pos)) {
                pos = i;
            }
            if (--count[s.charAt(i) - 'a'] == 0) {
                break;
            }
        }
        return s.length() == 0 ? "" : s.charAt(pos) + removeDuplicateLetters(s.substring(pos + 1).replaceAll("" + s.charAt(pos), ""));
    }
}
```


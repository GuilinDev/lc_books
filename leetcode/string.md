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
        //给新创建的数组每个位置赋值为相对应的值，方便查找
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
        for(int i = 0; i < nums.length - 1; i++){
            if(nums[i] < nums[i + 1]) // 当前位置为IV IX这样的情况
                result -= nums[i];
            else  // 当前位置为VI XI这样的情况，或者相等
                result += nums[i];
        }
        return result + nums[nums.length - 1]; //最后一位单独加上
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

穷举法来对应

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
        if(numRows <= 1) {
            return s;
        }
        int size = 2 * numRows - 2;
        int len = s.length();
        StringBuilder sb = new StringBuilder();
        
        for (int i = 0; i < numRows; i++) { // 逐行打印
            for (int j = i; j < len; j += size) { // j的值每次跳过行数
                sb.append(s.charAt(j));
                int position = j + size - i * 2; // 计算斜上字符的坐标
                if (i != 0 && i != numRows - 1 && position < len) { // 将斜上字符加入到结果中
                    sb.append(s.charAt(position));
                }
            }
        }
        return sb.toString();
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

给一个字符串，找到其最长的子串（不是子序列），返回这个子串的长度。维护一个滑动窗口，窗口里面的字符都是不重复的。

1）首先可以用一个HashMap来记录窗口内的字符和这些字符最后出现的位置，如果窗口右侧移动后发现有重复的字符，那就将left索引指向HashMap里面保存的该字符的位置的下一位，窗口右侧继续移动，同时保持len的最长的值；

2）使用HashSet，出现过的字符都放入set中，遇到set中没有的字符就加入set并更新结果result，如果有重复的，从左边开始删除字符，知道删到重复的字符为止。

3）两个索引的滑动窗口，左索引和右索引，当右边新进来字符时，检查有没有重复（用一个array来装），没有就直接加入，有的话一直删除最左边的字符直到没有重复，这个效率最高。

### 代码

HashMap

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        HashMap<Character, Integer> map = new HashMap<>(); // k-v: 字符-最新位置
        int result = 0;
        
        for (int left = 0, right = 0; right < s.length(); right++) {
            if (map.containsKey(s.charAt(right))) {
                left = Math.max(left, map.get(s.charAt(right)) + 1); // 更新一下把left的位置+1
            }
            map.put(s.charAt(right), right); //不管是否移动左边的索引，都将当前的字符存入hashmap
            result = Math.max(result, right - left + 1);
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

两个索引的滑动窗口用array来存

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int[] freq = new int[256];
        int left = 0;
        int right = -1; // 刚开始设定窗口里面什么都没有
        int result = 0;
        
        while (right + 1 < s.length()) {
            if (right + 1 < s.length() && freq[s.charAt(right + 1)] == 0) {
                freq[s.charAt(right + 1)] = 1;
                right++;
            } else { //freq[s.charAt(right + 1)] == 1
                //移掉一个左边的字符，并减掉相应的freq
                //一直到减掉重复的字符之前，虽然有重复的字符，但中间不会是最长的子字符串
                freq[s.charAt(left)]--;
                left++;
            }
            result = Math.max(result, right - left + 1);
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

2）用dp，维护一个二维dp，**其中dp\[i\]\[j\]表示字符串区间\[i, j\]是否为回文串**，当i==j时，只有一个字符，肯定是回文串；如果i = j + 1，那二者是相邻字符，此时判断s\[i\]是否等于s\[j\]即可；如果i和j不相邻，也就是i - j &gt;= 2的时候，除了判断s\[i\]和s\[j\]相等，还得保证dp\[j+1\]\[i-1\]也得为真，才是回文串，所以递推式为

dp\[i, j\] = 1                                               if i == j为回文串

           = s\[i\] == s\[j\]                                if j = i + 1

           = s\[i\] == s\[j\] && dp\[i + 1\]\[j - 1\]    if j &gt; i + 1     

3）O\(n\)的马拉车算法Manacher's Algorithm，O\(n\)时间，[这里](http://www.cnblogs.com/grandyang/p/4475985.html)有详细介绍，这个看情况掌握。

### 代码

O\(n^2\)

```java
class Solution {
    private int lo, maxLen;

    public String longestPalindrome(String s) {
        int len = s.length();
        if (len < 2)
            return s;

        for (int i = 0; i < len - 1; i++) {
            extendPalindrome(s, i, i);  //assume odd length, try to extend Palindrome as possible
            extendPalindrome(s, i, i + 1); //assume even length.
        }
        return s.substring(lo, lo + maxLen);
    }

    private void extendPalindrome(String s, int left, int right) {//字符串和两个指针
        while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            left--;
            right++;
        }
        //注意上面while循环是在左右不等才停止的，当前的maxLen是成员变量，需要维持奇偶中大的一个（较小的不进循环）
        if (maxLen < right - left - 1) {
            lo = left + 1;//回文子字符串的下标
            maxLen = right - left - 1;//回文子字符串的上标
        }
    }
}
```

DP，也是O\(n ^ 2\)

```java
class Solution {
    public String longestPalindrome(String s) {
        int len = s.length();
        int maxLen = 0;
        boolean[][] memo = new boolean[len][len];
        String result = "";
        
        for (int left = len - 1; left >= 0; left--) { // left,right为区间[left, right]最长回文
            for (int right = left; right < len; right++) {// right必须大于等于left，所以left从右到左，right从左到右比较好写
                if (s.charAt(left) == s.charAt(right)) { // 两个字符相等
                    if (right - left <= 2) { // left和right重合或者相邻，或者中间隔一个字符
                        memo[left][right] = true;
                    } else {
                        memo[left][right] = memo[left + 1][right - 1]; // left和right对应字符相等的情况下，取决与上一个状态
                    }
                }
                if (memo[left][right] && maxLen < right - left + 1) { // 计算当前dp对应的最长字符串
                    maxLen = right - left + 1;
                    result = s.substring(left, left + maxLen);
                }
            }
        }
        return result;
    }
}
```

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

求两个字符串是否能完全cover。跟44-Wildcard Matching类似，\*的意思略有不同，这道题\*表示0个，1个或者多个前面的字符（包括'.'），因此a\*b可以表示b，ab或aaab，即任意个a。

1）递归的办法，递归过程中的三种情况：

I - 如果p为空，s也为空，返回true，否则返回false，此为基线条件；

II - p和s不为空，且p的第二个字符为\*，因为\*之前的字符可以0个或任意正整数个，所以首先用递归调用\*代表0的情况，也就是直接把\*和它之前的这两个字符去掉再和s比较；而如果当s的第一个字符和p的第一个字符相同，或者p第一个字符等于‘.’（仅代表任意1个字符，可以认为.==s.charAt\(0\)），把s去掉首字符并递归检查。

III - p和s不为空，且递归过程中p的第二个字符不为\*，那就如同正常字符串一样比较第一个字符，然后对后面的字符串调用递归。

2） DP的方法

如果能写成递归，基本就能改成动态规划，因为是两个字符串，这里需要声明一个二维数组布尔数组dp\[i\]\[j\]

dp\[i\]\[j\]：**表示字符串中s中的前i个字符与字符串p中的前j个字符是否能够匹配**。如果能匹配则为true，反之为false。

**假如**已经算出了前i-1个字符与前j-1个字符的匹配情况了，那如何计算dp\[i\]\[j\]呢？

* 如果s\[i\] == p\[j\]，说明dp\[i\]\[j\]取决于dp\[i-1\]\[j-1\] 
* 如果s\[i\] != p\[j\]，说明两个字符串不匹配 

_对于 '.' 和 '\*'的处理_ 

由于'.' 和 '\*'都是在p中，所以

* 当p\[j\] == '.'的时候，说明这个字符什么都可以当，和之前s\[i\] == p\[j\]是一样的，故dp\[i\]\[j\] == dp\[i - 1\]\[j - 1\].
* 当p\[j\] == '\*' 的时候这要分两种情况：
  * 如果前面的字符p\[j - 1\]能与s当前的字符s\[i\]匹配上的话，那就dp\[i\]\[j\]的状态就取决于dp\[ i - 1\]\[j\]，也是就相当于看前面的状态。
  * 如果前面的字符p\[j - 1\]不能与s当前的字符s\[i\]匹配上的话，是不是就代表匹配失败了呢？不一定，因为这毕竟是一个 \* 号而不是真正的要匹配的字符，说白了大不了不用它来匹配了，也就是使用0次，那就dp\[i\]\[j\]的状态就去取决于dp\[i\]\[j - 2\]。上面这两种状态只要能满足其中一种就可以了，即：

![](../.gitbook/assets/image%20%2863%29.png)

### 代码

递归

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

DP

```java
class Solution {
    public boolean isMatch(String s, String p) {
        int lenS = s.length(), lenP = p.length();
        boolean[][] dp = new boolean[lenS + 1][lenP + 1]; //多一个初始状态，都为0的情况
        dp[0][0] = true;
        
        for (int j = 2; j <= lenP; j++) { // 先把"*"处理了
            if (p.charAt(j - 1) == '*') {
                dp[0][j] = dp[0][j - 2];
            }
        }
        
        for (int i = 1; i <= lenS; i++) {
            for (int j = 1; j <= lenP; j++) {
                if (match(s.charAt(i - 1), p.charAt(j - 1))) { // 前面两个字符相匹配
                    dp[i][j] = dp[i - 1][j - 1];
                } else if (p.charAt(j - 1) == '*') { // p中前面一个字符为'*'，分两种情况
                    dp[i][j] = dp[i][j - 2]; // 情况1
                    if (match(s.charAt(i - 1), p.charAt(j - 2))) { // 情况2
                        dp[i][j] |= dp[i - 1][j]; // |=表示可能dp[i][j]之前'*'处理过
                    }
                }
            }
        }
        return dp[lenS][lenP];
    }
    private boolean match(char a, char b) {
        return a == b || b == '.';
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

先创建一个mapping，对应所有的键，比如输入"23"，2对应"abc"，3对应"def"，在递归的时候先确定2对应的字母之一，比如a，然后按照回溯模板的做法来即可。

### 代码

```java
class Solution {
    // 类变量，也可以把这些作为参数参数helper function
    List<String> results;
    StringBuilder oneResult;
    String[] buttons;
    public List<String> letterCombinations(String digits) {
        results = new ArrayList<>();
        if (digits.isEmpty()) { // corner cases
            return results;
        }
        oneResult = new StringBuilder();
        // 方便查找字符
        buttons = new String[]{"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        
        dfs(0, digits);
        return results;
    }
    private void dfs(int curLen, String digits) {
        if (oneResult.length() == digits.length()) {
            results.add(oneResult.toString());
            return;
        } 
        for (char ch : buttons[digits.charAt(curLen) - '0'].toCharArray()) { //遍历根据输入数字找到的某个字符串的所有字符
            oneResult.append(ch);
            dfs(curLen + 1, digits);
            //回溯
            oneResult.deleteCharAt(oneResult.length() - 1);
        }
    }
}
```

迭代的办法则可以用BFS或者DFS来做，下面是BFS是使用队列

```java
class Solution {
    public List<String> letterCombinations(String digits) {
        LinkedList<String> result = new LinkedList<>();//创建一个queue
        if (digits.isEmpty()) {
            return result;
        }
        
        String[] mapping = new String[]{"0","1","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"};
        result.add("");
        
        while (result.peek().length() < digits.length()) {//如果队列中的字符串的长度还小与input digits的长度，就继续BFS
            String shorterStr = result.remove(); // 从队列中弹出长度还未到达结果长度的临时字符串元素
            char ch = digits.charAt(shorterStr.length());//e.g.,比如给定的例子中树形结构的第二层有三个元素，分别是a，b，c，这时候长度还不满足最后条件，继续做BFS到树形结构下一层
            String map = mapping[ch - '0'];//该层的所有元素
            for (char temp : map.toCharArray()) {//该层的所有元素分别加入一个字符成为下一层的树形结构节点
                result.addLast(shorterStr + temp);
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
        List<String> results = new ArrayList<>();
        generateParenthesisOneByOne(results, n, n, "");
        return results;
    }
    private void generateParenthesisOneByOne(List<String> results, int left, int right, String oneResult) {
        //左右括号都没剩余的，已经组装完一个合法的括号组合字符串，这时候才可以加入到结果中
        if (left == 0 && right == 0) {
            results.add(oneResult);
            return;
        }

        if (left > 0) {//左括号还有剩余，可以继续加
            generateParenthesisOneByOne(results, left - 1, right, oneResult + "(");
        }

        if (right > left) {//严格来说这里的条件是right > 0 && right > left，因为right个括号还有剩余并且不能更多地放在left个括号之前
            generateParenthesisOneByOne(results, left, right - 1, oneResult + ")");
        }
    }
}
```

BFS

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        if (n == 0) {
            return Collections.emptyList();
        }
        var queue = new ArrayDeque<Pair<String, int[]>>();
        queue.offer(new Pair<>("", new int[] {0, 0}));
        var result = new ArrayList<String>();
        while (!queue.isEmpty()) {
            var current = queue.poll();
            String bracket = current.getKey();
            int opening = current.getValue()[0];
            int closing = current.getValue()[1];
            if (opening == n && closing == n) {
                result.add(bracket);
            } else {
                if (opening < n) {
                    queue.offer(new Pair<>(bracket + "(", new int[] {opening + 1, closing}));
                }
                if (closing < opening) {
                    queue.offer(new Pair<>(bracket + ")", new int[] {opening, closing + 1}));
                }
            }
        }
        return result;
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
                        str = str.substring(0, i + 1) + "()" + str.substring(i + 1, str.length());
                        result.add(str);
                        str = str.substring(0, i + 1) + str.substring(i + 3, str.length());
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

HashMap + 排序的办法，时间复杂度O\(NKlogK\)，空间复杂度O\(NK\)

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        if (strs == null || strs.length == 0) {
            return new ArrayList<>();
        }

        HashMap<String, List<String>> map = new HashMap<>();
        for (String s : strs) {
            char[] cha = s.toCharArray();
            Arrays.sort(cha);//对字符串转换的字符数组进行排序
            String keyStr = String.valueOf(cha);//字符数组转换成字符串的key
            if (!map.containsKey(keyStr)) {//第一次出现就新增一个key
                map.put(keyStr, new ArrayList<>());
            }
            map.get(keyStr).add(s);
        }
        return new ArrayList<>(map.values());//map.values()获得HashMap的所有的值
    }
}
```

在上面HashMap方法的基础上，不直接Arrays.sort\(\)的结果当作key，而是自己生成key，时间复杂度O\(NK\)，空间复杂度O\(NK\)

```java
class Solution {
    /**
    将HashMap的key用特殊的值来代替，把KlogK优化到K
    */
    public List<List<String>> groupAnagrams(String[] strs) {
        if (strs.length == 0) {
            return new ArrayList<>();
        }
        Map<String, List> map = new HashMap<>();
        int[] count = new int[26]; // 都是小写字母
        for (String str : strs) {
            Arrays.fill(count, 0); // 每个str重新default
            for (char c : str.toCharArray()) {
                count[c - 'a']++;
            }

            StringBuilder sb = new StringBuilder("");
            for (int i = 0; i < 26; i++) {// 生成特殊的key
                sb.append('#');
                sb.append(count[i]);
            }
            String key = sb.toString(); // 拿到unique的字符串key了
            if (!map.containsKey(key)) {
                map.put(key, new ArrayList<>());
            }
            map.get(key).add(str);
        }
        return new ArrayList(map.values());
    }
}
```

HashMap + 质数做乘积找key，时间复杂度O\(NK\)，空间复杂度O\(NK\)

```java
class Solution {
    public static List<List<String>> groupAnagrams(String[] strs) {
        if (strs == null || strs.length == 0) {
            return new ArrayList<>();
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
                map.put(key, new ArrayList<>());
            }
            map.get(key).add(s);
        }
        return new ArrayList<>(map.values());
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
        int index = 0;//记录字符串的索引
        if (str.charAt(0) == '-') {
            flag = '-';
            index++;//挪到正/负号的下一位
        } else if (str.charAt(0) == '+') {
            index++;
        }

        //使用double来保存结果,这里用long也会有test cases越界，用BigInteger的话则计算的时候代码较多
        double result = 0;
        //检查条件，i索引不越界，且值在0到9之间
        while (str.length() > index && str.charAt(i) >= '0' && str.charAt(index) <= '9') {
            result = result * 10 + (str.charAt(index) - '0');//从左到右每次取一位字符来计算
            index++;//向右移动索引
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

开始遍历母字符串，我们并不需要遍历整个母字符串，而是遍历到剩下的长度和子字符串相等的位置即可，这样可以稍微提高运算效率。对于遍历到的每一个字符，都遍历一遍子字符串，一个一个字符的对应比较，如果对应位置有不等的，则跳出循环，如果一直都没有跳出循环，则说明子字符串出现了，则返回起始位置即可。

至于KMP算法，需要了解下，看[这个链接](https://labuladong.gitbook.io/algo/dong-tai-gui-hua-xi-lie/dong-tai-gui-hua-zhi-kmp-zi-fu-pi-pei-suan-fa)。

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

用substring

```java
class Solution {
    public int strStr(String haystack, String needle) {
        if (needle == null || needle.length() == 0) {
            return 0;
        }
        int len = needle.length();
        char ch = needle.charAt(0);
        for (int i = 0; i <= haystack.length()-len; i++) {
            if (haystack.charAt(i) == ch) { //先比较下首字符，聊胜于无
                String temp = haystack.substring(i, i + len);
                if (temp.equals(needle)) {
                    return i;
                }
            }
        }
        return -1;
    }
}
```

KMP

```java
public String strStr(String haystack, String needle) {
	//KMP algorithms
	if(needle.equals("")) return haystack;
	if(haystack.equals("")) return null;
	char[] arr = needle.toCharArray();
	int[] next = makeNext(arr);

	for(int i = 0, j = 0, end = haystack.length(); i < end;){
		if(j == -1 || haystack.charAt(i) == arr[j]){
			j++;
			i++;
			if(j == arr.length) return haystack.substring(i - arr.length);
		}
		if(i < end && haystack.charAt(i) != arr[j]) j = next[j];
	}
    return null;
}

private int[] makeNext(char[] arr){
	int len = arr.length;
	int[] next = new int[len];

	next[0] = -1;
	for(int i = 0, j = -1; i + 1 < len;){
		if(j == -1 || arr[i] == arr[j]){
			next[i+1] = j+1;
			if(arr[i+1] == arr[j+1]) next[i+1] = next[j+1];
			i++;
			j++;
		}
		if(arr[i] != arr[j]) j = next[j];
	}

	return next;
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

Given an arbitrary ransom note string and another string containing letters from all the magazines, write a function that will return true if the ransom note can be constructed from the magazines ; otherwise, it will return false.

Each letter in the magazine string can only be used once in your ransom note.

**Note:**  
You may assume that both strings contain only lowercase letters.

```text
canConstruct("a", "b") -> false
canConstruct("aa", "ab") -> false
canConstruct("aa", "aab") -> true
```

### 题意和分析

赎金条，看一个magazines来的字符串是否可以组成赎金的note的字符串，每个字符只能用一次。用HashMap来统计次数，然后把赎金条的信息遍历看是否够用即可。

### 代码

```java
class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        Map<Character, Integer> map = new HashMap<>();
        for (char ch : magazine.toCharArray()) {
            int newCount = map.getOrDefault(ch, 0) + 1;
            map.put(ch, newCount);
        }

        for (char ch : ransomNote.toCharArray()) {
            if (!map.containsKey(ch)) {//不包含的字符直接为false
                return false;
            }
            int newCount = map.get(ch) - 1;
            if (newCount < 0) {//ransomNote字符过多返回false
                return false;
            }
            map.put(ch, newCount);//map.get(ch) = newCount;
        }
        return true;
    }
}
```

## 804 - Unique Morse Code Words

### 原题概述

International Morse Code defines a standard encoding where each letter is mapped to a series of dots and dashes, as follows: `"a"`maps to `".-"`, `"b"` maps to `"-..."`, `"c"` maps to `"-.-."`, and so on.

For convenience, the full table for the 26 letters of the English alphabet is given below:

```text
[".-","-...","-.-.","-..",".","..-.","--.","....","..",".---","-.-",".-..","--","-.","---",".--.","--.-",".-.","...","-","..-","...-",".--","-..-","-.--","--.."]
```

Now, given a list of words, each word can be written as a concatenation of the Morse code of each letter. For example, "cab" can be written as "-.-.-....-", \(which is the concatenation "-.-." + "-..." + ".-"\). We'll call such a concatenation, the transformation of a word.

Return the number of different transformations among all words we have.

```text
Example:
Input: words = ["gin", "zen", "gig", "msg"]
Output: 2
Explanation: 
The transformation of each word is:
"gin" -> "--...-."
"zen" -> "--...-."
"gig" -> "--...--."
"msg" -> "--...--."

There are 2 different transformations, "--...-." and "--...--.".
```

**Note:**

* The length of `words` will be at most `100`.
* Each `words[i]` will have length in range `[1, 12]`.
* `words[i]` will only consist of lowercase letters.

### 题意和分析

介绍了摩斯码的写法，给一个字符串数组，问表示这些单词的摩斯码共有多少种，因为有些单词的摩斯码是相同的，比如gin和zin；遍历给的字符串数组，求出每个字符串摩斯码，然后在HashSet中检查是否出现过，最后返回HashSet的长度。

Follow up问题可能问给一个摩斯码，问可以组成几个单词？ 

### 代码

```java
class Solution {
    public int uniqueMorseRepresentations(String[] words) {
        String[] morse = {".-", "-...", "-.-.", "-..", ".", "..-.", "--.", "....", "..", ".---", "-.-", ".-..", "--", "-.", "---", ".--.", "--.-", ".-.", "...", "-", "..-", "...-", ".--", "-..-", "-.--", "--.."};
        HashSet<String> hashSet = new HashSet<>();
        for (String word : words) {
            StringBuilder sb = new StringBuilder();
            for (char ch : word.toCharArray()) {
                sb.append(morse[ch - 'a']);
            }
            hashSet.add(sb.toString());//HashSet直接加入，不用查重
        }
        return hashSet.size();
    }
}
```

## 43 - Multiply Strings

### 原题概述

Given two non-negative integers `num1` and `num2` represented as strings, return the product of `num1` and `num2`, also represented as a string.

**Example 1:**

```text
Input: num1 = "2", num2 = "3"
Output: "6"
```

**Example 2:**

```text
Input: num1 = "123", num2 = "456"
Output: "56088"
```

**Note:**

1. The length of both `num1` and `num2` is &lt; 110.
2. Both `num1` and `num2` contain only digits `0-9`.
3. Both `num1` and `num2` do not contain any leading zero, except the number 0 itself.
4. You **must not use any built-in BigInteger library** or **convert the inputs to integer** directly.

### 题意和分析

两个字符串表示的数字，相乘后返回字符串表示的乘积（这样可以计算超大数相乘，不受int和long数值范围的约束），就用多位数的乘法过程，每位相乘然后错位相加，把错位相加的结果保存在一个一维数组中，然后每位上算进位，最后每位就变成一位，去掉首位的0，然后把每位数字按顺序保存到结果中。

![](../.gitbook/assets/image%20%2843%29.png)

### 代码

```java
class Solution {
    public String multiply(String num1, String num2) {
        int m = num1.length(), n = num2.length();
        int[] pos = new int[m+n];//二者的乘积最大长度为m+n-1加上首位上的0

        for (int i = m - 1; i >= 0; i--) {//从右边的数开始乘积
            for (int j = n - 1; j >= 0; j--) {
                int mul = (num1.charAt(i) - '0') * (num2.charAt(j) - '0');
                int p1 = i + j, p2 = i + j + 1;//上一步的乘积可能是两位数，所以在一维数组pos中先找到两个位置的索引存放十位和个位
                int sum = mul + pos[p2];//加上第二个数上一轮可能存在此位置的数值

                pos[p1] += sum / 10;//重新计算的sum，第一个位置存十位
                pos[p2] = sum % 10;//重新计算的sum，第二个位置存个位
            }
        }
        StringBuilder sb = new StringBuilder();
        //遍历整个一维数组
        for (int p : pos) {
            if (!(sb.length() == 0 && p == 0)) {//非乘以0的情况
                sb.append(p);
            }
        }
        return sb.length() == 0 ? "0" : sb.toString();
    }
}
```

## 387 - First Unique Character in a String

### 原题概述

Given a string, find the first non-repeating character in it and return it's index. If it doesn't exist, return -1.

**Examples:**

```text
s = "leetcode"
return 0.

s = "loveleetcode",
return 2.
```

**Note:** You may assume the string contain only lowercase letters.

### 题意和分析

找到全小写字符串中第一个出现的非重复字符，那么就先找到每个字符出现的次数，然后找到出现次数为1的字符，返回其位置即可。由于Java中的HashMap的顺序是按照有利于随机查找的hash的顺序，并非是按照输入的顺序，所以用一个26长度的数组来记录频率。

### 代码

```java
class Solution {
    public int firstUniqChar(String s) {
        if (s == null || s.length() == 0) {
            return -1;
        }

        int[] freq = new int[26];
        for (char ch : s.toCharArray()) {
            freq[ch - 'a']++;
        }
        for (int i = 0; i < s.length(); i++) {//注意依然按照s中字符的顺序来遍历
            if (freq[s.charAt(i) - 'a'] == 1) return i;
        }
        return -1;
    }
}
```

## 345 - Reverse Vowels of a String

### 原题概述

Write a function that takes a string as input and reverse only the vowels of a string.

**Example 1:**

```text
Input: "hello"
Output: "holle"
```

**Example 2:**

```text
Input: "leetcode"
Output: "leotcede"
```

**Note:**  
The vowels does not include the letter "y".

### 题意和分析

只翻转一个字符串中的元音字符，题目中没说只是小写，所以大写的字符也算，可以直接双指针left和right，如果都指向元音字符，交换；如果left不是，右移一位left；如果right不是，左移一位right；当left大于等于right的时候停止。

### 代码

```java
class Solution {
    public String reverseVowels(String s) {
        if (s == null || s.length() == 0 || s.length() == 1) {
            return s;
        }
        char[] word = s.toCharArray();
        int left = 0, right = s.length() - 1;
        while(left <= right){
            if(isVowels(word[left]) && isVowels(word[right])){
                swap(word, left, right);
                left++;
                right--;
            }else if (!isVowels(word[left])){
                left++;
            }else {
                right--;
            }
        }
        return new String(word);
    }
    public boolean isVowels(char c){
        return c=='a'||c=='e'||c=='i'||c=='o'||c=='u'||c=='A'||c=='E'||c=='I'||c=='O'||c=='U';
    }
    public void swap(char[] ch,int left,int right){
        char temp = ch[left];
        ch[left] = ch[right];
        ch[right] = temp;
    }
}
```

```java
class Solution {
    public String reverseVowels(String s) {
        if(s == null || s.length()==0 || s.length() == 1) return s;
        String vowels = "aeiouAEIOU";
        char[] chars = s.toCharArray();
        int left = 0;
        int right = s.length()-1;
        while(left < right){

            while(left < right && !vowels.contains(chars[left]+"")) {//转换成字符串检查是否存在
                left++;
            }

            while(left < right && !vowels.contains(chars[right]+"")) {
                right--;
            }

            char temp = chars[left];
            chars[left] = chars[right];
            chars[right] = temp;

            left++;
            right--;
        }
        return new String(chars);
    }
}
```

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

相对于Basic Calculate I中只有加减，这道题多了乘除，因为没有括号，所以简化了。使用stack来存储计算的中间结果，每次遇到加减法就直接压入；遇到乘除法就先计算一下再压入。

时间上一遍扫过去O\(n\)，空间上开了一个stack，O\(n\)。

当然这道题不用stack也可以（不如stack直观），可以把string先转换成char array，然后从左到右运算，维持一个result负责最终结果和一个pre负责遇到乘法除法的时候重算一下先做乘除。复杂度同stack。

### 代码

Stack

```java
class Solution {
    public int calculate(String s) {
        if (s.isEmpty()) {
            return 0;
        }
        int num = 0;
        int len = s.length();
        Stack<Integer> stack = new Stack<>();//存符号两边的数字
        char sign = '+';
        
        for (int i = 0; i < len; i++) {
            char ch = s.charAt(i);
            if (Character.isDigit(ch)) {
                num = num * 10 + (ch - '0'); //多位数的多个数字
            }
            
            if ((!Character.isDigit(ch) && //不是数字，是符号
                ch != ' ') || //不是空格
               i == len - 1) { //最后一个位置虽然是数字但位置特殊也要计算
                
                // 加法和减法是直接push，加正数和负数的区别
                if (sign == '+') {
                    stack.push(num);
                }
                if (sign == '-') {
                    stack.push(-num);
                }
                
                
                //乘法和除法是先计算两旁的数再push
                if (sign == '*') {
                    stack.push(stack.pop() * num); //stack中最顶上的数字是当前符号的前一个数字
                }
                if (sign == '/') {
                    stack.push(stack.pop() / num);
                }
                sign = ch; //更新当前sign的符号，下一次循环根据sign的值处理该sign两边的数字
                num = 0;
            }
        }
        
        int result = 0;
        //循环做完后，所有乘法和除法也做完并push了，目前stack里面只剩加法和减法符号
        for (int ele : stack) { //这样遍历是先进先出（顺序不影响）
            result += ele;
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

## 273 Integer to English Words

### 原题概述

Convert a non-negative integer to its english words representation. Given input is guaranteed to be less than 231 - 1.

**Example 1:**

```text
Input: 123
Output: "One Hundred Twenty Three"
```

**Example 2:**

```text
Input: 12345
Output: "Twelve Thousand Three Hundred Forty Five"
```

**Example 3:**

```text
Input: 1234567
Output: "One Million Two Hundred Thirty Four Thousand Five Hundred Sixty Seven"
```

**Example 4:**

```text
Input: 1234567891
Output: "One Billion Two Hundred Thirty Four Million Five Hundred Sixty Seven Thousand Eight Hundred Ninety One"
```

### 题意和分析

要求把整数转换成英文单词，利用数组来枚举， 3个一组的进行处理，而且题目中限定了输入数字范围为0到2^31 - 1之间，最高只能到billion位，3个一组也只需处理四组即可，那么我们需要些一个处理三个一组数字的函数。

需要把1到19的英文单词都列出来，放到一个数组里，还要把20,30，... 到90的英文单词列出来放到另一个数组里，然后需要用写技巧，比如一个三位数n，百位数表示为n/100，后两位数一起表示为n%100，十位数表示为n%100/10，个位数表示为n%10，然后我们看后两位数是否小于20，小于的话直接从数组中取出单词，如果大于等于20的话，则分别将十位和个位数字的单词从两个数组中取出来。然后再来处理百位上的数字，还要记得加上Hundred。主函数中调用四次这个帮助函数，然后中间要插入"Thousand", "Million", "Billion"到对应的位置，最后check一下末尾是否有空格，把空格都删掉，返回的时候检查下输入是否为0，是的话要返回'Zero'。

### 代码

```java
class Solution {
    private final String[] LESS_THAN_20 = {"", "One", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine", "Ten", "Eleven", "Twelve", "Thirteen", "Fourteen", "Fifteen", "Sixteen", "Seventeen", "Eighteen", "Nineteen"};
    private final String[] TENS = {"", "Ten", "Twenty", "Thirty", "Forty", "Fifty", "Sixty", "Seventy", "Eighty", "Ninety"};
    private final String[] HUNDREDS = {"", "One", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine"};
    private final String[] THOUSANDS = {"", "Thousand", "Million", "Billion"};

    public String numberToWords(int num) {
        if (num == 0) {
            return "Zero";
        }

        int i = 0;
        String words = "";

        while (num > 0) {
            if (num % 1000 != 0) {
                words = numberToWordsHelper(num % 1000) + THOUSANDS[i] + " " + words;
            }
            num /= 1000;
            i++;//指针每次在THOUSANDS数组里面挪动读取对应的字母
        }
        return words.trim();
    }

    private String numberToWordsHelper(int num) {
        if (num == 0) {
            return "";
        } else if (num < 20) {
            return LESS_THAN_20[num] + " ";
        } else if (num < 100) {
            return TENS[num / 10] + " " + numberToWordsHelper(num % 10);
        } else {
            return HUNDREDS[num / 100] + " Hundred " + numberToWordsHelper(num % 100);
        }
    }
}
```

## 65 Valid Number

### 原题概述

Validate if a given string can be interpreted as a decimal number.

Some examples:  
`"0"` =&gt; `true`  
`" 0.1 "` =&gt; `true`  
`"abc"` =&gt; `false`  
`"1 a"` =&gt; `false`  
`"2e10"` =&gt; `true`  
`" -90e3   "` =&gt; `true`  
`" 1e"` =&gt; `false`  
`"e3"` =&gt; `false`  
`" 6e-1"` =&gt; `true`  
`" 99e2.5 "` =&gt; `false`  
`"53.5e93"` =&gt; `true`  
`" --6 "` =&gt; `false`  
`"-+3"` =&gt; `false`  
`"95a54e53"` =&gt; `false`

**Note:** It is intended for the problem statement to be ambiguous. You should gather all requirements up front before implementing one. However, here is a list of characters that can be in a valid decimal number:

* Numbers 0-9
* Exponent - "e"
* Positive/negative sign - "+"/"-"
* Decimal point - "."

Of course, the context of these characters also matters in the input.

**Update \(2015-02-10\):**  
The signature of the `C++` function had been updated. If you still see your function signature accepts a `const char *` argument, please click the reload button to reset your code definition.

### 题意和分析

这道题主要考察各种corner cases，包括正负号/e/点，来判断字符串是否是有效的数字。如果是在最近的面经里看到这道题可以刷一下，否则不用理会。

### 代码

```java
class Solution {
   public boolean isNumber(String s) {
      s = s.trim();

      boolean numberSeen = false;
      boolean pointSeen = false;
      boolean eSeen = false;
      boolean numberAfterE = true;//表示e之后是否有number，因为最后要返回，这里初始值为true（之有数字的情况，可以过）

      for (int i = 0; i < s.length(); i++) {
         if (s.charAt(i) >= '0' && s.charAt(i) <= '9') {
            numberSeen = true;
            numberAfterE = true;//再有数字出现，再把numberAfterE赋值为true，注意第25行
         } else if (s.charAt(i) == '.') {
            if (eSeen || pointSeen) {//'.'可以在'e'前面，比如1.2e3；但不能在e后面，比如4e1.2就不行；另外同时出现两个'.'也不行，比如1..2
               return false;
            }
            pointSeen = true;
         } else if (s.charAt(i) == 'e') {
            if (eSeen //e之前不能有另外的e
                  || !numberSeen) {//e之前必须有number出现
               return false;
            }
            eSeen = true;
            numberAfterE = false;//首先有e出现，numberAfterE必为false，注意第13行
         } else if (s.charAt(i) == '+' || s.charAt(i) == '-') {
            if (i != 0 && s.charAt(i - 1) != 'e') {//+和-只能在第一位或者e后面出现
               return false;
            }
         } else {
            return false;
         }
      }
      return numberSeen && numberAfterE;//必须有数字；以及如果有e的话，e后面必须跟数字
   }
}
```

## 819 Most Common Word

### 原题概述

Given a paragraph and a list of banned words, return the most frequent word that is not in the list of banned words.  It is guaranteed there is at least one word that isn't banned, and that the answer is unique.

Words in the list of banned words are given in lowercase, and free of punctuation.  Words in the paragraph are not case sensitive.  The answer is in lowercase.

**Example:**

```text
Input: 
paragraph = "Bob hit a ball, the hit BALL flew far after it was hit."
banned = ["hit"]
Output: "ball"
Explanation: 
"hit" occurs 3 times, but it is a banned word.
"ball" occurs twice (and no other word does), so it is the most frequent non-banned word in the paragraph. 
Note that words in the paragraph are not case sensitive,
that punctuation is ignored (even if adjacent to words, such as "ball,"), 
and that "hit" isn't the answer even though it occurs more because it is banned.
```

**Note:**

* `1 <= paragraph.length <= 1000`.
* `1 <= banned.length <= 100`.
* `1 <= banned[i].length <= 10`.
* The answer is unique, and written in lowercase \(even if its occurrences in `paragraph` may have uppercase symbols, and even if it is a proper noun.\)
* `paragraph` only consists of letters, spaces, or the punctuation symbols `!?',;.`
* There are no hyphens or hyphenated words.
* Words only consist of letters, never apostrophes or other punctuation symbols.

### 题意和分析

找到段落中出现次数最多的单词，并且该单词不出现在被禁止的列表当中。思路很简单，用hashmap统计所有单词频率，将map中banned的单词去除，然后统计出现次数最多的单词即可。

### 代码

```java
class Solution {
   public String mostCommonWord(String paragraph, String[] banned) {
      String result = new String();
      if (paragraph.isEmpty()) {
         return result;
      }
      String[] words = paragraph.replaceAll("\\W+" , " ").toLowerCase().split("\\s+");//将所有单词提取出来，并小写存入到array中

      HashMap<String, Integer> map = new HashMap<>();
      //将所有的paragraph里面的单词加入到hashmap中，value为出现的次数
      for (String word : words) {
         map.put(word, map.getOrDefault(word, 0) + 1);
      }
      //剔除banned里面的单词
      for (String bannedWord : banned) {
         if (map.containsKey(bannedWord)) {
            map.remove(bannedWord);
         }
      }
      //找到map中出现最多的单词
      for (Map.Entry<String, Integer> wordEntry : map.entrySet()) {
         if (result.length() == 0 || wordEntry.getValue() > map.get(result)) {
            result = wordEntry.getKey();
         }
      }
      return result;
   }
}
```

## 1071 Greatest Common Divisor of Strings

### 题目

For strings `S` and `T`, we say "`T` divides `S`" if and only if `S = T + ... + T`  \(`T` concatenated with itself 1 or more times\)

Return the largest string `X` such that `X` divides str1 and `X` divides str2.

**Example 1:**

```text
Input: str1 = "ABCABC", str2 = "ABC"
Output: "ABC"
```

**Example 2:**

```text
Input: str1 = "ABABAB", str2 = "ABAB"
Output: "AB"
```

**Example 3:**

```text
Input: str1 = "LEET", str2 = "CODE"
Output: ""
```

**Note:**

1. `1 <= str1.length <= 1000`
2. `1 <= str2.length <= 1000`
3. `str1[i]` and `str2[i]` are English uppercase letters.

### 分析

找两个字符串的最大公约数，依然利用GCD的辗转相除法（Euclidean algorithm），两个字符串如果有不为""的最大公约字符串，那二者相加的顺序应该时相同的，证明：

1. 假如s1和s2存在相同的除数X，即s1=XXX, s2=XX, 可见s1+s2=s2+s1 
2. 现在的问题是：若s1和s2不存在相同的除数，那么是否存在s1+s2=s2+s1的情况呢？ 假设s1和s2不存在相同的除数，且满足s1+s2=s2+s1，如下图所示（红色的是s1，蓝色的是s2，其中s1长于s2）

![](../.gitbook/assets/image%20%2898%29.png)

那由上图可知s2既是s1的前缀，又是s2的后缀（灰色表示）。

![](../.gitbook/assets/image%20%2896%29.png)

再看上图的黄色部分可知s3+s2 = s2+s3；其中s3=s1-s2。 

以上，我们由s1+s2=s2+s1推出了s3+s2=s2+s3， 其中s3 = s1 - s2。 

继而，s3 + s4 = s4 + s3,其中s4 = s3 - s2。 

然后可以递归下去，每次用长的减去短的。 因为我们的假设条件是等式s1+s2=s2+s1成立，即上图示成立，所以每次递归时的两个字符串，短串总是长串的前缀和后缀，所以两串总是可减的， 所以我们总是能减到两个串相等的时候，相等时即是最大公共除数，与假设的没有公共除数矛盾。 

因此结论是若s1和s2不存在公共除数，则s1+s2!=s2+s1\(反之亦然\)。

### 代码

```java
class Solution {
    public String gcdOfStrings(String str1, String str2) {
        // 假设str1是N个x，str2是M个x，那么concatate后，str1+str2肯定是等于str2+str1的
        if (!(str1 + str2).equals(str2 + str1)) {
            return "";
        }
        
        // 辗转相除法，将两个字符串的长度作为参数，辗转取余，较小的数为0的时候为最大gcd
        return str1.substring(0, gcd(str1.length(), str2.length()));
    }
    
    private int gcd(int a, int b) {
        while (b != 0) {
            int temp = b;
            b = a % b;
            a = temp;
        }
        return a; // 这时b为0
        // return b == 0 ? a: gcd(b, a % b); //递归写法
    }
}
```

## 165 Compare Version Number

### 题目

Compare two version numbers _version1_ and _version2_.  
If _`version1`_ `>` _`version2`_ return `1;` if _`version1`_ `<` _`version2`_ return `-1;`otherwise return `0`.

You may assume that the version strings are non-empty and contain only digits and the `.` character.

The `.` character does not represent a decimal point and is used to separate number sequences.

For instance, `2.5` is not "two and a half" or "half way to version three", it is the fifth second-level revision of the second first-level revision.

You may assume the default revision number for each level of a version number to be `0`. For example, version number `3.4` has a revision number of `3` and `4` for its first and second level revision number. Its third and fourth level revision number are both `0`.

**Example 1:**

```text
Input: version1 = "0.1", version2 = "1.1"
Output: -1
```

**Example 2:**

```text
Input: version1 = "1.0.1", version2 = "1"
Output: 1
```

**Example 3:**

```text
Input: version1 = "7.5.2.4", version2 = "7.5.3"
Output: -1
```

**Example 4:**

```text
Input: version1 = "1.01", version2 = "1.001"
Output: 0
Explanation: Ignoring leading zeroes, both “01” and “001" represent the same number “1”
```

**Example 5:**

```text
Input: version1 = "1.0", version2 = "1.0.0"
Output: 0
Explanation: The first version number does not have a third level revision number, which means its third level revision number is default to "0"
```

**Note:**

1. Version strings are composed of numeric strings separated by dots `.` and this numeric strings **may** have leading zeroes.
2. Version strings do not start or end with dots, and they will not be two consecutive dots.

### 分析

思路简单，就是需要把用“.”隔开的版本号从左到右逐个比较，每个隔开的小版本号可能会有leading zeros，不管几个leading zeros，都是一样的，比如1.01和1.001是一样的，可以用split分开成字符串数组，然后挨个比较；或者每次判断是不是“.”，从左到右将每个小版本号转换成整数进行比较。

### 代码

split\(\)

```java
class Solution {
    public int compareVersion(String version1, String version2) {
        if (version1.isEmpty() || version2.isEmpty()) {
            throw new IllegalArgumentException("Versions can not be Null.");
        }
        
        // regex中 "\."代表真正的dot，而"."代表任何字符
        String[] arr1 = version1.split("\\.");
        String[] arr2 = version2.split("\\.");
        
        int idx = 0;
        
        int len = Math.max(arr1.length, arr2.length);
        
        for (int i = 0; i < len; i++) { // 数组中比较短的那个版本号后面用0代替
            
            // 分别计算两个版本当前的小版本号，Integer才有后面的parseInt和compareTo方法
            Integer v1 = (idx < arr1.length) ? Integer.parseInt(arr1[idx]) : 0;
            Integer v2 = (idx < arr2.length) ? Integer.parseInt(arr2[idx]) : 0;
            idx++;
            
            int compare = v1.compareTo(v2);
            if (compare != 0) {
                return compare;
            }
        }
        return 0; //相同
    }
}
```

另一种写法，省空间一点

```java
class Solution {
    public int compareVersion(String version1, String version2) {
        if (version1.isEmpty() || version2.isEmpty()) {
            throw new IllegalArgumentException("Versions can not be Null.");
        }
        
        int v1 = 0, v2 = 0;        
        int idx1 = 0, idx2 = 0;
        
        while (idx1 < version1.length() || idx2 < version2.length()) {
            v1 = 0;
            v2 = 0;
            
            // 分别计算两个版本的小版本号
            while (idx1 < version1.length() && version1.charAt(idx1) != '.') {
                v1 = v1 * 10 + version1.charAt(idx1) - '0';
                idx1++;
            }
            while (idx2 < version2.length() && version2.charAt(idx2) != '.') {
                v2 = v2 * 10 + version2.charAt(idx2) - '0';
                idx2++;
            }
            
            if (v1 < v2) {
                return -1;
            } else if (v1 > v2) {
                return 1;
            } else { //当前小版本相等还得继续检查，直到最长的版本检查完
                idx1++;
                idx2++;
            }
        }
        return 0; // 查到最后完全相等
    }
}
```

## 1371 Find the Longest Substring Containing Vowels in Even Counts

### 题目

Given the string `s`, return the size of the longest substring containing each vowel an even number of times. That is, 'a', 'e', 'i', 'o', and 'u' must appear an even number of times.

**Example 1:**

```text
Input: s = "eleetminicoworoep"
Output: 13
Explanation: The longest substring is "leetminicowor" which contains two each of the vowels: e, i and o and zero of the vowels: a and u.
```

**Example 2:**

```text
Input: s = "leetcodeisgreat"
Output: 5
Explanation: The longest substring is "leetc" which contains two e's.
```

**Example 3:**

```text
Input: s = "bcbcbc"
Output: 6
Explanation: In this case, the given string "bcbcbc" is the longest because all vowels: a, e, i, o and u appear zero times.
```

**Constraints:**

* `1 <= s.length <= 5 x 10^5`
* `s` contains only lowercase English letters.

### 分析

遇到找最大最小字串问题，第一反应滑动窗口行不行。 但是很快这个想法就被否定了，因为滑动窗口（这里是可变滑动窗口）需要扩张和收缩窗口大小，这样做起来比较麻烦。因为题目要求的是奇偶性，而不是类似“元音出现最多的子串”等。

1\) 没了思路，那就试试暴力法吧。暴力法的思路比较朴素和直观。 那就是双层循环找到所有子串，然后对于每一个子串，统计元音个数，如果子串的元音个数都是偶数，则更新答案，最后返回最大的满足条件的子串长度即可。暴力可以用一个小的 trick。枚举所有子串的时候，从最长的子串开始枚举的，这样找到一个满足条件的直接返回就行了（early return），不必再去找较小的子串以及维护最大值。

* 时间复杂度：双层循环找出所有子串的复杂度是O\(n^2\)，统计字串中元音个数复杂度也是$O\(n\)，因此这种算法的时间复杂度为$O\(n^3\)。
* 空间复杂度：O\(1\)

暴力法可以通过测试。

2\) 上面暴力法思路中对于每一个子串，统计元音个数，仔细观察的话，会发现有很多重复的统计。那么优化这部分的内容就可以获得更好的效率。

对于这种连续的数字问题，这里考虑使用[前缀和](https://oi-wiki.org/basic/prefix-sum/)来优化，做预处理。

经过这种空间换时间的策略之后，时间复杂度会降低到O\(n ^ 2\)，但是相应空间复杂度会上升到O\(n\)，这种取舍在很多情况下是值得的。

3\) 前面的前缀和思路，通过空间（prefix）换取时间的方式降低了时间复杂度。但是时间复杂度仍然是平方，是否可以继续优化呢？

实际上由于我们只关心元音出现的奇偶性，并不关心每一个元音字母具体出现的次数。因此可以使用**`是奇数，是偶数`**两个状态来表示，由于只有两个状态，考虑使用位运算。

使用 5 位的二进制来表示以 i 结尾的字符串中包含各个元音的奇偶性，其中 0 表示偶数，1 表示奇数，并且最低位表示 a，然后依次是 e，i，o，u。比如 `10110` 则表示的是包含偶数个 a 和 o，奇数个 e，i，u。

为什么用 0 表示偶数？1 表示奇数？

其实这个解法还用到了一个性质，这个性质是小学数学知识：

* 如果两个数字奇偶性相同，那么其相减一定是偶数。
* 如果两个数字奇偶性不同，那么其相减一定是奇数。

看到这里，`为什么用 0 表示偶数？1 表示奇数？`。因为这里我们打算用异或运算，而异或的性质是：

如果对两个二进制做异或，会对其每一位进行位运算，如果相同则位 0，否则位 1。这和上面的性质非常相似。上面说`奇偶性相同则位偶数，否则为奇数`。因此很自然地`用 0 表示偶数？1 表示奇数`会更加方便。

时间复杂度O\(n\)，空间复杂度O\(n\)。

### 代码

暴力法，O\(n ^ 3\)

```java
class Solution {
    public int findTheLongestSubstring(String s) {
        int len = s.length();
        char[] vowels = {'a', 'e', 'i', 'o', 'u'};
        for (int i = len; i >= 0; i--) { // i是offset，从最长的字符串长度len开始
            for (int j = 0; j < len - i + 1; j++) {// j是起点             
                String sub = s.substring(j, j + i); // 从最长字符串开始
                boolean has_odd_vowel = false;
                
                for (char vowel : vowels) { // 逐个检查每个元音
                    if (countVowel(sub, vowel) % 2 != 0) {
                        has_odd_vowel = true;
                        break;
                    }
                }
                
                if (!has_odd_vowel) { // early return
                    return i;
                }
            }
        }
        return 0;
    }
    private int countVowel (String sub, char ch) {
        int result = 0;
        for (int k = 0; k < sub.length(); k++) {
            if (ch == sub.charAt(k)) {
                result++;
            }
        }
        return result;
    }
}
```

前缀和+剪枝，O\(n ^ 2\)

```java
class Solution {
    public int findTheLongestSubstring(String s) {

        int len = s.length();

        if (len == 0) {
            return 0;
        }

        int[][] preSum = new int[len][5]; // 记录5个元音字母的出现次数
        
        // 初始化
        int start = getIndex(s.charAt(0)); // 找到当前字符在数组中的位置
        if (start != -1) {
            preSum[0][start]++;
        }

        // 计算字符串中每个位置的preSum
        for (int i = 1; i < len; i++) {

            int index = getIndex(s.charAt(i));

            for (int j = 0; j < 5; j++) { //index分别为0，1，2，3，4

                if (index == j) { // 遇到元音，长度+1
                    preSum[i][j] = preSum[i - 1][j] + 1;
                } else { // 遇到辅音
                    preSum[i][j] = preSum[i - 1][j];
                }
            }
        }

        for (int i = len - 1; i >= 0; i--) { // offset
            for (int j = 0; j < len - i; j++) {
                if (checkValid(preSum, s, j, j + i)) { // [)
                    return i + 1;
                }
            }
        }
        return 0;
    }


    public boolean checkValid(int[][] preSum, String s, int left, int right) {

        int index = getIndex(s.charAt(left));

        for (int k = 0; k < 5; k++) {
            if (((preSum[right][k] - preSum[left][k] + (index == k ? 1 : 0)) & 1) == 1) { //根据存储的信息，相减计算当前字串是否有偶数的元音
                return false;
            }
        }

        return true;
    }
    
    private int getIndex(char ch) {

        if (ch == 'a') 
            return 0;
        else if (ch == 'e')
            return 1;
        else if (ch == 'i')
            return 2;
        else if (ch == 'o')
            return 3;
        else if (ch == 'u')
            return 4;
        else
            return -1;
    }
}
```

前缀和+状态压缩，O\(n\)

```java
class Solution {
    HashMap<Character, Integer> vowlToBitIndex = new HashMap<Character, Integer>() {
        {
            put('a', 1);
            put('e', 2);
            put('i', 4);
            put('o', 8);
            put('u', 16);
        }
    };
    
    public int findTheLongestSubstring(String s) {
        HashMap<Integer, Integer> stateToIndex = new HashMap<>();
        stateToIndex.put(0, -1);
        int state = 0, maxLen = 0;
        for(int i = 0; i < s.length(); ++i) {
            char cur = s.charAt(i);
            if(vowlToBitIndex.containsKey(cur)) {
                // flap the digits of the state. 1-> 0 or 0 -> 1
                int bitToFlip = vowlToBitIndex.get(cur); 
                state ^= bitToFlip; 
            }
            
            stateToIndex.putIfAbsent(state, i);
            maxLen = Math.max(maxLen, i - stateToIndex.get(state));
        }
        
        return maxLen;    
    }
}
```

```python

class Solution:
    def findTheLongestSubstring(self, s: str) -> int:
        mapper = {
            "a": 1,
            "e": 2,
            "i": 4,
            "o": 8,
            "u": 16
        }
        seen = {0: -1}
        res = cur = 0

        for i in range(len(s)):
            if s[i] in mapper:
                cur ^= mapper.get(s[i])
            # 全部奇偶性都相同，相减一定都是偶数
            if cur in seen:
                res = max(res, i - seen.get(cur))
            else:
                seen[cur] = i
        return res
```

## 929 Unique Email Address

### 题目

Every email consists of a local name and a domain name, separated by the @ sign.

For example, in `alice@leetcode.com`, `alice` is the local name, and `leetcode.com` is the domain name.

Besides lowercase letters, these emails may contain `'.'`s or `'+'`s.

If you add periods \(`'.'`\) between some characters in the **local name** part of an email address, mail sent there will be forwarded to the same address without dots in the local name.  For example, `"alice.z@leetcode.com"` and `"alicez@leetcode.com"` forward to the same email address.  \(Note that this rule does not apply for domain names.\)

If you add a plus \(`'+'`\) in the **local name**, everything after the first plus sign will be **ignored**. This allows certain emails to be filtered, for example `m.y+name@email.com` will be forwarded to `my@email.com`.  \(Again, this rule does not apply for domain names.\)

It is possible to use both of these rules at the same time.

Given a list of `emails`, we send one email to each address in the list.  How many different addresses actually receive mails? 

**Example 1:**

```text
Input: ["test.email+alex@leetcode.com","test.e.mail+bob.cathy@leetcode.com","testemail+david@lee.tcode.com"]
Output: 2
Explanation: "testemail@leetcode.com" and "testemail@lee.tcode.com" actually receive mails
```

**Note:**

* `1 <= emails[i].length <= 100`
* `1 <= emails.length <= 100`
* Each `emails[i]` contains exactly one `'@'` character.
* All local and domain names are non-empty.
* Local names do not start with a `'+'` character.

### 分析

根据题意，"."可以忽略，"+"在名字的位置后面的忽略，domain name照常比较。

直接的思路，用hashset除重。

### 代码

```java
class Solution {
    public int numUniqueEmails(String[] emails) {
        HashSet<String> set = new HashSet<>();
        int count = 0;
        for (String email : emails) {
            String[] arr = email.split("@", 2); // @只出现一次
            StringBuilder sb = new StringBuilder();
            int index = 0;
            while (index < arr[0].length() && arr[0].charAt(index) != '+') {
                if (arr[0].charAt(index) != '.') {
                    sb.append(arr[0].charAt(index));
                }
                index++;
            }
            sb.append("@");
            sb.append(arr[1]);
            if (set.add(sb.toString())) {
                count++;
            }
        }
        return count;
    }
} 
```



```java
class Solution {
    public int numUniqueEmails(String[] emails) {
        if (emails == null || emails.length == 0) {
            return 0;
        }
        Set<String> result = new HashSet<>();
        for (String email : emails) {
            StringBuilder sb = new StringBuilder();
            String[] splitted = email.split("\\@");
            char[] letters = splitted[0].toCharArray();
            for (char letter : letters) {
                if (letter != '+') {
                    if (Character.isLetterOrDigit(letter)) {
                        sb.append(letter);
                    } else if (letter == '.') {
                        continue;
                    }
                } else {
                    break;
                }
            }
            if (!sb.toString().isEmpty()) {
                sb.append(splitted[1]);
                result.add(sb.toString());
            }
        }
        return result.size();
    }
}
```


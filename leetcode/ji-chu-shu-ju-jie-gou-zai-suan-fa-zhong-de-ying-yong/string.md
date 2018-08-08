# String

### 13 - Roman to Integer

#### 原题概述

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

#### 题意和分析

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

#### 代码

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

### 12 - Integer to Roman

#### 原题概述

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

#### 题意和分析

上一道题反过来，数字转换为罗马数字，输入的限制为1～3999，

| 基本字符 | I | V | X | L | C | D | M |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 相应的数字 | 1 | 5 | 10 | 50 | 100 | 500 | 1000  |

因为有输入的限制，所以投机取巧可以建立一个数表，每次查表找出最大的当前最大的数，然后减去再继续查表

#### 代码

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

### 344 - Reverse String

#### 原题概述

Write a function that takes a string as input and returns the string reversed.

**Example:**  
Given s = "hello", return "olleh".

#### 题意和分析

转换成字符数组，两个索引往中间走，如果相等或者left大于right就停止。

#### 代码

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

### 14 - Longest Common Prefix

#### 原题概述

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

#### 题意和分析

求一个字符串数组的最长的共同前缀，字符串都是小写字母，这个只能把所有单词排成纵列挨个查了，如果查找的过程中某一个字符串没有了，或者某个字符串的字符不同，那就直接上一轮保存的最长公共前缀。

#### 代码

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

### 6 - ZigZag Conversion

#### 原题概述

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

#### 题意和分析

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

除了第一行和最后一行没有中间形成之字型的数字外，其他行都有，而首位两行中相邻两个元素的index之差跟行数是相关的，为 2\*numRows- 2 （注意空格也算一个位置），根据这个特点，我们可以按顺序找到所有的正常元素在原字符串的位置，将他们按顺序加到新字符串里面。对于在正常字元素出现的位置也是有规律的，每个黑体字元素的位置为 j + 2\*numRows-2 - 2\*i，（其中，j为前一个正常字体元素的列数，i为当前行数）。 比如当n = 4中的黑体字5，它的位置为 1 + 2\*4-2 - 2\*1 = 5，为原字符串的正确位置。当我们知道所有正常字体元素和黑体字元素位置的正确算法，那就可以一次性的把它们按顺序都加到新的字符串里面。

#### 代码

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

### 3 - Longest Substring Without Repeating Characters

#### 原题概述

Given a string, find the length of the **longest substring** without repeating characters.

**Examples:**

Given `"abcabcbb"`, the answer is `"abc"`, which the length is 3.

Given `"bbbbb"`, the answer is `"b"`, with the length of 1.

Given `"pwwkew"`, the answer is `"wke"`, with the length of 3. Note that the answer must be a **substring**, `"pwke"` is a subsequence and not a substring.

#### 题意和分析

给一个字符串，找到其最长的子串（不是子序列），返回这个子串的长度。维护一个滑动窗口，窗口里面的字符都是不重复的。1）首先可以用一个HashMap来记录窗口内的字符和这些字符最后出现的位置，如果窗口右侧移动后发现有重复的字符，那就将left索引指向HashMap里面保存的该字符的位置的下一位，窗口右侧继续移动，同时保持len的最长的值；2）使用HashSet，出现过的字符都放入set中，遇到set中没有的字符就加入set并更新结果result，如果有重复的，从左边开始删除字符，知道删到重复的字符为止。

#### 代码

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

### 5 - Longest Palindromic Substring

#### 原题概述

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

#### 题意和分析

找最长回文子串，给定的字符串最长为1000，1\)

1）首先可以O\(n^2\)，以每个字符为中心然后向两边延伸来检查，保留一个最长的子串即可，这个需要注意一下奇偶（以自己为中心以及以自己旁边的两个字符为中心）；2\)

2）优化的办法是用dp，维护一个二维dp，其中dp\[i\]\[j\]表示字符串区间\[i,j\]是否为回文串，当i==j时，只有一个字符，肯定是回文串；如果i = j + 1，那二者是相邻字符，此时判断s\[i\]是否等于s\[j\]，如果i和j不相邻，i-j&gt;=2的时候，除了判断s\[i\]和s\[j\]相等，还得保证dp\[j+1\]\[i-1\]也得为真，才是回文串，所以递推式为

dp\[i, j\] = 1                                               if i == j为回文串

           = s\[i\] == s\[j\]                                if j = i + 1

           = s\[i\] == s\[j\] && dp\[i + 1\]\[j - 1\]    if j &gt; i + 1     

3）O\(n\)的马拉车算法Manacher's Algorithm，[这里](http://www.cnblogs.com/grandyang/p/4475985.html)有详细介绍

#### 代码

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



### 38 - Count and Say

#### 原题概述

#### 题意和分析

#### 代码

### 151 - Reverse Words in a String

#### 原题概述

#### 题意和分析

#### 代码

### 67 - Add Binary

#### 原题概述

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

#### 题意和分析

将两个string形式的二进制数相加，并且保存在string中，要注意的是如何将string和int之间互相转换，并且每位相加时，会有进位的可能，会影响之后相加的结果。而且两个输入string的长度也可能会不同。创建一个StringBuilder来存相加的结果，从两个string的末尾开始取出字符，然后转为数字，相加，如果大于等于2，则标记进位标志carry，最后将StringBuilder转换成String，因为是从后面开始加，翻转一下。

Time：O\(m\)； Space：O\(m\)，m是较长的那个string的长度。

#### 代码

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

## 计算器

### 227 - Basic Calculator II

#### 原题概述

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

#### 题意和分析

设计一个计算器，输入一个有效的字符串表达式，加减乘除后输出一个整数，除法是地板除法，表达式里的数字都是正整数。eval\(\)是脚本语言中的方法，Java并没有。

相对于I中只有加减，这道题多了乘除，并且没有括号，所以简化了。使用stack来存储计算的中间结果，每次遇到加减法就直接压入；遇到乘除法就先计算一下再压入。

时间上一遍扫过去O\(n\)，空间上开了一个stack，O\(n\)。

当然这道题不用stack也可以，可以把string先转换成char array，然后从左到右运算，维持一个result负责最终结果和一个pre负责遇到乘法除法的时候重算一下先做乘除。复杂度一样。

#### 代码

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



### 316 - Remove Duplicate Letters

#### 原题概述

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

#### 题意和分析

移除重复字母，使每个字母只出现一次，结果按照字母顺序排，而且不能打乱原来的相对位置。

递归的办法，先用哈希表记录每个字母出现的次数，再遍历给定字符串s，找出最小的字母，每比较一个字母，在哈希表中的值减1，如果此时为0了，则不继续遍历了，此时我们记录了一个位置，把字符串s中该位置左边的字符都删掉，右边的所有再出现的该字母也删掉，递归调用此函数即可。时间： O\(26 \* n\) = O\(n\)。

#### 代码

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


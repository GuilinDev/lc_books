# String

## 13 - Roman to Integer

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


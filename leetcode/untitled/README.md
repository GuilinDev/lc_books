---
description: >-
  位操作题目只需要关注简单题目和高频题目，包括判断奇偶，交换两数，变换符号，求绝对值，筛素数与空间压缩，高低位交换，二进制逆序，二进制中1的个数，缺失的数字等。
---

# 6. Bit Manipulation

[https://blog.csdn.net/hk\_john/article/details/70231705](https://blog.csdn.net/hk_john/article/details/70231705)

基本的位操作符有与、或、异或、取反、左移、右移这6种，它们的运算规则如下所示：

| 符号 |  描述 |  运算规则                       |
| :--- | :--- | :--- |
| &       |  与 | 两个位都为1时，结果才为1 |
| \|   |  或     | 两个位都为0时，结果才为0 |
| ^     | 异或 | 两个位相同为0，相异为1 |
| ~    | 取反 | 0变1，1变0 |
| &lt;&lt;  | 左移 | 各二进位全部左移若干位，高位丢弃，低位补0 |
| &gt;&gt;  | 右移 | 各二进位全部右移若干位，对无符号数，高位补0，有符号数，各编译器处理方法不一样，有的补符号位（算术右移），有的补0（逻辑右移） |

注意以下几点：

1．  在这6种操作符，只有~取反是单目操作符，其它5种都是双目操作符。

2．  位操作只能用于整形数据，对float和double类型进行位操作会被编译器报错。

3．  对于移位操作，在微软的VC6.0和VS2008编译器都是采取算术称位即算术移位操作，算术移位是相对于逻辑移位，它们在左移操作中都一样，低位补0即可，但在右移中逻辑移位的高位补0而算术移位的高位是补符号位。如下面代码会输出-4和3。

1. int a = -15, b = 15;  
2. printf\("%d %d\n", a &gt;&gt; 2, b &gt;&gt; 2\);  

因为15=0000 1111\(二进制\)，右移二位，最高位由符号位填充将得到0000 0011即3。-15 = 1111 0001\(二进制\)，右移二位，最高位由符号位填充将得到1111 1100即-4（见注1）。

4．  位操作符的运算优先级比较低，因为尽量使用括号来确保运算顺序，否则很可能会得到莫明其妙的结果。比如要得到像1，3，5，9这些2^i+1的数字。写成int a = 1 &lt;&lt; i + 1;是不对的，程序会先执行i + 1，再执行左移操作。应该写成int a = \(1 &lt;&lt; i\) + 1;

5．  另外位操作还有一些复合操作符，如&=、\|=、 ^=、&lt;&lt;=、&gt;&gt;=。





## 136 - Single Number

### 原题概述

### 题意与分析

### 代码

```java
class Solution {
    public int singleNumber(int[] nums) {
        int result = 0;
        for (int i = 0; i < nums.length; i++) {
            result ^= nums[i];//logical exclusive OR，相同的数就消掉,
        }
        return result;
    }
}
```

## 137 - Single Number II

### 原题概述

### 题意与分析

把每一位数都想成二进制的数，用一个32位的数组来记录每一位上面的1的count值。注意数组计数是从左往右走，而二进制数数是从右往左的；所以数组第0位的count就是二进制最低位上的count的。例如：4的二进制是100（当然作为32位就是前面还一堆了000...000100这样子），3个4的话按照每位相加的话，按照二进制表示法考虑就是300，当然存在数组里面就是003（A\[0\]=0;A\[1\]=0;A\[2\]=3，然后后面到A\[31\]都得0）。

然后对所有数按照二进制表示按位加好后，就要把他还原成所求的值。这里面的想法是，如果一个数字出现了3次，那么这个数字的每一位上面，如果有1那么累加肯定是得3的，如果是0，自然还是0。所以对每一位取余数，得的余数再拼接起来就是我们要找的那个single one。

这里还原的方法是，对32位数组从0开始，对3取余数，因为数组0位置其实是二进制的最低位，所以每次要向左移。用OR（\|）和 + 都可以拼接回来。

### 代码

```java
class Solution {
    public int singleNumber(int[] nums) {
        int[] count = new int[32];
        int result = 0;
        for (int i = 0; i < 32; i++) {
            for (int j = 0; j < nums.length; j++) {
                if (((nums[j] >> i) & 1) == 1) {//nums中当前元素的32位二进制形式，从右边的位开始，每位右移i位，来判断是否为1，然后在数组count中的i位置递增
                    count[i]++;
                }
            }
            //遍历完数组nums里面的所有元素后，除以3再左移还原
            result |= ((count[i] % 3) << i);
        }
        //余数为只出现一次的那个数
        return result;
    }
}
```

## 187 - Repeated DNA Sequence

### 原题概述

All DNA is composed of a series of nucleotides abbreviated as A, C, G, and T, for example: "ACGAATTCCG". When studying DNA, it is sometimes useful to identify repeated sequences within the DNA.

Write a function to find all the 10-letter-long sequences \(substrings\) that occur more than once in a DNA molecule.

**Example:**

```text
Input: s = "AAAAACCCCCAAAAACCCCCCAAAAAGGGTTT"

Output: ["AAAAACCCCC", "CCCCCAAAAA"]
```

### 题意和分析

给一个DNA字符串，找到它的所有长度为10的子串，并且这些子串在原本的DNA字符串中出现了两次或多次；这道题比较大众的解法是用位操作，

 0xfffff的解释：前两位0x表示十六进制，也就是每个数用二进制表示都是4位，比如0x0 = 0000，0x6 = 0110，十六进制的的数字从0 - 15，其中F代表15，写成二进制位1111，所以0xFFFFF = 1111 1111 1111 1111 1111，如果说这样一句话“程序是从内存中的页面x0xA532到0xFEE2读取的，那意思就是下面的页面被加载了 - 1010 0101 0011 0010 到 1111 1110 1110 0010。

### 代码

```java
class Solution {
    public List<String> findRepeatedDnaSequences(String s) {
        List<String> result = new ArrayList<>();
        if (s == null || s.length() < 10) {
            return result;
        }

        Set<Integer> words = new HashSet<>();
        Set<Integer> doubleWords = new HashSet<>();
        char[] map = new char[26];

        //map['A' - 'A'] = 0;Java中整型数组的default值本身就是0
        map['C' - 'A'] = 1;
        map['G' - 'A'] = 2;
        map['T' - 'A'] = 3;

        for (int i = 0; i < s.length() - 9; i++) {
            int v = 0;
            for (int j = i; j < i + 10; j++) {
                v <<= 2; //v左移两位，例如01就变成0100了
                v |= map[s.charAt(j) - 'A']; //append两位， 例如0100 || 11 => 0111
            }

            if (!words.add(v) && doubleWords.add(v)) {
                result.add(s.substring(i , i + 10));
            }
        }
        return result;
    }
}
```

这道题也可以用滑动窗口的办法也可以解决，Set来判断是否重复子串（用HashMap判断是否重复则比较麻烦，需要把子串当作key，出现的次数作为value，来判断是否出现2次或以上），Time：O\(n\), Space O\(n\)

```java
class Solution {
    public List<String> findRepeatedDnaSequences(String s) {
        Set seen = new HashSet();
        Set repeated = new HashSet();
        for (int i = 0; i + 9 < s.length(); i++) {//到倒数第十个循环后就不用
            String ten = s.substring(i, i + 10);
            if (!seen.add(ten)) {//判断条件是说，子串在Set中存在，不能往里面加了
                repeated.add(ten);
            }
        }
        return new ArrayList(repeated);
    }
}
```

## 29 Divide Two Integers

### 原题概述

Given two integers `dividend` and `divisor`, divide two integers without using multiplication, division and mod operator.

Return the quotient after dividing `dividend` by `divisor`.

The integer division should truncate toward zero.

**Example 1:**

```text
Input: dividend = 10, divisor = 3
Output: 3
```

**Example 2:**

```text
Input: dividend = 7, divisor = -3
Output: -2
```

**Note:**

* Both dividend and divisor will be 32-bit signed integers.
* The divisor will never be 0.
* Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: \[−231,  231 − 1\]. For the purpose of this problem, assume that your function returns 231 − 1 when the division result overflows.

### 题意和分析

不让用乘法除法和取余，来实现地板除法，那就只好用位操作了，参考了[这里](https://leetcode.com/problems/divide-two-integers/discuss/13467/Very-detailed-step-by-step-explanation-%28Java-solution%29)的解法。

### 代码

```java
class Solution {
    public int divide(int dividend, int divisor) {
        boolean isNegative = (dividend < 0 && divisor > 0) || (dividend > 0 && divisor < 0) ? true : false;
        long absDividend = Math.abs((long) dividend);
        long absDivisor = Math.abs((long) divisor);
        long result = 0;
        while (absDividend >= absDivisor) {
            long temp = absDivisor, count = 1;
            while (temp <= absDividend) {
                temp <<= 1;
                count <<= 1;
            }
            result += count >> 1;
            absDividend -= temp >> 1;
        }
        return isNegative ? (int) ~result + 1 : (result > Integer.MAX_VALUE ? Integer.MAX_VALUE : (int) result);
    }
}
```

## 461 Hamming Distance

### 原题概述

The [Hamming distance](https://en.wikipedia.org/wiki/Hamming_distance) between two integers is the number of positions at which the corresponding bits are different.

Given two integers `x` and `y`, calculate the Hamming distance.

**Note:**  
 0 ≤ `x`, `y` &lt; 231.

**Example:**

```text
Input: x = 1, y = 4

Output: 2

Explanation:
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑

The above arrows point to positions where the corresponding bits are different.
```

### 题意和分析

求两个数的Hamming Distance，二进制的形式转变多少次即为hamming distance，异或一下数有多少个1即可。

### 代码

```java
class Solution {
    public int hammingDistance(int x, int y) {
        int distance = 0;
        int xor = x ^ y;
        while (xor != 0) {
            xor = xor & (xor - 1);//每次移除掉最后一个1
            distance++;
        }
        return distance;
    }
}
```

Java本身自带的APIbigCount

```java
class Solution {
    public int hammingDistance(int x, int y) {
        return Integer.bitCount(x ^ y);
    }
}
```

## 371 Sum of Two Integers

### 原题概述

Calculate the sum of two integers a and b, but you are **not allowed** to use the operator `+` and `-`.

**Example 1:**

```text
Input: a = 1, b = 2
Output: 3
```

**Example 2:**

```text
Input: a = -2, b = 3
Output: 1
```

### 题意和分析

计算两个整数之和，不能用+和-，摆明了考察位操作的知识。做法就是，由两个数“异或”a^b可得按位相加后没有进位的和； 由“与”a&b可得可以产生进位的地方（二者都为1，结果才为1，两个1相加，进一位）； 由\(a&b\)&lt;&lt;1得到进位后的值（相当于乘以2）。 那么，按位相加后原位和+进位和就是加法的和了，而a^b + \(a&b\)&lt;&lt;1相当于把 + 两边再代入上述三步进行加法计算。直到进位和为0说明没有进位了，则此时原位和即所求和。

### 代码

```java
class Solution {
    public int getSum(int a, int b) {

        int result = a;
        int xor = a ^ b;//得到原味和(没有进位)
        int forward = (a & b ) << 1;//得到进位和
        if(forward != 0) {//进位和不为0,有进位，还得把进位加上，于是递归调用
            result = getSum(xor, forward);
        } else {//没有进位，此时的原味和为所求的值
            result = xor;
        }

        return result;
    }
}
```


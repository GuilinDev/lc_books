---
description: >-
  位操作题目只需要关注简单题目和高频题目，包括判断奇偶，交换两数，变换符号，求绝对值，筛素数与空间压缩，高低位交换，二进制逆序，二进制中1的个数，缺失的数字等。
---

# 6. Bit Manipulation

## 妙用n & \(n - 1\)

n与n-1的区别在于,对于n,从右向左数的第一个"1"开始一直到右,和n-1,完全相反 n&\(n-1\)作用：将n的二进制表示中的最低位为1的改为0，先看一个简单的例子： n = 10100\(二进制），则\(n-1\) = 10011 ==》n&\(n-1\) = 10000 可以看到原本最低位为1的那位变为0。 弄明白了n&\(n-1\)的作用，那它有哪些应用？ 1. 求某一个数的二进制表示中1的个数 while \(n &gt;0 \) { count ++; n &= \(n-1\); }

1. 判断一个数是否是2的方幂 n &gt; 0 && \(\(n & \(n - 1\)\) == 0 \)
2. 计算N!的质因数2的个数。 容易得出N!质因数2的个数 = \[N / 2\] + \[N / 4\] + \[N / 8\] + .... 下面通过一个简单的例子来推导一下过程：N = 10101\(二进制表示） 现在我们跟踪最高位的1，不考虑其他位假定为0， 则在 \[N / 2\] 01000 \[N / 4\] 00100 \[N / 8\] 00010 \[N / 8\] 00001 则所有相加等于01111 = 10000 - 1 由此推及其他位可得：\(10101\)!的质因数2的个数为10000 - 1 + 00100 - 1 + 00001 - 1 = 10101 - 3\(二进制表示中1的个数\)

推及一般N!的质因数2的个数为N - （N二进制表示中1的个数）

## 191 - Number of 1 Bits

### 原题概述

Write a function that takes an unsigned integer and returns the number of '1' bits it has \(also known as the [Hamming weight](http://en.wikipedia.org/wiki/Hamming_weight)\).

**Example 1:**

```text
Input: 11
Output: 3
Explanation: Integer 11 has binary representation 00000000000000000000000000001011 
```

**Example 2:**

```text
Input: 128
Output: 1
Explanation: Integer 128 has binary representation 00000000000000000000000010000000
```

### 题意和分析

这道题是给一个无符号的整数，将之转换成二进制的形式后，总共有多少个1，这样 1的个数也叫做Hamming Weight，就是在一串字符中有多少个非0符号，在信息论，编译原理和密码学中Hamming Weight应用广泛。

我们可以从左到右循环，每次把n向右移动一位，把n和1进行&操作，判断结果是否为1，然后数个数，这种做法会超时。

用n & \(n - 1\)的办法，如果将一个整数减去1，那么都是把最右边那个1变成0，如果这个1右边还有0，所有的0全部变成1，但是这个1左边数字均保持不变；把这个整数和它减去1的结果进行&的话，就可以得到最右边的1。比如110，减去1得101，二者&操作后得100，消去了最右边的1，依次类推。

Time： O\(1\)； Space： O\(1\)；

### 代码

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int result = 0;
        while (n != 0) { //while (n)
            result++;
            n = n & (n - 1);
        }
        return result;
    }
}
```

## 231 - Power of Two

### 原题概述

Given an integer, write a function to determine if it is a power of two.

**Example 1:**

```text
Input: 1
Output: true 
Explanation: 20 = 1
```

**Example 2:**

```text
Input: 16
Output: true
Explanation: 24 = 16
```

**Example 3:**

```text
Input: 218
Output: false
```

### 题意和分析

判断一个是否是2的幂，这道题自然可以用recursive或者iterative来做\(O\(log n\)\)，这里我们还是用n & \(n - 1\),

* n = 2 ^ 0 = 1 = 0b0000...00000001, and \(n - 1\) = 0 = 0b0000...0000.
* n = 2 ^ 1 = 2 = 0b0000...00000010, and \(n - 1\) = 1 = 0b0000...0001.
* n = 2 ^ 2 = 4 = 0b0000...00000100, and \(n - 1\) = 3 = 0b0000...0011.
* n = 2 ^ 3 = 8 = 0b0000...00001000, and \(n - 1\) = 7 = 0b0000...0111.

因此我们看出 如果n是2的方幂，那么n & \(n-1\) == 0b0000...0000 == 0；

否则比如 n =14 = 0b0000...1110, and \(n - 1\) = 13 = 0b0000...1101，n & \(n-1\) == 0b0000...1100 != 0.

另外n当然不能是0或负数。

Time O\(1\) ：Space：O\(1\)；

### 代码

```java
class Solution {
    public boolean isPowerOfTwo(int n) {
        return n > 0 && ((n & (n - 1)) == 0);
    }
}
```

## 异或XOR操作

XOR交换两个整数的值时可以不用第三个参数。 

如a=11,b=9.

以下是二进制 

a=a^b=1011^1001=0010; 

b=b^a=1001^0010=1011; 

a=a^b=0010^1011=1001; 

这样一来a=9, b=11了。

## 136 - Single Number

### 原题概述

Given a **non-empty** array of integers, every element appears _twice_ except for one. Find that single one.

**Note:**

Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

**Example 1:**

```text
Input: [2,2,1]
Output: 1
```

**Example 2:**

```text
Input: [4,1,2,1,2]
Output: 4
```

### 题意和分析

给一个数组，每个数字出现两次，找出只出现过一次的数字。中规中矩用HashMap来统计次数自然是没什么难度，这里用位操作中XOR，当两个数相同的时候为0，位数相互进行XOR后，最后的结果一定会是只出现一次的元素。

### 代码

```java
class Solution {
    public int singleNumber(int[] nums) {
        int result = 0;
        for (int num : nums) {
            result ^= num;
        }
        return result;
    }
}
```

## 137 - Single Number II

### 原题概述 <a id="yuan-ti-gai-shu"></a>

### 题意和分析 <a id="ti-yi-he-fen-xi"></a>

### 代码 <a id="dai-ma"></a>

## 260 - Single Number III

### 题目 <a id="yuan-ti-gai-shu"></a>

Given an array of numbers `nums`, in which exactly two elements appear only once and all the other elements appear exactly twice. Find the two elements that appear only once.

**Example:**

```text
Input:  [1,2,1,3,2,5]
Output: [3,5]
```

**Note**:

1. The order of the result is not important. So in the above example, `[5, 3]` is also correct.
2. Your algorithm should run in linear runtime complexity. Could you implement it using only constant space complexity?

### 分析 <a id="ti-yi-he-fen-xi"></a>

这道题考察的是位操作，需要比较清楚的二进制编码，反码，补码的知识；首先第一次遍历将array中所有的元素进行XOR操作，得到的结果diff是需要找出的两个只出现一次的元素相互之间的XOR操作，这时候将diff和其负数作&与操作，得到新的diff包含了所有1的位置，然后作第二次遍历，同样，出现两次的元素自我抵消掉；剩下的两个单独数字之中，其中有一个必然与diff作与&操作为0，记录，另一个不为零，记录。

### 代码 <a id="dai-ma"></a>

```java
class Solution {
    public int[] singleNumber(int[] nums) {
        
        int diff = 0;
        
        //找到两个只出现一次的数字的xor后的结果
        for (int num : nums) {
            diff ^= num;
        }
        
        diff &= -diff;//与自己的补码进行与操作，得到最后一个bit，0或者1（自己与自己负数-补码，只是最后一位相同，别的31位都不同）
        
        int[] result = new int[2];
        for (int num : nums) {
            if ((diff & num) == 0) {
                result[0] ^= num; //用xor是因为要把diff &= -diff这步得到的bit相同的num彼此消掉（别的num出现两次）
            } else {
                result[1] ^= num; //用xor是因为要把diff &= -diff这步得到的bit不同的num彼此消掉（别的num出现两次）
            }
        }
        
        return result;
    }
}
```

## 268 - Missing Number

### 原题概述

Given an array containing n distinct numbers taken from `0, 1, 2, ..., n`, find the one that is missing from the array.

**Example 1:**

```text
Input: [3,0,1]
Output: 2
```

**Example 2:**

```text
Input: [9,6,4,2,3,5,7,0,1]
Output: 8
```

**Note**:  
Your algorithm should run in linear runtime complexity. Could you implement it using only constant extra space complexity?

### 题意和分析

给一个未排序的数组，里面有n个数字，范围从0到n\(本来有n+1个数字\)，找到缺失的那个数，要求线型时间复杂度和常数空间。

我们可以用等差数列的求和公司n \* \(n - 1\) / 2求得一个值Sum，然后遍历整个数组，将每个元素值从Sum里面减掉，最后剩下的数字就是缺失的数字。

这里我们还是用位操作异或XOR来做，因为0到n之间少了一个数，因为 a^b^b =a，异或两次等于原来的数，将这个少了一个数的数组和0到n之间完整的数组异或XOR一下，那么相同的数字都变为0了，最后剩下的就是缺失了的那个数字了。比如5==101 ^ 6==110 == 011；数组\[0,1,3,4\]，result初始为4，循环的值分别为4^0^0=4，4^1^1=4，4^2^3=5，5^3^4=2，最后2作为缺失的数字返回。

### 代码

```java
class Solution {
    public int missingNumber(int[] nums) {
        int result = nums.length;
        for (int i = 0; i < nums.length; i++) {
            result = result ^ i ^ nums[i]; // a^b^b = a
        }
        return result;
    }
}
```

面试中有可能给定的数组是排好序的，那么就用二分查找法来做，找出中间元素nums\[mid\]的下标mid，然后用元素值nums\[mid\]和下标值mid之间做对比，如果元素值大于下标值，则说明缺失的数字在左边，此时将right赋为mid，反之则将left赋为mid+1。

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
    int result = 0;
    for (int i = 0; i < 32; i++) {
      int sum = 0; //位操作一个整数就是32位
      for (int num : nums) {
        sum += (num >> i) & 1; //通过右移计算该位上，所有数组中数字的1的个数
      }
      result |= (sum % 3) << i; // 除以3相当于“去掉三个重复数”，然后再左移还原i位，然后|拼接还原
    }
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


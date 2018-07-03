# 妙用n & \(n-1\)

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




# 位数移动

## 7 - Reverse Integer

### 原题概述

Given a 32-bit signed integer, reverse digits of an integer.

**Example 1:**

```text
Input: 123
Output: 321
```

**Example 2:**

```text
Input: -123
Output: -321
```

**Example 3:**

```text
Input: 120
Output: 21
```

**Note:**  
Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: \[−231,  231 − 1\]. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.

### 题意和分析

将一个无符号的正整数颠倒顺序，注意32位是整数的范围，如果超出则返回0。通过取模得到尾数，拿到后在新数中每次乘以10，而原来的数每次除以10消除掉尾数，判断是否overflow就减去尾数再除以10，看是否等于之前的result，如果不是表面overflow了，返回0，否则正常计算。

时间O\(n\)，空间O\(1\)。

### 代码

```java
class Solution {
    public int reverse(int x) {
        int result = 0;
        while (x != 0) {
            int tail = x % 10;
            int newResult = result * 10 + tail; //判断是否溢出，把之前的操作反过来做一遍即可
            if ((newResult - tail) / 10 != result) {
                return 0;
            }
            result = newResult;
            x /= 10;
        }
        return result;
    }
}
```


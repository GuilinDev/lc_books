# String和数转换

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


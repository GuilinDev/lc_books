# 回文结构

## 9 - Palindrome Number

### 原题概述

Determine whether an integer is a palindrome. An integer is a palindrome when it reads the same backward as forward.

**Example 1:**

```text
Input: 121
Output: true
```

**Example 2:**

```text
Input: -121
Output: false
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
```

**Example 3:**

```text
Input: 10
Output: false
Explanation: Reads 01 from right to left. Therefore it is not a palindrome.
```

**Follow up:**

Coud you solve it without converting the integer to a string?

### 题意和分析

给一个整数，判定它是否是一个回文数字，既然是整数，所以assume就不会出现010这样的非法输入。不要转换成String用index来判断。首先负数和除0之外能整除10的数都不会是回文数字，因为不会有leading zeros；对于0和正整数，从input的末尾的数开始创建一个新的数，到达或超过原先input数字一半的时候判断新老数字就可以知道是否是回文数字。

### 代码

```java
class Solution {
    public boolean isPalindrome(int x) {
        if (x < 0 || (x != 0 && x % 10 == 0)) { //负数和除0之外的能整除10的数肯定不是
            return false;
        }
        int halfReverse = 0;
        while (x > halfReverse) {//到达一半，比如1221这样的数，或者超过一半，比如12321这样的数
            halfReverse = halfReverse * 10 + x % 10;//从x最后一个数字当作halfReverse的第一个数字开始算
            x /= 10;
        }
        return (x == halfReverse || x == halfReverse / 10);//偶数位两个数字就相同，奇数位就把x中原来中间的那个数 - halfReverse中末尾的那个数去掉再判断
    }
}
```




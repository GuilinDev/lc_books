# Array中做加减法

## 66 - Plus One

### 原题概述

> Given a **non-empty** array of digits representing a non-negative integer, plus one to the integer.
>
> The digits are stored such that the most significant digit is at the head of the list, and each element in the array contain a single digit.
>
> You may assume the integer does not contain any leading zero, except the number 0 itself.
>
> **Example 1:**
>
> ```text
> Input: [1,2,3]
> Output: [1,2,4]
> Explanation: The array represents the integer 123.
> ```
>
> **Example 2:**
>
> ```text
> Input: [4,3,2,1]
> Output: [4,3,2,2]
> Explanation: The array represents the integer 4321.
> ```

### 题意和分析

作为开胃菜，这道题目很简单，就是需要考虑数组中最后一位加1的时候进位的问题。

Time: O\(n\)

### 代码

```java
class Solution {
    public int[] plusOne(int[] digits) {
        int carry = 1;
        
        //如果carry==0，没有往前进位的情况，就终止循环，不再加了
        for (int i = digits.length - 1; i >= 0 && carry > 0; i--) {
            int sum = digits[i] + carry;
            digits[i] = sum % 10;
            carry = sum / 10;
        }
        
        //处理数组中最后一次加法（第0位）可能往前进位的情况
        if (carry == 0) {//没有进位了，直接返回
            return digits;
        } else { //有进位，新建一个长度多一位的array
            int[] newDigits = new int[digits.length + 1];
            newDigits[0] = 1;
            for (int j = 1; j <= digits.length; j++) {
                newDigits[j] = digits[j - 1];
            }
            return newDigits;
        }
    }
}
```




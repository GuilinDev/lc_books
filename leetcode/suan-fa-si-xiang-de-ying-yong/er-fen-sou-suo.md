# Binary Search

## 50 Power\(x, n\)

### 原题概述

Implement [pow\(_x_, _n_\)](http://www.cplusplus.com/reference/valarray/pow/), which calculates _x_ raised to the power _n_ \(xn\).

**Example 1:**

```text
Input: 2.00000, 10
Output: 1024.00000
```

**Example 2:**

```text
Input: 2.10000, 3
Output: 9.26100
```

**Example 3:**

```text
Input: 2.00000, -2
Output: 0.25000
Explanation: 2-2 = 1/22 = 1/4 = 0.25
```

**Note:**

* -100.0 &lt; _x_ &lt; 100.0
* _n_ is a 32-bit signed integer, within the range \[−231, 231 − 1\]

### 题意和分析

利用递归，每次对n进行折半处理，n最终会变成0，任何数的0次方都是1，这时候回溯，如果n是偶数，直接将上次递归的道德值算个平方在返回；如果是奇数，还需要乘上x的值。

### 代码

```java
class Solution {
    public double myPow(double x, int n) {
        if (n == 0) {//递归的基线条件
            return 1;
        }
        double half = myPow(x, n /2);
        if (n % 2 == 0) {//偶数
            return half * half;
        } else if (n > 0) {//奇数，并且n不等于0的时候
            return half * half * x;
        } else {//n小于等于0，刚才多乘了一次
            return half * half / x;
        }
    }
}
```

## 29 Divide Two Integers

### 原题概述

### 题意和分析

### 代码

## 287 Find the Duplicate Number 

### 原题概述

### 题意和分析

### 代码

## 69 Sqrt\(x\)

### 原题概述

### 题意和分析

### 代码


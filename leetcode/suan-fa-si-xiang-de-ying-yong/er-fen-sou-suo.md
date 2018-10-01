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

利用递归，每次对n进行折半处理，n最终会变成0，任何数的0次方都是1，这时候回溯，如果n是偶数，直接将上次递归的道德值算个平方在返回；如果是奇数，还需要乘上x的值，比如2^50 = 2^25 \* 2^25，而2^51 = 2 ^25 \* 2^ 25 \* 2。

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

## 69 Sqrt\(x\)

### 原题概述

Implement `int sqrt(int x)`.

Compute and return the square root of _x_, where _x_ is guaranteed to be a non-negative integer.

Since the return type is an integer, the decimal digits are truncated and only the integer part of the result is returned.

**Example 1:**

```text
Input: 4
Output: 2
```

**Example 2:**

```text
Input: 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since 
             the decimal part is truncated, 2 is returned.
```

### 题意和分析

求平方根，保证输入是正数，向下返回值。

1）用二分搜索来在\[0,x\]之间找平方根，找到一个不大于目标值的数；

2）使用牛顿迭代法，这个主要是逼近法求方程的根（[http://www.cnblogs.com/AnnieKim/archive/2013/04/18/3028607.html](http://www.cnblogs.com/AnnieKim/archive/2013/04/18/3028607.html)），这里是求x^2的解，因此方程f\(x\) = x^2 - n， 相当于求解f\(x\)=0的解，如图所示

![](../../.gitbook/assets/image%20%288%29.png)

首先取x0，如果x0不是解，做一个经过\(x0,f\(x0\)\)这个点的切线，与x轴的交点为x1。

同样的道理，如果x1不是解，做一个经过\(x1,f\(x1\)\)这个点的切线，与x轴的交点为x2。

以此类推。

以这样的方式得到的xi会无限趋近于f\(x\)=0的解。

判断xi是否是f\(x\)=0的解有两种方法：

一是直接计算f\(xi\)的值判断是否为0，二是判断前后两个解xi和xi-1是否无限接近。

经过\(xi, f\(xi\)\)这个点的切线方程为f\(x\) = f\(xi\) + f’\(xi\)\(x - xi\)，其中f'\(x\)为f\(x\)的导数，本题中为2x。令切线方程等于0，即可求出xi+1=xi - f\(xi\) / f'\(xi\)。

继续化简，xi+1=xi - \(xi2 - n\) / \(2xi\) = xi - xi / 2 + n / \(2xi\) = xi / 2 + n / 2xi = \(xi + n/xi\) / 2。

### 代码

二分法搜索合适的数

```java
class Solution {
    public int mySqrt(int x) {
        if (x <= 1) {
            return x;
        }
        int left = 0, right = x;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (x / mid >= mid) {
                left = mid + 1; //这里是mid+1是因为最后返回的是right-1
            } else {
                right = mid;
            }
        }
        return right - 1;
    }
}
```

牛顿迭代法

```java
class Solution {
    public int mySqrt(int x) {
        if (x == 0) {
            return 0;
        }
        double last = 0;
        double result = 1;
        while (result != last) {
            last = result;
            result = (result + x / result) / 2;
        }
        return (int)result;
    }
}
```


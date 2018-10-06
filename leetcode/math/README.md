# 5. Math

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

## 204 Count Primes

### 原体概述

Count the number of prime numbers less than a non-negative number, **n**.

**Example:**

```text
Input: 10
Output: 4
Explanation: There are 4 prime numbers less than 10, they are 2, 3, 5, 7.
```

### 题意和分析

这道题比较简单，给一个非负整数n，找到小于n的所以素数，不包括n自己。正常的做法就是从2循环到n-1，每个数都判断下是否是素数，判断是否是素数的办法如下：

```java
//判断一个数是否是质数（素数）
public boolean isPrimeNumber(int num){
    if(num == 2) return true;//2特殊处理
    if(num < 2 || num % 2 == 0) return false;//识别小于2的数和偶数
    for(int i=3; i<=Math.sqrt(num); i+=2){//从3开始，到平方根结束，每次+2，因为偶数肯定不是质数
        if(num % i == 0){//识别被奇数整除
            return false;
        }
    }
    return true;
}
```

在Discuss区看到一个空间换时间的做法（[https://leetcode.com/problems/count-primes/discuss/57588/My-simple-Java-solution](https://leetcode.com/problems/count-primes/discuss/57588/My-simple-Java-solution)），从2开始遍历，然后每次遇到质数的时候就开始计算后面的数字，标记true或者false，因为如果是合数则一定可以被前面的数乘以得到（证明略），接下来遇到即可直接判断。

### 代码

```java
class Solution {
    public int countPrimes(int n) {
        if (n < 2) {
            return 0;
        }
        boolean[] notPrime = new boolean[n];
        int count = 0;
        for (int i = 2; i < n; i++) {
            if (notPrime[i] == false) {//default值为false
                count++;
                for (int j = 2; i*j < n; j++) {//j从2开始
                    notPrime[i*j] = true;
                }
            }
        }
        return count;
    }
}
```


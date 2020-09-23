# 5. Math

包括位数移动，矩阵，数位分离，进制转换，高精度运算（Add Strings Add BinaryAdd Two Numbers，Multiply Strings），计算几何（Max Point on a Plane）等数学问题

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

将一个无符号的正整数颠倒顺序，注意32位是整数的范围，如果超出则返回0。通过取模得到尾数，拿到后在新数中每次乘以10，而原来的数每次除以10消除掉尾数，判断是否overflow就减去尾数再除以10，看是否等于之前的result，如果不是就表明overflow了，返回0，否则正常计算。

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

正常做法

```java
class Solution {
   public int reverse(int x) {
      long result = 0;
      while (x != 0) {
         result = (result * 10) + (x % 10); // 不用单独判断正负号
         if (result > Integer.MAX_VALUE || result < Integer.MIN_VALUE) {
            return 0;
         }
         x /= 10;
      }
      return (int)result;
   }
}
```

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

## 258 Add Digits

### 原题概述

Given a non-negative integer `num`, repeatedly add all its digits until the result has only one digit.

**Example:**

```text
Input: 38
Output: 2 
Explanation: The process is like: 3 + 8 = 11, 1 + 1 = 2. 
             Since 2 has only one digit, return it.
```

**Follow up:**  
Could you do it without any loop/recursion in O\(1\) runtime?

### 题意和分析

这个题比较简单，follow up是常数级的复杂度；如果用正常的loop或者递归的话是线性时间复杂度，因此需要略微取巧。

### 代码

正常的loop

```java
class Solution {
    public int addDigits(int num) {
        if (num < 10) {
            return num;
        }
        int temp = num;
        int result = 0;
        while (true) {
            while (temp != 0) {
                result += temp % 10;
                temp /= 10;
            }
            if (result < 10) {
                return result;
            } else {
                temp = result;
                result = 0;
            }
        }
    }
}
```

递归

```java
class Solution {
    public int addDigits(int num) {
        if (num < 10) {
            return num;
        }
        return addDigits(num % 10 + num / 10);
    }
}
```

接下来看常数的解法，对9进行取模,当作9进制来算（证明略），[参考这里](https://leetcode.com/problems/add-digits/discuss/68622/Java-Code-with-Explanation)

```java
class Solution {
    public int addDigits(int num) {
        if (num < 10) {
            return num;
        } else if (num % 9 == 0) {
            return 9;
        } else {
            return num % 9;
        }
    }
}
```

## 172 Factorial Trailing Zeros

### 原体概述

Given an integer n, return the number of trailing zeroes in n!.

**Example 1:**

```text
Input: 3
Output: 0
Explanation: 3! = 6, no trailing zero.
```

**Example 2:**

```text
Input: 5
Output: 1
Explanation: 5! = 120, one trailing zero.
```

**Note:** Your solution should be in logarithmic time complexity.

### 题意和分析

题目要求用对数级复杂度O\(logn\)，如果使用朴素解法就是先求出`n!`的值然后取模看最后有多少个0，这个会超出范围；O\(logn\)的解法：考虑`n!`的质数因子，0的得到总是2和5相乘来的，因此需要计算2和5的个数，就解决了， 例如：n=5，\(5\*4\*3\*2\*1\) = \(5\*2\*2\*3\*2\*1\)，包含1个5和3个2，因此后缀0的个数是1； 例如：n=11 \(...\)包含2个5和3个2，因为468可以分解成2，所以质数因子中2的个数总是多于5的个数，因此只要计算5的个数就知道多少个0了； 还有一点要注意的就是25这种，5和5相乘的结果，所以，应该计算n/5里面有多少个5，也就相当于看n里面有多少个25，还有125，625.等等

### 代码

```java
class Solution {
    public int trailingZeroes(int n) {
        int answer = 0;
        while (n > 0) {
            answer += n/5;//这里n/5的个数表示有多少个质数因子5，所以是累加
            n /= 5;
        }
        return answer;
    }
}
```

## 171 Excel Sheet Column Number

### 原体概述

Given a column title as appear in an Excel sheet, return its corresponding column number.

For example:

```text
    A -> 1
    B -> 2
    C -> 3
    ...
    Z -> 26
    AA -> 27
    AB -> 28 
    ...
```

**Example 1:**

```text
Input: "A"
Output: 1
```

**Example 2:**

```text
Input: "AB"
Output: 28
```

**Example 3:**

```text
Input: "ZY"
Output: 701
```

### 题意和分析

给一个字符串，返回相应的数字。相当于一个26进制转换数，每次多一个字母都会乘以26.

### 代码

```java
class Solution {
    public int titleToNumber(String s) {
        if (s == null) {
            return 0;
        }
        int result =0;
        for (int i = 0; i < s.length(); i++) {
            result = result * 26 + (s.charAt(i) - 'A' + 1);
        }
        return result;
    }
}
```

## 319 Bulb Switcher

### 原题概述

There are n bulbs that are initially off. You first turn on all the bulbs. Then, you turn off every second bulb. On the third round, you toggle every third bulb \(turning on if it's off or turning off if it's on\). For the i-th round, you toggle every i bulb. For the n-th round, you only toggle the last bulb. Find how many bulbs are on after n rounds.

**Example:**

```text
Input: 3
Output: 1 
Explanation: 
At first, the three bulbs are [off, off, off].
After first round, the three bulbs are [on, on, on].
After second round, the three bulbs are [on, off, on].
After third round, the three bulbs are [on, off, off]. 

So you should return 1, because there is only one bulb is on.
```

### 题意和分析

 有n个灯泡，第一次打开所有的灯泡，第二次每2个更改灯泡的开关状态，第三次每三个更改灯泡的开关状态，以此类推，第n次每n个更改灯泡的状态，求n次后，所有亮的灯泡的个数。这个一看就是找规律的题，不可能一轮一轮去算，这道题的规律就是画出例子后看哪些灯是亮的，最后发现完全平方数的位置总是亮着的，于是求1到n的完全平方数就行了。如果follow up是按1，3，5，7/2，4，6，8这样来toggle， 那么toggle的次数跟奇数因子的数字有关（不包括1），只要有奇数个奇因子，那么灯就是灭的，只要有偶数个奇因子，那么灯就是亮的，比如6（2X3\)，两个奇数因子，亮的，8（2X2X2），三个，灭的。

### 代码

```java
class Solution {
    public int bulbSwitch(int n) {
        if (n < 1) {
            return 0;
        }
        return (int)Math.sqrt(n);
    }
}
```

## 223 Rectangle Area

### 原题概述

Find the total area covered by two **rectilinear** rectangles in a **2D** plane.

Each rectangle is defined by its bottom left corner and top right corner as shown in the figure.

![Rectangle Area](https://assets.leetcode.com/uploads/2018/10/22/rectangle_area.png)

**Example:**

```text
Input: A = -3, B = 0, C = 3, D = 4, E = 0, F = -1, G = 9, H = 2
Output: 45
```

**Note:**

Assume that the total area is never beyond the maximum possible value of **int**.

### 题意和分析

这道题考察我们能否全面的考虑到所有可能。如果两个矩形没有重叠部分，则直接计算两个矩形面积之和就行了。如果两个矩形有重叠部分，则要将重叠部分减去。如何判断两个矩形没有重叠部分呢，如果一个矩形右上角的横坐标比另一个矩形左下角的横坐标要小，或者一个矩形右上角的纵坐标比另一个矩形左下角的纵坐标要小，则两个矩形是不重叠的。因为两个矩形的坐标都可能比对方小，所以我们一共有四种可能情况是不重叠的。如果重叠的话，计算重叠部分面积就是四个横坐标中中间那两个和四个纵坐标中间那两个。

### 代码

```java
class Solution {
    public int computeArea(int A, int B, int C, int D, int E, int F, int G, int H) {
        int area1 = Math.abs(A - C) * Math.abs(B - D);
        int area2 = Math.abs(E - G) * Math.abs(F - H);
        
        //分别是横坐标相差和纵坐标，求出横纵坐标的相差，乘以得出重叠区
        int common = overlap(A, C, E, G) * overlap(B, D, F, H); 
        
        return area1 + area2 - common; //两个矩形的面积相加，然后减去一个相交矩形的面积
    }
    private int overlap(int lowerLeft1, int upperRight1, int lowerLeft2, int upperRight2) {
        if (lowerLeft1 >= upperRight2 || lowerLeft2 >= upperRight1) {//两个矩形不相交
            return 0;
        }
        //两个lowerleft中的较大者在上面，两个upperRight中的较小者在下面，二者中间是重叠区域的长和宽
        return Math.abs(Math.max(lowerLeft1, lowerLeft2) - Math.min(upperRight1, upperRight2));
    }
}
```

## 149 Max Point On a Line

### 原体概述

Given _n_ points on a 2D plane, find the maximum number of points that lie on the same straight line.

**Example 1:**

```text
Input: [[1,1],[2,2],[3,3]]
Output: 3
Explanation:
^
|
|        o
|     o
|  o  
+------------->
0  1  2  3  4
```

**Example 2:**

```text
Input: [[1,1],[3,2],[5,3],[4,1],[2,3],[1,4]]
Output: 4
Explanation:
^
|
|  o
|     o        o
|        o
|  o        o
+------------------->
0  1  2  3  4  5  6
```

### 题意和分析

给一堆二维点，找出一条直线包含最多的点，两点确定一条直线（非重复的两点），直线的方程是y = ax + b； 两个点\(x1, y1\)和\(x2, y2\)的斜率k表示为\(y2 - y1\) / \(x2 - x1\)，斜率使用hashmap保存除数和被除数， 但当x1 = x2时斜率无法计算，需要特殊处理。用HashMap来记录斜率和共线点个数之间的映射，其中第一种重合点的情况假定其斜率为INT\_MIN，第二种情况假定其斜率为INT\_MAX，这样都可以用map映射了。我们还需要顶一个变量duplicate来记录重合点的个数，最后只需和哈希表中的数字相加即为共线点的总数。

### 代码

```java
/**
 * Definition for a point.
 * class Point {
 *     int x;
 *     int y;
 *     Point() { x = 0; y = 0; }
 *     Point(int a, int b) { x = a; y = b; }
 * }
 */
class Solution {
    public int maxPoints(Point[] points) {
        int result = 0;
        for (int i = 0; i < points.length; i++) {
            Map<Map<Integer, Integer>, Integer> map = new HashMap<>();
            int duplicate = 1;//重复的点，不能确定一条直线
            for (int j = i + 1; j < points.length; j++) {
                if (points[i].x == points[j].x && points[i].y == points[j].y) {//重复的点
                    duplicate++;
                    continue;
                }
                int dx = points[j].x - points[i].x;
                int dy = points[j].y - points[i].y;
                int d = gcd(dx, dy);//找到最大公约数
                Map<Integer, Integer> t = new HashMap<>();
                t.put(dx/d, dy/d);
                map.put(t, map.getOrDefault(t, 0) + 1);
            }
            result = Math.max(result, duplicate);
            for (Map.Entry<Map<Integer, Integer>, Integer> entry : map.entrySet()) {
                result = Math.max(result, entry.getValue() + duplicate);
            }
        }
        return result;
    }

    private int gcd(int a, int b) {
        return (b == 0) ? a : gcd(b, a % b);//最大公约数的办法，注意递归的办法
    }
}
```

## 263 Ugly Number

### 题目

Write a program to check whether a given number is an ugly number.

Ugly numbers are **positive numbers** whose prime factors only include `2, 3, 5`.

**Example 1:**

```text
Input: 6
Output: true
Explanation: 6 = 2 × 3
```

**Example 2:**

```text
Input: 8
Output: true
Explanation: 8 = 2 × 2 × 2
```

**Example 3:**

```text
Input: 14
Output: false 
Explanation: 14 is not ugly since it includes another prime factor 7.
```

**Note:**

1. `1` is typically treated as an ugly number.
2. Input is within the 32-bit signed integer range: \[−231,  231 − 1\].

### 分析

看给定的一个数是否是正整数并且是否只包含2， 3，5这三个因子，做法就是遍历2，3，5，如果可以整除就逮住一直除，最后判断是否为1，如果除到最后不为1就说明还有别的因子，不是丑数。

### 代码

```java
class Solution {
    public boolean isUgly(int num) {
        if (num <= 0) {
            return false;
        }
        int[] divisions = new int[]{2, 3, 5};
        for (int division : divisions) {
            while (num % division == 0) {
                num /= division;
            }
        }
        return num == 1;
    }
}
```

## 264 Ugly Number II

### 题目

Write a program to find the `n`-th ugly number.

Ugly numbers are **positive numbers** whose prime factors only include `2, 3, 5`. 

**Example:**

```text
Input: n = 10
Output: 12
Explanation: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 is the sequence of the first 10 ugly numbers.
```

**Note:**  

1. `1` is typically treated as an ugly number.
2. `n` **does not exceed 1690**.

### 分析

这个题用263的方式来判断是否ugly number，逐个检查会超时。

ugly number的顺序是这样：1, 2, 3, 4, 5, 6, 8, 9, 10, 12, 15, …，所有的数只能被2，3，5整除，其中一个方式可以把这个数列分成三个groups：

\(1\) 1×2, 2×2, 3×2, 4×2, 5×2, 6×2, 8×2… 

\(2\) 1×3, 2×3, 3×3, 4×3, 5×3, 6×3, 8×3,… 

\(3\) 1×5, 2×5, 3×5, 4×5, 5×5, 6×5, 8×5,…

可以看到这三个ugly number的子序列就是本来的序列\(1, 2, 3, 4, 5, 6, 8, 9, 10, 12, 15, …\)分别乘以2，3，5，如此我们可以在三个子序列中挑一个最小的值出来（这样能保证本来的ugly number序列是从小到大按顺序增加的），创建三个索引记录三个子序列的位置，三者加起来一共遍历n次为止。

### 代码

直接应用上述思想的递推式

```java
class Solution {
    public int nthUglyNumber(int n) {
        int index2 = 0;
        int index3 = 0;
        int index5 = 0;
        int[] dp = new int[n]; //dp[i]表示第i个ugly number
        
        dp[0] = 1;
        for (int i = 1; i < n; i++) { //dp[0]已有值
            dp[i] = Math.min(dp[index2] * 2, Math.min(dp[index3] * 3, dp[index5]*5));
            if (dp[i] == dp[index2] * 2) {
                index2++;
            }
            if (dp[i] == dp[index3] * 3) {
                index3++;
            }
            if (dp[i] == dp[index5] * 5) {
                index5++;
            }
        }
        return dp[n-1];
    }
}
```

考虑状态压缩

```java
class Solution {
    public int nthUglyNumber(int n) {
        
        int num_2 = 1;
        int num_3 = 1;
        int num_5 = 1;
        
        int result = 1;
        
        for (int i = 1; i < n; i++) { 
            result = Math.min(num_2 * 2, Math.min(num_3 * 3, num_5 * 5));
            if (result == num_2 * 2) {
                num_2 *= 2;
            }
            if (result == num_3 * 3) {
                num_3 *= 3;
            }
            if (result == num_5 * 5) {
                num_5 *= 5;
            }
        }
        return result;
    }
}
```

利用优先队列来保持顺序

```java
class Solution {
    public int nthUglyNumber(int n) {
        PriorityQueue<Long> pq = new PriorityQueue<>();
        pq.offer(1L);
        
        for (int i = 1; i < n; i++) {
            long temp = pq.poll();
            
            // 去重
            while (!pq.isEmpty() && pq.peek() == temp) {
                temp = pq.poll();
            }
            
            // 更新pq中丑数，保持顺序
            pq.offer(temp * 2);
            pq.offer(temp * 3);
            pq.offer(temp * 5);
        }
        
        return pq.poll().intValue();
    }
}
```

也可以用TreeSet，并且不用考虑去重

```java
class Solution {
    public int nthUglyNumber(int n) {
        TreeSet<Long> set = new TreeSet<>();
        set.add(1L);
        
        for (int i = 1; i < n; i++) {
            long first = set.pollFirst();
            
            // 更新pq中丑数，保持顺序
            set.add(first * 2);
            set.add(first * 3);
            set.add(first * 5);
        }
        
        return set.first().intValue();
    }
}
```


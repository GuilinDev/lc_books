# 计算器

## 227 - Basic Calculator II

### 原题概述

Implement a basic calculator to evaluate a simple expression string.

The expression string contains only **non-negative** integers, `+`, `-`, `*`, `/` operators and empty spaces . The integer division should truncate toward zero.

**Example 1:**

```text
Input: "3+2*2"
Output: 7
```

**Example 2:**

```text
Input: " 3/2 "
Output: 1
```

**Example 3:**

```text
Input: " 3+5 / 2 "
Output: 5
```

**Note:**

* You may assume that the given expression is always valid.
* **Do not** use the `eval` built-in library function.

### 题意和分析

设计一个计算器，输入一个有效的字符串表达式，加减乘除后输出一个整数，除法是地板除法，表达式里的数字都是正整数。eval\(\)是脚本语言中的方法，Java并没有。

相对于I中只有加减，这道题多了乘除，并且没有括号，所以简化了。使用stack来存储计算的中间结果，每次遇到加减法就直接压入；遇到乘除法就先计算一下再压入。

时间上一遍扫过去O\(n\)，空间上开了一个stack，O\(n\)。

当然这道题不用stack也可以，可以把string先转换成char array，然后从左到右运算，维持一个result负责最终结果和一个pre负责遇到乘法除法的时候重算一下先做乘除。复杂度一样。

### 代码

Stack

```java
class Solution {
    public int calculate(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        Stack<Integer> stack = new Stack<>();
        int num = 0;//转换数字所用，初始为0
        char sign = '+'; //记录本次数据之前的运算符号，初始值为'+'
        for (int i = 0; i <s.length(); i++) {
            if (Character.isDigit(s.charAt(i))) {//如果是数字，连续出现就会每次乘以10，比如8+123*5
                num = num * 10 + s.charAt(i) - '0';
            }

            if ((!Character.isDigit(s.charAt(i))//非数字则是运算符，先乘除再加减
                    && ' ' != s.charAt(i)) //空格就跳过
                    || i == s.length() - 1) { //到达字符串的最后，这时候是数字，但是需要计算最后一次的结果
                //加减在这个时候不计算结果，直接压入stack，到最后的时候再计算
                if (sign == '+') {
                    stack.push(num);
                }
                if (sign == '-') {//待会加一个负数即可
                    stack.push(-num);
                }

                //乘除从stack中弹出一个数字先计算结果后，再将这个结果压入stack，因为没有括号，所以把乘除运算符的前后两个数拿来运算即可
                if (sign == '*') {
                    stack.push(stack.pop() * num);
                }
                if (sign == '/') {
                    stack.push(stack.pop() / num);
                }
                sign = s.charAt(i);//这时候的字符是运算符，记录下准备下一轮遇到数字再判断
                num = 0; //遇到运算符将num归零，下次循环遇到数字再来换算
            }
        }

        int result = 0;
        //这时候stack里面都是正数（+）或负数（-），直接全部加起来即可
        for (int i : stack) {
            result += i;
        }
        return result;
    }
}
```

用变量来维持

```java
class Solution {
    public int calculate(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }
        int result = 0, previous = 0, num = 0;
        char sign = '+';
        char[] array = s.trim().toCharArray();
        for (int i = 0; i <= array.length; i++) {//<=表示最后还得运算一次
            if (i != array.length && Character.isDigit(array[i])) {
                num = num * 10 + array[i] - '0';
            } else {
                if (i != array.length && array[i] == ' ') {
                    continue;
                }
                if (sign == '+') {
                    result += num;
                    previous = num;
                }
                if (sign == '-') {
                    result -= num;
                    previous = -num;
                }
                if (sign == '*') {
                    result = result - previous + previous * num;//之前多加了一次num，因为之前不知道后面的运算符，所以先加了这里再减了再做乘法
                    previous = previous * num;//乘法运算符两边的数相乘为下一轮当前数的previous
                }
                if (sign == '/') {
                    result = result - previous + previous / num;
                    previous = previous / num;
                }
                num = 0;
                if (i != array.length) {
                    sign = array[i];
                }
            }
        }

        return result;
    }
}
```


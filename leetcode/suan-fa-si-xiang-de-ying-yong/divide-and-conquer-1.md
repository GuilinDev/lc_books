# Divide and Conquer

## 241 Different Ways to Add Parentheses

### 原题概述

Given a string of numbers and operators, return all possible results from computing all the different possible ways to group numbers and operators. The valid operators are `+`, `-` and `*`.

**Example 1:**

```text
Input: "2-1-1"
Output: [0, 2]
Explanation: 
((2-1)-1) = 0 
(2-(1-1)) = 2
```

**Example 2:**

```text
Input: "2*3-4*5"
Output: [-34, -14, -10, -10, 10]
Explanation: 
(2*(3-(4*5))) = -34 
((2*3)-(4*5)) = -14 
((2*(3-4))*5) = -10 
(2*((3-4)*5)) = -10 
(((2*3)-4)*5) = 10
```

### 题意和分析

给一个String，是计算式，求出各种组合得出的不同结果，只有加减乘，返回结果list。解法是用递归，将operator为界建立左右子树；也就是说当遇到是operator时，将这个operator的左右分别递归，一直递归到两边只有一个数，然后进行计算，将左右子树的所有结果存入结果list中。

### 代码

```java
class Solution {
    public List<Integer> diffWaysToCompute(String input) {
        List<Integer> result = new ArrayList<>();
        if (input == null || input.length() == 0) {
            return result;
        }

        //遍历每一个字符
        for (int i = 0; i < input.length(); i++) {
            if (isOperator(input.charAt(i))) {
                char operator = input.charAt(i);
                List<Integer> left = diffWaysToCompute(input.substring(0, i));
                List<Integer> right = diffWaysToCompute(input.substring(i + 1));//不包括i位置本身

                for (int num1 : left) {//遍历左边的数
                    for (int num2 : right) {//遍历右边的数
                        result.add(calculate(num1, num2, operator));//处理两个数
                    }
                }
            }
        }

        //没有operator，input只是一个数的情况
        if (result.size() == 0) {
            result.add(Integer.valueOf(input));
        }
        return result;
    }

    private boolean isOperator(char c) {
        if (c == '+' || c == '-' || c == '*') {
            return true;
        }
        return false;
    }

    private int calculate(int num1, int num2, char operator) {
        int result = 0;
        switch(operator) {
            case '+':
                result = num1 + num2;
                break;
            case '-':
                result = num1 - num2;
                break;
            case '*':
                result = num1 * num2;
                break;
        }
        return result;
    }
}
```


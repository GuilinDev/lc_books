# Stack

### 20 - Valid Parentheses

#### 原题概述

Given a string containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.

Note that an empty string is also considered valid.

**Example 1:**

```text
Input: "()"
Output: true
```

**Example 2:**

```text
Input: "()[]{}"
Output: true
```

**Example 3:**

```text
Input: "(]"
Output: false
```

**Example 4:**

```text
Input: "([)]"
Output: false
```

**Example 5:**

```text
Input: "{[]}"
Output: true
```

#### 题意和分析

验证输入的字符串是否是括号字符串，用栈，开始遍历输入字符串，如果当前字符为左半边括号就压入栈中；如果是右半边括号，先判断此时栈是否为空，是的话就直接返回false，如不为空，取出栈顶元素，如果是对应的左半边括号，继续循环，否则不成对就返回false。

#### 代码

```java
class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (ch == '(' || ch == '[' || ch == '{' ) {
                stack.push(ch);
            } else {
                if (stack.empty()) {
                    return false;
                }
                if (ch == ')' && stack.pop() != '(') return false;
                if (ch == ']' && stack.pop() != '[') return false;
                if (ch == '}' && stack.pop() != '{') return false;
            }
        }
        return stack.empty();//如果最后是empty，返回true；如果还有元素，返回false
    }
}
```


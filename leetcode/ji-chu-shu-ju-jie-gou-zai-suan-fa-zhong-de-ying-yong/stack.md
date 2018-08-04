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

### 155 - Min Stack

#### 原题概述

Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

* push\(x\) -- Push element x onto stack.
* pop\(\) -- Removes the element on top of the stack.
* top\(\) -- Get the top element.
* getMin\(\) -- Retrieve the minimum element in the stack.

**Example:**

```text
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> Returns -3.
minStack.pop();
minStack.top();      --> Returns 0.
minStack.getMin();   --> Returns -2.
```

#### 题意和分析

跟普通的栈相比，就多了一个取最小值的方法，使用两个栈，一个栈按顺序存储进来的数据，另外一个存出现过的最小值。

#### 代码

使用两个栈

```java
class MinStack {
    private Stack<Integer> stack1 = new Stack<>();
    private Stack<Integer> stack2 = new Stack<>();

    /** initialize your data structure here. */
    public MinStack() {

    }

    public void push(int x) {
        stack1.push(x);
        if (stack2.empty() || stack2.peek() >= x) {//第二个栈存入最小值
            stack2.push(x);
        }
    }

    public void pop() {
        //注意这里不能下面这样写，因为两个栈各自弹出的元素类型是object，两个不同的object比较会是false；得闲转为int
        // if (stack2.peek() == stack1.peek()) stack2.pop();
        // stack1.pop();

        //如果两个栈的顶部元素相等，那都要弹出，最小值不能留在第二个栈里面
        int x = stack1.pop();
        if (stack2.peek() == x) {
            stack2.pop();
        }
    }

    public int top() {
        return stack1.peek();
    }

    public int getMin() {
        return stack2.peek();
    }
}

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack obj = new MinStack();
 * obj.push(x);
 * obj.pop();
 * int param_3 = obj.top();
 * int param_4 = obj.getMin();
 */
```

只用一个栈，需要一个整型变量min\_val来记录当前最小值，初始化为整型最小值，然后如果需要进栈的数字小于等于当前最小值min\_val，那么将min\_val压入栈，并且将min\_val更新为当前数字。在出栈操作时，先将栈顶元素移出栈，再判断该元素是否和min\_val相等，相等的话我们将min\_val更新为新栈顶元素，再将新栈顶元素移出栈即可。

```java
class MinStack {
    private Stack<Integer> stack = new Stack<>();
    private int min_val = Integer.MAX_VALUE;

    /** initialize your data structure here. */
    public MinStack() {

    }

    public void push(int x) {
        if (x <= min_val) {
            stack.push(min_val);
            min_val = x;
        }
        stack.push(x);
    }

    public void pop() {
        if (stack.pop() == min_val) {
            min_val = stack.pop();
        }
    }

    public int top() {
        return stack.peek();
    }

    public int getMin() {
        return min_val;
    }
}

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack obj = new MinStack();
 * obj.push(x);
 * obj.pop();
 * int param_3 = obj.top();
 * int param_4 = obj.getMin();
 */
```

### 42 - Trapping Rain Water

#### 原题概述

Given _n_ non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

![](http://www.leetcode.com/static/images/problemset/rainwatertrap.png)  
The above elevation map is represented by array \[0,1,0,2,1,0,1,3,2,1,2,1\]. In this case, 6 units of rain water \(blue section\) are being trapped. **Thanks Marcos** for contributing this image!

**Example:**

```text
Input: [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
```

#### 题意和分析

#### 代码

### 173 - Binary Search Tree Iterator

#### 原题概述

#### 题意和分析

#### 代码

### 150 - Evaluate Reverse Polish Notation

#### 原题概述

#### 题意和分析

#### 代码


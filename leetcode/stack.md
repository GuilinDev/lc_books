# Stack

## 20 - Valid Parentheses

### 原题概述

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

### 题意和分析

验证输入的字符串是否是括号字符串，用栈，开始遍历输入字符串，如果当前字符为左半边括号就压入栈中；如果是右半边括号，先判断此时栈是否为空，是的话就直接返回false，如不为空，取出栈顶元素，如果是对应的左半边括号，继续循环，否则不成对就返回false。

### 代码

```java
class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (ch == '(' || ch == '[' || ch == '{' ) {
                stack.push(ch);
            } else {
                if (stack.empty()) { // 不然pop空的stack会是EmptyStackException
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

不用的办法，用一个arr来记录对应位置“应该出现的”括号

```java
class Solution {
    public boolean isValid(String s) {
        char[] arr = new char[s.length()];
        int counter = 0;


        for(int i = 0; i < s.length(); i++){
            if(s.charAt(i) == '(')  {
                arr[counter] = ')';
                counter++;
            } else if(s.charAt(i) == '{'){
                arr[counter] = '}';
                counter++;
            } else if(s.charAt(i) == '['){
                arr[counter] = ']';
                counter++;
            } else{                   
                if(counter == 0 || s.charAt(i) != arr[counter - 1]) {
                    return false;
                } else {
                    counter--;
                }
            }
        }

        return counter == 0; 
    }
}
```

## 155 - Min Stack

### 原题概述

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

### 题意和分析

跟普通的栈相比，就多了一个取最小值的方法，使用两个栈，一个栈按顺序存储进来的数据，另外一个存出现过的最小值。

### 代码

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

        //如果两个栈的顶部元素相等，那都要弹出，最小值不能只留在第二个栈里面
        int x = stack1.pop();
        if (stack2.peek() == x) {
            stack2.pop(); // 仔细想想，只要stack1中还有元素，stack2中的最小值就不会pop完
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
        // 把可能的倒数第二小的值存在第二位
        if (x <= min_val) {
            stack.push(min_val);
            min_val = x;
        }
        stack.push(x);
    }

    public void pop() {
        // 同样这里要pop两次，栈顶元素和可能的刚才第二小元素
        if (stack.pop() == min_val) { // 如果不等于，说明之前倒数第二个元素不是倒数第二小
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

## 42 - Trapping Rain Water

### 原题概述

Given _n_ non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

![](http://www.leetcode.com/static/images/problemset/rainwatertrap.png)  
The above elevation map is represented by array \[0,1,0,2,1,0,1,3,2,1,2,1\]. In this case, 6 units of rain water \(blue section\) are being trapped. **Thanks Marcos** for contributing this image!

**Example:**

```text
Input: [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
```

### 题意和分析

跟84 - Largest Rectangle in Histogram类似，收集雨水，可以用DP或者stack来做。

1\) 暴力解法，按照题意，直接计算每个柱子头顶上的雨水量，加起来返回。具体做法：对于数组中的每个元素的顶上，找出下雨后水能达到的最高位置 = 该元素左右两边最大高度的较小值减去当前高度的值。

![](../.gitbook/assets/image%20%2853%29.png)

2\) 在暴力方法中，仅仅为了找到最大值每次都要向左和向右扫描一次，所以可以在暴力法的基础上，先存上每个元素的左右最大值，然后左右两个最大值之中，取较小值（不溢出）进行计算，这就是DP的办法。

![](../.gitbook/assets/image%20%2852%29.png)

时间和空间复杂度均为O\(n\)。

![](../.gitbook/assets/image%20%2854%29.png)



3\) 可以不用像方法2的DP 那样存储最大高度，而是用栈来跟踪可能储水的最长的条形块。使用栈就可以在一次遍历内完成计算。

我们在遍历数组时维护一个栈。如果当前的条形块小于或等于栈顶的条形块，我们将条形块的索引入栈，意思是当前的条形块被栈中的前一个条形块界定。如果我们发现一个条形块长于栈顶，我们可以确定栈顶的条形块被当前条形块和栈的前一个条形块界定，因此我们可以弹出栈顶元素并且累加答案到 result。

时间和空间复杂度均为O\(n\)。

![](../.gitbook/assets/image%20%2851%29.png)

4\) 和方法 2的DP 相比，我们不从左和从右分开计算，我们想办法一次完成遍历。 从动态编程方法的示意图中我们注意到，只要 right\_max\[i\] &gt; left\_max\[i\] （元素 0 到元素 6），积水高度将由 left\_max 决定，类似地 left\_max\[i\] &gt; right\_max\[i\]（元素 8 到元素 11）。 所以可以认为如果一端有更高的条形块（例如右端），积水的高度依赖于当前方向的高度（从左到右）。当我们发现另一侧（右侧）的条形块高度不是最高的，我们则开始从相反的方向遍历（从右到左）。 我们必须在遍历时维护left\_max和right\_max ，但是我们现在可以使用两个指针交替进行，实现 1 次遍历即可完成。

时间复杂度：O\(n\)。单次遍历的时间O\(n\)。 空间复杂度：O\(1\) 的额外空间。left, right, left\_max 和 right\_max 只需要常数的空间。

![](../.gitbook/assets/image%20%2855%29.png)

### 代码

2. DP方法一，维护一个一位dp数组，遍历两遍数组，第一遍遍历dp\[i\]中存入i位置左边的最大值；然后第二遍遍历，找到dp\[i\]右边的最大值；然后两个最大值取较小值，跟当前值A\[i\]比较，如果大于A\[i\]，将差值存入结果。

```java
class Solution {
    public int trap(int[] height) {
        int result = 0;
        int left_max = 0;
        int right_max = 0;
        int n = height.length;
        
        int[] dp = new int[n]; //根据当前元素i的两边柱子计算的顶上可以储存的水
        
        //缓存i位置左边的最大值
        for (int i = 0; i < n; i++) {
            dp[i] = left_max;
            left_max = Math.max(left_max, height[i]);
        }
        
        //找到右边的最大值并和左边的最大值比较
        for (int i = n - 1; i >= 0; i--) {
            dp[i] = Math.min(dp[i], right_max);//dp[i]取左右两边的较小值，水才不会溢出
            right_max = Math.max(right_max, height[i]); // 缓存i元素右边的最大值
            if (dp[i] - height[i] > 0) { // 当前元素柱子顶上可以存储的水
                result += dp[i] - height[i];
            }
        }
        return result;
    }
}
```

DP方法二，上面的方法一看是两次遍历，直觉就可以优化，可以用两个指针left和right分别指向给定数组的首尾，然后两头往中间扫描，在当前两个指针已经扫描过的范围内，先比较两头找出较小值，如果较小值是left指向的值，从左往右扫，相反就从右往左扫；在扫描过程中如果遇到的值比当前的较小值小，将差值存入结果，如果遇到的值比较小值大，重新确定窗口范围，直到left和right指针相遇并且重合。

```java
class Solution {
    public int trap(int[] height) {
        int result = 0, left = 0, right = height.length - 1;
        while (left < right) {
            int min = Math.min(height[left], height[right]);
            if (height[left] == min) {
                left++;
                while (left < right && height[left] < min) {
                    result += min - height[left];
                    left++;
                }
            } else {
                right--;
                while (left < right && height[right] < min) {
                    result += min - height[right];
                    right--;
                }
            }
        }
        return result;
    }
}
```

上面解法可以更简洁

```java
class Solution {
    public int trap(int[] height) {
        int result = 0, left = 0, right = height.length - 1;
        int level = 0;
        while (left < right) {
            int min = height[(height[left] < height[right]) ? left++ : right--];
            level = Math.max(level, min);
            result += level - min;
        }
        return result;
    }
}
```

3. Stack的做法，遍历高度，如果此时栈为空，或者当前高度小于等于栈顶高度，则把当前高度的坐标压入栈，注意我们不直接把高度压入栈，而是把坐标压入栈，这样方便我们在后来算水平距离。当遇到比栈顶高度大的时候，就说明有可能会有坑存在，可以装雨水。此时栈里至少有一个高度，如果只有一个的话，那么不能形成坑，我们直接跳过，如果多余一个的话，那么此时把栈顶元素取出来当作坑，新的栈顶元素就是左边界，当前高度是右边界，只要取二者较小的，减去坑的高度，长度就是右边界坐标减去左边界坐标再减1，二者相乘就是盛水量。

```java
class Solution {
    public int trap(int[] height) {
        Stack<Integer> stack = new Stack<Integer>();
        int i = 0, n = height.length, result = 0;
        while (i < n) {
            if (stack.isEmpty() || height[i] <= height[stack.peek()]) {
                stack.push(i);
                i++;
            } else {
                int t = stack.pop();
                if (stack.isEmpty()) {
                    continue;
                }
                result += (Math.min(height[i], height[stack.peek()]) - height[t]) * (i - stack.peek() - 1);
            }
        }
        return result;
    }
}
```

## 407 Trapping Rain Water II

### 题目

Given an `m x n` matrix of positive integers representing the height of each unit cell in a 2D elevation map, compute the volume of water it is able to trap after raining.

**Example:**

```text
Given the following 3x6 height map:
[
  [1,4,3,1,3,2],
  [3,2,1,3,2,4],
  [2,3,3,2,3,1]
]

Return 4.
```

![](https://assets.leetcode.com/uploads/2018/10/13/rainwater_empty.png)

The above image represents the elevation map `[[1,4,3,1,3,2],[3,2,1,3,2,4],[2,3,3,2,3,1]]` before the rain.

![](https://assets.leetcode.com/uploads/2018/10/13/rainwater_fill.png)

After the rain, water is trapped between the blocks. The total volume of water trapped is 4.

**Constraints:**

* `1 <= m, n <= 110`
* `0 <= heightMap[i][j] <= 20000`

### 分析

第42题是1-D的接雨水问题，其中的DP解法是从左右两边的边界往中间进行收缩，收缩的过程中，对每个坐标（一维坐标）头顶上能接的雨水进行累加。

这道题是2-D的接雨水的问题，这个边界不再是线段的两个端点，而是矩形的一周，所以用优先队列维护所有边界点，收缩时，也不仅仅只有左右两个方向，而是上下左右四个方向，分别计算柱子头顶对比四周可以存储的雨水，同时维护一个visit的数组，记录哪些坐标已经被访问过，不然会造成重复求解。因此，这是个PQ+BFS的解法。

### 代码

```java
class Solution {
    /**
    维护一个Cell类来hold一根柱子的横纵坐标和高度
    */
    class Cell {
        private int row;
        private int col;
        private int height;
        
        public Cell(int row, int col, int height) {
            this.row = row;
            this.col = col;
            this.height = height;
        }
    }
    
    public int trapRainWater(int[][] heightMap) {
        int result = 0;
        if (heightMap == null || heightMap.length == 0 || heightMap[0].length == 0) {
            return result;
        }
        int m = heightMap.length;
        int n = heightMap[0].length;
        
        PriorityQueue<Cell> pq = new PriorityQueue<>(Comparator.comparingInt((Cell cell) -> cell.height)); // 优先队列的顺序从最高的柱子开始检查
        
        boolean[][] visited = new boolean[m][n];
        
        // 首先把二维数组的四个边加入到pq中，准备往中间计算
        for (int k = 0; k < m; k++) {
            visited[k][0] = true;
            pq.offer(new Cell(k, 0, heightMap[k][0]));
            visited[k][n - 1] = true;
            pq.offer(new Cell(k, n - 1, heightMap[k][n - 1]));
        }
        for (int k = 1; k < n - 1; k++) { // 左上和右下两个点已经处理
            visited[0][k] = true;
            pq.offer(new Cell(0, k, heightMap[0][k]));
            visited[m - 1][k] = true;
            pq.offer(new Cell(m - 1, k, heightMap[m - 1][k]));
        }
        
        int[][] dirs = {{1, 0},{-1, 0},{0, 1},{0, -1}};
        
        while (!pq.isEmpty()) {
            // 与BFS的标准写法不同的一点是，这个BFS不用计算扩散中每一层有多少个元素
            Cell oneCell = pq.poll();
            for (int[] dir : dirs) {//当前柱子的四个方向
                int newX = oneCell.row + dir[0];
                int newY = oneCell.col + dir[1];
                
                if (newX >= 0 && newX < m && newY >= 0 && newY < n && !visited[newX][newY]) {
                    result += Math.max(0, oneCell.height - heightMap[newX][newY]); // 当前柱子与周边四个柱子比较，水往低处流
                    pq.offer(new Cell(newX, newY, Math.max(oneCell.height, heightMap[newX][newY]))); //选择height的时候需要选较高的柱子，水流不过去
                    visited[newX][newY] = true;
                }
            }
        }
        return result;
    }
}
```

## 150 - Evaluate Reverse Polish Notation

### 原题概述

Evaluate the value of an arithmetic expression in [Reverse Polish Notation](http://en.wikipedia.org/wiki/Reverse_Polish_notation).

Valid operators are `+`, `-`, `*`, `/`. Each operand may be an integer or another expression.

**Note:**

* Division between two integers should truncate toward zero.
* The given RPN expression is always valid. That means the expression would always evaluate to a result and there won't be any divide by zero operation.

**Example 1:**

```text
Input: ["2", "1", "+", "3", "*"]
Output: 9
Explanation: ((2 + 1) * 3) = 9
```

**Example 2:**

```text
Input: ["4", "13", "5", "/", "+"]
Output: 6
Explanation: (4 + (13 / 5)) = 6
```

**Example 3:**

```text
Input: ["10", "6", "9", "3", "+", "-11", "*", "/", "*", "17", "+", "5", "+"]
Output: 22
Explanation: 
  ((10 * (6 / ((9 + 3) * -11))) + 17) + 5
= ((10 * (6 / (12 * -11))) + 17) + 5
= ((10 * (6 / -132)) + 17) + 5
= ((10 * 0) + 17) + 5
= (0 + 17) + 5
= 17 + 5
= 22
```

### 题意和分析

将操作数放前面，操作符后置的写法是逆波兰表达式。从前往后遍历数组，遇到数字则压入栈中，遇到符号，则把栈顶的两个数字拿出来运算，把结果再压入栈中，直到遍历完整个数组，栈顶数字即为最终答案。

### 代码

```java
class Solution {
    public int evalRPN(String[] tokens) {
        int a,b;
        Stack<Integer> stack = new Stack<>();
        for (String s : tokens) {
            if (s.equals("+")) {
                stack.add(stack.pop() + stack.pop());
            } else if (s.equals("/")) {
                b = stack.pop();
                a = stack.pop();
                stack.add(a / b);
            } else if (s.equals("*")) {
                stack.add(stack.pop() * stack.pop());
            } else if (s.equals("-")) {
                b = stack.pop();
                a = stack.pop();
                stack.add (a - b);
            } else {//遇到数字
                stack.add(Integer.parseInt(s));
            }
        }
        return stack.pop();
    }
}
```

也可以用递归

```java
class Solution {
    int index;
    public int evalRPN(String[] tokens) {
        if (tokens == null || tokens.length == 0) {
            return 0;
        }
        index = tokens.length - 1;
        return recursion(tokens);
    }
    private int recursion(String[] tokens) {
        String token = tokens[index];
        index--;
        
        if (token.equals("+") || token.equals("-") || token.equals("*") || token.equals("/")) {
            
            // 遇到符号，先递归找到离该符号左边最近的两个数
            int b = recursion(tokens);
            int a = recursion(tokens);
            
            // 计算后，递归栈记录返回的数字
            if (token.equals("+")) {
                return a + b;
            } else if (token.equals("-")) {
                return a - b;
            } else if (token.equals("*")) {
                return a * b;
            } else {
                return a / b;
            }
            
        } else { // 遇到数字，直接返回上一层递归，准备给符号用
            return Integer.parseInt(token); // 递归栈记录返回的数字
        }
    }
}
```

"茴"字的第三种写法，每次遇到操作符就运算之前的两个数字（一定有的）

```java
class Solution {
    public int evalRPN(String[] tokens) {
        int[] ls = new int[tokens.length/2+1];
        int index = 0;
        for (String token : tokens) {
            switch (token) {
                case "+":
                    ls[index - 2] = ls[index - 2] + ls[index - 1];
                    index--;
                    break;
                case "-":
                    ls[index - 2] = ls[index - 2] - ls[index - 1];
                    index--;
                    break;
                case "*":
                    ls[index - 2] = ls[index - 2] * ls[index - 1];
                    index--;
                    break;
                case "/":
                    ls[index - 2] = ls[index - 2] / ls[index - 1];
                    index--;
                    break;
                default:
                    ls[index++] = Integer.parseInt(token);
                    break;
            }
        }
        return ls[0];
    }
}
```

### 

## 224 - Basic Calculator

### 原题概述

Implement a basic calculator to evaluate a simple expression string.

The expression string may contain open `(` and closing parentheses `)`, the plus `+` or minus sign `-`, **non-negative** integers and empty spaces .

**Example 1:**

```text
Input: "1 + 1"
Output: 2
```

**Example 2:**

```text
Input: " 2-1 + 2 "
Output: 3
```

**Example 3:**

```text
Input: "(1+(4+5+2)-3)+(6+8)"
Output: 23
```

**Note:**

* You may assume that the given expression is always valid.
* **Do not** use the `eval` built-in library function.

### 题意和分析

实现一个简单的计算器，表达式中只有加减号，数字，括号和空格，没有乘除，所以没有计算的优先级之分，因此这道题就变的没有那么复杂。用一个栈来辅助计算，用个变量sign来表示当前的符号，遍历给定的字符串s，如果遇到了数字，由于可能是个多位数，所以我们要用while循环把之后的数字都读进来，然后用sign\*num来更新结果res；如果遇到了加号，则sign赋为1，如果遇到了符号，则赋为-1；如果遇到了左括号，则把当前结果res和符号sign压入栈，res重置为0，sign重置为1；如果遇到了右括号，结果res乘以栈顶的符号，栈顶元素出栈，结果res加上栈顶的数字，栈顶元素出栈。

### 代码

```java
class Solution {
    public int calculate(String s) {
        int res = 0, sign = 1, n = s.length();
        Stack<Integer> stack = new Stack<>();
        for (int i = 0; i < n; i++) {
            char c = s.charAt(i);
            if (c >= '0') {
                int num = 0;
                while (i < n && s.charAt(i) >= '0') {
                    num = 10 * num + s.charAt(i++) - '0';
                }
                res += sign * num;
                i--;
            } else if (c == '+') {
                sign = 1;
            } else if (c == '-') {
                sign = -1;
            } else if (c == '(') {
                stack.push(res);
                stack.push(sign);
                res = 0;
                sign = 1;
            } else if (c == ')') {
                res *= stack.pop();
                res += stack.pop();
            }
        }
        return res;
    }
}
```

类似的做法，使用一个变量来保存读取的num，遇到其他字符，就用sign\*num来更新结果res

```java
class Solution {
    public int calculate(String s) {
        int res = 0, num = 0, sign = 1, n = s.length();
        Stack<Integer> stack = new Stack<>();
        for (int i = 0; i < n; i++) {
            char c = s.charAt(i);
            if (c >= '0') {
                num = 10 * num + (c - '0');
            } else if (c == '+' || c == '-') {
                res += sign * num;
                num = 0;
                sign = (c == '+') ? 1 : -1;
            } else if (c == '(') {
                stack.push(res);
                stack.push(sign);
                res = 0;
                sign = 1;
            } else if (c == ')') {
                res += sign * num;
                num = 0;
                res *= stack.pop();
                res += stack.pop();
            }
        }
        res += sign * num;
        return res;
    }
}
```

递归处理括号的方法在这道题也同样适用，用一个变量count，遇到左括号自增1，遇到右括号自减1，当count为0的时候，说明括号正好完全匹配，这个trick在验证括号是否valid的时候经常使用到。然后我们就是根据左右括号的位置提取出中间的子字符串调用递归函数，返回值赋给num，这种思路类似Basic Calculator III。

```java
class Solution {
    public int calculate(String s) {
        if (s.length() == 0) return 0;
        s = "(" + s + ")";
        int[] p = {0};
        return eval(s, p);
    }
    //计算括号里面的值
    private int eval(String s, int[] p){
        int val = 0;
        int i = p[0];
        int sign = 1; //1:+ -1:-
        int num = 0;
        while(i < s.length()){
            char c = s.charAt(i);
            switch(c){
                case '+': val = val + sign * num; // end of number and set operator
                    num = 0;
                    sign = 1;
                    i++;
                    break;
                case '-': val = val + sign * num; // end of number and set operator
                    num = 0;
                    sign = -1;
                    i++;
                    break;
                case '(': p[0] = i + 1; // start a new eval
                    val = val + sign * eval(s, p);
                    i = p[0];
                    break;
                case ')': p[0] = i + 1; // end current eval and return. Note that we need to deal with the last num
                    return val + sign * num;
                case ' ': i++;
                    continue;
                default : num = num * 10 + c - '0'; i++;
            }
        }
        return val;
    }
}
```

## 341 - Flatten Nested List Iterator

### 原题概述

Given a nested list of integers, implement an iterator to flatten it.

Each element is either an integer, or a list -- whose elements may also be integers or other lists.

**Example 1:**  
Given the list `[[1,1],2,[1,1]]`,

By calling next repeatedly until hasNext returns false, the order of elements returned by next should be: `[1,1,2,1,1]`.

**Example 2:**  
Given the list `[1,[4,[6]]]`,

By calling next repeatedly until hasNext returns false, the order of elements returned by next should be: `[1,4,6]`.

### 题意和分析

建立压平嵌套链表的迭代器，而迭代器一般都是用迭代的方法来解，如果要用递归一般都需用栈来辅助遍历，由于栈的后进先出的特性，在对向量遍历的时候，从后往前把对象压入栈中，那么第一个对象最后压入栈就会第一个取出来处理，我们的hasNext\(\)函数需要遍历栈，并进行处理，如果栈顶元素是整数，直接返回true，如果不是，那么移除栈顶元素，并开始遍历这个取出的list，还是从后往前压入栈，循环停止条件是栈为空，返回false。

### 代码

```java
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * public interface NestedInteger {
 *
 *     // @return true if this NestedInteger holds a single integer, rather than a nested list.
 *     public boolean isInteger();
 *
 *     // @return the single integer that this NestedInteger holds, if it holds a single integer
 *     // Return null if this NestedInteger holds a nested list
 *     public Integer getInteger();
 *
 *     // @return the nested list that this NestedInteger holds, if it holds a nested list
 *     // Return null if this NestedInteger holds a single integer
 *     public List<NestedInteger> getList();
 * }
 */
public class NestedIterator implements Iterator<Integer> {
    
    Stack<NestedInteger> stack = new Stack<>();

    public NestedIterator(List<NestedInteger> nestedList) {
        //构造器先把所有NestedIterator的对象从后向前加入到栈中
        for (int i = nestedList.size() - 1; i >= 0; i--) {
            stack.push(nestedList.get(i));
        }
    }

    @Override
    public Integer next() {
        // 返回stack顶部元素的整数
        return stack.pop().getInteger();
    }

    @Override
    public boolean hasNext() {
        while (!stack.isEmpty()) {
            NestedInteger ni = stack.peek();
            if (ni.isInteger()) { // 如果时数字，返回
                return true;
            }
            stack.pop(); // 否则是数组，嵌套从尾到头压入栈
            for (int i = ni.getList().size() - 1; i >= 0; i--) {
                stack.push(ni.getList().get(i));
            }
        }
        return false;
    }
}

/**
 * Your NestedIterator object will be instantiated and called as such:
 * NestedIterator i = new NestedIterator(nestedList);
 * while (i.hasNext()) v[f()] = i.next();
 */
```

## 232 - Implement Queue using Stacks

### 原题概述

Implement the following operations of a queue using stacks.

* push\(x\) -- Push element x to the back of queue.
* pop\(\) -- Removes the element from in front of queue.
* peek\(\) -- Get the front element.
* empty\(\) -- Return whether the queue is empty.

**Example:**

```text
MyQueue queue = new MyQueue();

queue.push(1);
queue.push(2);  
queue.peek();  // returns 1
queue.pop();   // returns 1
queue.empty(); // returns false
```

**Notes:**

* You must use only standard operations of a stack -- which means only `push to top`, `peek/pop from top`, `size`, and `is empty`operations are valid.
* Depending on your language, stack may not be supported natively. You may simulate a stack by using a list or deque \(double-ended queue\), as long as you use only standard operations of a stack.
* You may assume that all operations are valid \(for example, no pop or peek operations will be called on an empty queue\).

### 题意和分析

用栈来实现队列，刚好和225-Implement Stack using Queues相反。这个现实主要是用在读写分离的情况下，[函数式编程里面也常实现](https://stackoverflow.com/questions/2050120/why-use-two-stacks-to-make-a-queue/2050402#2050402)。

栈和队列的核心不同点就是栈是先进后出，而队列是先进先出，所以要用栈的先进后出的特性来模拟出队列的先进先出。那么怎么做呢，只要我们在插入元素的时候每次都从前面插入即可，比如如果一个队列是1,2,3,4，那么在栈中保存为4,3,2,1，那么返回栈顶元素1，也就是队列的首元素，则问题迎刃而解。所以此题的难度是push函数，我们需要一个辅助栈s2，把s的元素也逆着顺序存入temp中，此时加入新元素x，再把temp中的元素存回来，这样就是我们要的顺序了，其他三个操作也就直接调用栈的操作即可。

### 代码

把逻辑都写在push里面

```java
class MyQueue {

    Stack<Integer> queue = new Stack<>();

    /** Initialize your data structure here. */
    public MyQueue() {

    }

    /** Push element x to the back of queue. */
    public void push(int x) {
        Stack<Integer> temp = new Stack<Integer>();
        while(!queue.empty()){
            temp.push(queue.pop());
        }
        queue.push(x);
        while(!temp.empty()){
            queue.push(temp.pop());
        }
    }

    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        return queue.pop();
    }

    /** Get the front element. */
    public int peek() {
        return queue.peek();
    }

    /** Returns whether the queue is empty. */
    public boolean empty() {
        return queue.empty();
    }
}

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = new MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * boolean param_4 = obj.empty();
 */
```

上面那个解法简单容易理解，但是效率不高，因为每次在push的时候，都要翻转两边栈，下面这个方法使用了两个栈s1和s2，其中新进栈的都先缓存在s1中，当要pop和peek的时候，才将s1中所有元素移到s2中操作，这样可以得到优化。



```java
class MyQueue {

    Stack<Integer> s1 = new Stack<>();
    Stack<Integer> s2 = new Stack<>();

    /** Initialize your data structure here. */
    public MyQueue() {

    }

    /** Push element x to the back of queue. */
    public void push(int x) {
        s1.push(x);
    }

    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        peek();
        return s2.pop();
    }

    /** Get the front element. */
    public int peek() {
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) {
                s2.push(s1.pop());
            }
        }
        return s2.peek();
    }

    /** Returns whether the queue is empty. */
    public boolean empty() {
        return s1.isEmpty() && s2.isEmpty();
    }
}

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = new MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * boolean param_4 = obj.empty();
 */
```

直接调用原生stack的方法，遇到push就把元素从s2往s1倒，遇到pop和peek就把元素从s1往s2倒

```java
class MyQueue {

    Stack<Integer> s1;
    Stack<Integer> s2;
    /** Initialize your data structure here. */
    public MyQueue() {
        s1 = new Stack<>();
        s2 = new Stack<>();
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        while (!s2.isEmpty()) {
            s1.push(s2.pop());
        }
        s1.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        while (!s1.isEmpty()) {
            s2.push(s1.pop());
        }
        return s2.pop();
    }
    
    /** Get the front element. */
    public int peek() {
        while(!s1.isEmpty()) {
            s2.push(s1.pop());
        }
        return s2.peek();
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return s1.isEmpty() && s2.isEmpty();
    }
}

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = new MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * boolean param_4 = obj.empty();
 */
```

## 946 Validate Stack Sequence

### 题目

验证栈序列，给定 pushed 和 popped 两个序列，每个序列中的 值都不重复，只有当它们可能是在最初空栈上进行的推入 push 和弹出 pop 操作序列的结果时，返回 true；否则，返回 false 。

Given two sequences `pushed` and `popped` **with distinct values**, return `true` if and only if this could have been the result of a sequence of push and pop operations on an initially empty stack.

**Example 1:**

```text
Input: pushed = [1,2,3,4,5], popped = [4,5,3,2,1]
Output: true
Explanation: We might do the following sequence:
push(1), push(2), push(3), push(4), pop() -> 4,
push(5), pop() -> 5, pop() -> 3, pop() -> 2, pop() -> 1
```

**Example 2:**

```text
Input: pushed = [1,2,3,4,5], popped = [4,3,5,1,2]
Output: false
Explanation: 1 cannot be popped before 2.
```

**Constraints:**

* `0 <= pushed.length == popped.length <= 1000`
* `0 <= pushed[i], popped[i] < 1000`
* `pushed` is a permutation of `popped`.
* `pushed` and `popped` have distinct values.

### 分析

做法是开一个栈直接模拟过程，每次都 push 进一个新元素，然后和 pop 序列不断比对，如果发现一致，则出队。 最后如果 pop 序列都遍历完，则证明合法。这也是贪心的思想。

### 代码

```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Stack<Integer> stack = new Stack<>();
        int len = pushed.length;
        int index1 = 0, index2 = 0;
        
        while (index1 < len && index2 < len) {
            stack.push(pushed[index1]);
            while (!stack.isEmpty() && stack.peek() == popped[index2]) {
                index2++;
                stack.pop();
            }
            index1++;
        }
        return index2 == len; //检查popped数组里面的元素是否pop完
    }
}
```

## 402 Remove k Digits

### 题目

Given a non-negative integer num represented as a string, remove k digits from the number so that the new number is the smallest possible.

**Note:**  


* The length of num is less than 10002 and will be ≥ k.
* The given num does not contain any leading zero.

**Example 1:**

```text
Input: num = "1432219", k = 3
Output: "1219"
Explanation: Remove the three digits 4, 3, and 2 to form the new number 1219 which is the smallest.
```

**Example 2:**

```text
Input: num = "10200", k = 1
Output: "200"
Explanation: Remove the leading 1 and the number is 200. Note that the output must not contain leading zeroes.
```

**Example 3:**

```text
Input: num = "10", k = 2
Output: "0"
Explanation: Remove all the digits from the number and it is left with nothing which is 0.
```

### 分析

 给定一个以字符串表示的非负整数 _num_，移除这个数中的 _k_ 位数字，使得剩下的数字最小。

完全枚举的复杂度是组合对数级的，肯定不行。分析下，对于两个相同长度的数字序列，最左边不同的数字决定了这两个数字的大小，例如，对于 A = 1axxx，B = 1bxxx，如果 a &gt; b 则 A &gt; B。知道了这个以后可以想到，在删除数字时应该从左向右迭代来判断。

确定了迭代的顺序以后，就必须制定如何消除数字的标准，以便获得最小值。

从一个简单的例子开始。给定一个数字序列，例如 425，如果要求只删除一个数字，那么从左到右，有 4、2 和 5 三个选择。将每一个数字和它的左邻居进行比较。从 2 开始，小于它的左邻居 4。则应该去掉数字 4。如果不这么做，则随后无论做什么，都不会得到最小数。

![](../.gitbook/assets/image%20%28110%29.png)

如果我们保留数字 4，那么所有可能的组合都是以数字 4（即 42，45）开头的。相反，如果去掉 4，留下 2，我们得到的是以 2 开头的组合（即 25），这明显小于任何留下数字 4 的组合。

总结上述删除一个数字的规则如下： 给定一个数字序列 \[D1 ​D2 ​D3 ​…Dn\]，如果数字 D2 小于其左邻居D1，则应该删除左邻居（D1），以获得最小结果。

这也是贪心的思想，一旦删除一个数字，剩下的数字就形成了一个新的问题，可以继续使用这个规则。在某些情况下，规则对任意数字都不适用，即单调递增序列。在这种情况下，只需要删除末尾的数字来获得最小数。可以使用栈或队列。

算法：

* 对于每个数字，如果该数字小于栈/队列顶部，即该数字的左邻居，则弹出，即删除左邻居。否则，把数字push到栈/队列上。
*  重复上述步骤，直到任何条件不再适用，例如堆栈为空（不再保留数字）。或者已经删除了 `k` 位数字。
* 
### 代码

利用栈或队列，或者直接把字符串转换成char  arr然后左右双指针操作。

```java
class Solution {
    public String removeKdigits(String num, int k) {
        Deque<Character> deque = new LinkedList<>();
        char[] arr = num.toCharArray();
        
        //从左到右入队和遍历，移除比当前字符大的左边字符
        for (char ch : arr) {
            while (!deque.isEmpty() && k > 0 && deque.peekLast() > ch) { //跟其左邻居比较，左邻居大就出队删除
                deque.removeLast();
                k--; // 删掉一个
            }
            //当前字符入队
            deque.addLast(ch);
        }
        
        // 把所有较大的左邻居删除了后，还不够k个，这时候右边就是最大的，从右边开始删除
        while (k > 0 && !deque.isEmpty()){
            deque.removeLast();
            k--;
        }
        
        // 处理头部0的情况，移除左边
        while (!deque.isEmpty() && deque.getFirst() == '0') {
            deque.removeFirst();
        }
        
        if (deque.isEmpty()) {
            return "0";
        }
        
        // 构建剩余的字符并返回
        char[] res = new char[deque.size()];
        for (int i = 0; i < res.length; i++) {
            res[i] = deque.removeFirst();
        }
        
        return new String(res);
    }
}
```




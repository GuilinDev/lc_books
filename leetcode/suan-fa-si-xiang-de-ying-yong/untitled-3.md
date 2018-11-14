---
description: 一般包括二叉树的BFS，Graph的BFS
---

# BFS

## 301 Remove Invalid Parentheses

### 原题概述

Remove the minimum number of invalid parentheses in order to make the input string valid. Return all possible results.

**Note:** The input string may contain letters other than the parentheses `(` and `)`.

**Example 1:**

```text
Input: "()())()"
Output: ["()()()", "(())()"]
```

**Example 2:**

```text
Input: "(a)())()"
Output: ["(a)()()", "(a())()"]
```

**Example 3:**

```text
Input: ")("
Output: [""]
```

### 题意和分析

移除最少的括号使得给定字符串为一个合法的含有括号的字符串，也就是字符串中的左右括号数应该相同，而且每个右括号的左边一定有其对应的左括号，需要找出所有合法的取法。用BFS或者DFS来解：

1）BFS，参考[这里](https://leetcode.com/problems/remove-invalid-parentheses/discuss/75032/Share-my-Java-BFS-solution)，对于字符串s，通过移除一个括号产生所有的状态，检查移除后是否还valid，如果检查到了valid，那就把该状态加入到结果集中，完成；否则把状态加入到queue中并移除两个再检查，以此类推。这样BFS的好处是被移除的括号总是最少的，并且没有递归调用。时间复杂度O\(n\)，空间复杂度O\(n^2\)；

2）DFS，参考[这里](https://leetcode.com/problems/remove-invalid-parentheses/discuss/75027/Easy-Short-Concise-and-Fast-Java-DFS-3-ms-solution)，几个重点，一次就产生独一的符合要求的状态，而不是像BFS那样用visited的Set来判断，并且无需预处理；检查括号字符串是否valid可以用stack或者count（遇到左括号+1，遇到右括号-1，在遇到右括号的同时检查count是否==0，以此判断该右括号之前是否有左括号）。移除任意一个左括号"\("，同时为了防止重复（比如两个左括号在一起的情况下移除第一个和第二个是没有区别的），在接下来的情况下移除第一个右括号"\)"，如果这时候prefix是有效的，递归调用直到查找所有的结果。

### 代码

BFS

```java
class Solution {
    public List<String> removeInvalidParentheses(String s) {
        List<String> result = new ArrayList<>();
        if (s == null) {
            return result;
        }

        Set<String> visited = new HashSet<>();
        Queue<String> queue = new LinkedList<>();//存储状态

        //初始化
        visited.add(s);
        queue.add(s);

        boolean found = false;//在某个level是否发现valid的状态

        while (!queue.isEmpty()) {
            s = queue.poll();

            if (isValid(s)) {
                //找到一个答案，加入到结果集
                result.add(s);
                found = true;
            }

            if (found) {//当前状态已加入结果集，跳出当前循环，检查下一个状态
                continue;
            }

            //产生所有的状态
            for (int i = 0; i < s.length(); i++) {
                //只移除左括号或右括号，字母什么的不移除
                if (s.charAt(i) != '(' && s.charAt(i) != ')') {
                    continue;
                }
                //产生一个临时状态
                String temp = s.substring(0, i) + s.substring(i + 1);//为什么这里i + 1不会越界？
                if (!visited.contains(temp)) {
                    queue.add(temp);
                    visited.add(temp);
                }
            }
        }

        return result;
    }

    //检查当前状态是否valid，也可以用stack来做，不过需要额外空间
    private boolean isValid(String s) {
        int count = 0;
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '(') {
                count++;
            }
            //遇到右括号，检查之前是否有左括号，并递减
            if (c == ')' && count-- == 0) {
                return false;
            }
        }
        return count == 0;
    }
}
```

DFS

```java
class Solution {
    public List<String> removeInvalidParentheses(String s) {
        List<String> result = new ArrayList<>();
        remove(s, result, 0, 0, new char[]{'(', ')'});
        return result;
    }

    public void remove(String s, List<String> result, int last_i, int last_j,  char[] par) {
        for (int stack = 0, i = last_i; i < s.length(); ++i) {
            if (s.charAt(i) == par[0]) stack++;
            if (s.charAt(i) == par[1]) stack--;
            if (stack >= 0) continue;
            for (int j = last_j; j <= i; ++j)
                if (s.charAt(j) == par[1] && (j == last_j || s.charAt(j - 1) != par[1])) {
                    remove(s.substring(0, j) + s.substring(j + 1, s.length()), result, i, j, par);
                }
            return;
        }
        String reversed = new StringBuilder(s).reverse().toString();
        if (par[0] == '('){// finished left to right
            remove(reversed, result, 0, 0, new char[]{')', '('});
        } else {// finished right to left
            result.add(reversed);
        }

    }
}
```


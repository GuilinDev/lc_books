# 寻找Tree中的nodes

## 653 - 2Sum - Input is a BST

### 原题概述

Given a Binary Search Tree and a target number, return true if there exist two elements in the BST such that their sum is equal to the given target.

**Example 1:**  


```text
Input: 
    5
   / \
  3   6
 / \   \
2   4   7

Target = 9

Output: True
```

**Example 2:**  


```text
Input: 
    5
   / \
  3   6
 / \   \
2   4   7

Target = 28

Output: False
```

### 题意

在BST中寻找是否有两个nodes的val，它们相加的和等于target，返回true，否则返回false。

### 代码


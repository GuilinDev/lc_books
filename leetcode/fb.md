# FB

## 953 Verifying an Alien Dictionary

直接顺序对比

```java
class Solution {
    public boolean isAlienSorted(String[] words, String order) {
        if (words == null || words.length == 0) {
            return true;
        }
        String prev = words[0];
        for (int i = 1; i < words.length; i++) {
            // compare prev and curr
            if(!check(prev, words[i], order)) {
                return false;
            }
            prev = words[i];
        }
        return true;
    }
    private boolean check(String prev, String curr, String order) {
        int idx1 = 0, idx2 = 0;
        while (idx1 < prev.length() && idx2 < curr.length()) {
            int p1 = order.indexOf(prev.charAt(idx1));
            int p2 = order.indexOf(curr.charAt(idx2));
            if (p1 < p2) {
                return true;
            } else if (p1 > p2) {
                return false;
            } else { // same char
                idx1++;
                idx2++;
            }
            
        }
        if (idx1 < prev.length() && idx2 >= curr.length()) { // if prev is longer than curr
            return false;
        }
        return true;
    }
}
```

## 1249 Minimum Remove to Make Valid Parentheses

额外Stack和Set记录哪些字符串可以删掉

```java
class Solution {
    public String minRemoveToMakeValid(String s) {
        if (s.isEmpty()) {
            return "";
        }
        Set<Integer> indicesToRemove = new HashSet<>();
        Stack<Integer> stack = new Stack<>();
        
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (ch == '(') {
                stack.push(i);
            }
            if (ch == ')') {
                if (stack.isEmpty()) {
                    indicesToRemove.add(i);
                } else {
                    stack.pop();
                }
            }
        }
        
        while (!stack.isEmpty()) { // some '(' left inside the stack
            indicesToRemove.add(stack.pop());
        }
        
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < s.length(); i++) {
            if (!indicesToRemove.contains(i)) {
                sb.append(s.charAt(i));
            }
        }
        return sb.toString();
    }
}
```

## 1428 Leftmost Column with at Least a One

对所有行进行遍历，对每一行最左边出现的1的位置进行记录（找1的过程利用二分搜索），最后返回所有行中最左边1的列数。

```java
/**
 * // This is the BinaryMatrix's API interface.
 * // You should not implement it, or speculate about its implementation
 * interface BinaryMatrix {
 *     public int get(int row, int col) {}
 *     public List<Integer> dimensions {}
 * };
 */

class Solution {
    public int leftMostColumnWithOne(BinaryMatrix binaryMatrix) {
        int rows = binaryMatrix.dimensions().get(0);
        int cols = binaryMatrix.dimensions().get(1);
        System.out.println(rows);
        
        int smallestIndex = Integer.MAX_VALUE;
        
        for (int i = 0; i < rows; i++) {
            int left = 0, right = cols - 1;
            while (left + 1 < right) {
                int mid = left + (right - left) / 2; // mid element of one row
                if (binaryMatrix.get(i, mid) == 0) { // one the right
                    left = mid;
                } else { // itself or on the left
                    right = mid;
                }
            }
            if (binaryMatrix.get(i, left) == 1) {
                smallestIndex = Math.min(smallestIndex, left);
            }  else if (binaryMatrix.get(i, right) == 1) {
                smallestIndex = Math.min(smallestIndex, right);
            } 
        }
        return smallestIndex == Integer.MAX_VALUE ? -1 : smallestIndex;
    }
}
```

## 238 Product of Array Except Self

## 973 K Closest Points to Origin

## 560 Subarray Sum Equals K

## 680 Valid Palindrome II

## 415 Add Strings

## 273 Integer to English




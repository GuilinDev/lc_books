# VM

## 146 LRU Cache 

[https://app.gitbook.com/@guilindev/s/interview/~/drafts/-MkF3RlZmVCDwo1nRFTf/leetcode/gao-ji-de-shu-ju-jie-gou-he-suan-fa\#146-lru-cache](https://app.gitbook.com/@guilindev/s/interview/~/drafts/-MkF3RlZmVCDwo1nRFTf/leetcode/gao-ji-de-shu-ju-jie-gou-he-suan-fa#146-lru-cache)

## 255 Verify Preorder Sequence in Binary Search Tree 

给一个数组，验证里面的数字是否符合BST的前序遍历顺序

正常O\(n\)解法

```java
class Solution {
    public boolean verifyPreorder(int[] preorder) {
        int low = Integer.MIN_VALUE;
        Stack<Integer> path = new Stack();
        for (int p : preorder) {
            if (p < low) {
                return false;
            }
            while (!path.empty() && p > path.peek()) {
                low = path.pop();
            }
            path.push(p);
        }
        return true;
    }
}
```

利用给定的参数的array，来存中间结果，O\(1\)解法

```java
class Solution {
    public boolean verifyPreorder(int[] preorder) {
        int low = Integer.MIN_VALUE, i = -1;
        for (int p : preorder) {
            if (p < low)
                return false;
            while (i >= 0 && p > preorder[i])
                low = preorder[i--];
            preorder[++i] = p;
        }
        return true;
    }
}
```

## 460 LFU Cache

[https://app.gitbook.com/@guilindev/s/interview/leetcode/gao-ji-de-shu-ju-jie-gou-he-suan-fa\#460-lfu](https://app.gitbook.com/@guilindev/s/interview/leetcode/gao-ji-de-shu-ju-jie-gou-he-suan-fa#460-lfu)

## 49 Group Anagrams

[https://app.gitbook.com/@guilindev/s/interview/leetcode/string\#49-group-anagrams](https://app.gitbook.com/@guilindev/s/interview/leetcode/string#49-group-anagrams)

## 56 Merge Intervals

[https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou\#56-merge-intervals](https://app.gitbook.com/@guilindev/s/interview/leetcode/hui-wen-jie-gou#56-merge-intervals)

## 1328 Break a Palindrome

给一个回文串，replace其中一个character，使其不为回文串且 **lexicographically** 最小，贪心算法O\(n\) O\(n\)，从左到右，替换任何非a的字母变成a，如果只有一个字母，变为空串，否则把最后一个字母替换为b\(全a的情况\)。

```java
class Solution {
    public String breakPalindrome(String palindrome) {
        char[] s = palindrome.toCharArray();
        int n = s.length;
        
        for(int i = 0; i < n/2; i++) { //回文串只需遍历一半
            if(s[i] != 'a') {
                s[i] = 'a';
                return String.valueOf(s);
            }
        }
        s[n-1] = 'b'; 
        return n < 2 ? "" : String.valueOf(s);
    }
}
```

## 697 Degree of an Array



## 20 Valid Parentheses



## 1228 Missing Number In Arithmetic Progression 



## 564 Find the Closest Palindrome



## 93 Restore IP Addresses



##  33 Search in Rotated Sorted Array 



## 138 Copy List with Random Pointer 



## 295 Find Median from Data Stream



## 1048 Longest String Chain 



## 1451 Rearrange Words in a Sentence 



## 85 Maximal Rectangle 



## 609 Find Duplicate File in System



## 221 Maximal Square 



## 4 Median of Two Sorted Arrays



## 1 Two Sum



## 1192 Critical Connections in a Network



## 706 Design HashMap 



## 97 Interleaving String 

## 76 Minimum Window Substring



## 621 Task Scheduler 



## 277 Find the Celebrity 



## 412 Fizz Buzz



## 2 Add Two Numbers 



## 863 All Nodes Distance K in Binary Tree 



## 300 Longest Increasing Subsequence 



## 23 Merge k Sorted Lists

## 121 Best Time to Buy and Sell Stock

## 322 Coin Change

## 98 Validate Binary Search Tree

## 1010 Pairs of Songs With Total Durations Divisible by 60 



## 15 3Sum 1046 Last Stone Weight 238 Product of Array Except Self 

## 151 Reverse Words in a String 297 Serialize and Deserialize Binary Tree 200 Number of Islands 8 String to Integer \(atoi\) 

## 3 Longest Substring Without Repeating Characters 

## 720 Longest Word in Dictionary 

## 17 Letter Combinations of a Phone Number 

## 5 Longest Palindromic Substring 1669 Merge In Between Linked Lists 

## 1347 Minimum Number of Steps to Make Two Strings Anagram 

## 981 Time Based Key-Value Store 

## 179 Largest Number 

## 206 Reverse Linked List 99 Recover Binary Search Tree 

## 314 Binary Tree Vertical Order Traversal 

## 150 Evaluate Reverse Polish Notation 

## 907 Sum of Subarray Minimums 41 First Missing Positive 

## 127 Word Ladder 21 Merge Two Sorted Lists 

## 155 Min Stack 1071 Greatest Common Divisor of Strings 

## 42 Trapping Rain Water 

## 426 Convert Binary Search Tree to Sorted Doubly Linked List 

## 905 Sort Array By Parity 

## 733 Flood Fill 236 Lowest Common Ancestor of a Binary Tree 38 Count and Say 

## 232 Implement Queue using Stacks 168 Excel Sheet Column Title 62 Unique Paths 103 Binary Tree Zigzag Level Order Traversal 1136 Parallel Courses 226 Invert Binary Tree 355 Design Twitter 

## 347 Top K Frequent Elements 

## 54 Spiral Matrix 88 Merge Sorted Array 269 Alien Dictionary 

## 139 Word Break 

## 253 Meeting Rooms II 

## 421 Maximum XOR of Two Numbers in an Array 

## 856 Score of Parentheses 39 Combination Sum 394 Decode String 

## 977 Squares of a Sorted Array 162 Find Peak Element 

## 124 Binary Tree Maximum Path Sum 

## 380 Insert Delete GetRandom O\(1\) 

## 133 Clone Graph 242 Valid Anagram 

## 50 Pow\(x, n\) 63 Unique Paths II 

## 108 Convert Sorted Array to Binary Search Tree 328 Odd Even Linked List 

## 987 Vertical Order Traversal of a Binary Tree 

## 283 Move Zeroes 

## 443 String Compression 

## 332 Reconstruct Itinerary 

## 532 K-diff Pairs in an Array 

## 14 Longest Common Prefix 11 Container With Most Water 

## 349 Intersection of Two Arrays 230 Kth Smallest Element in a BST 

## 811 Subdomain Visit Count 

## 199 Binary Tree Right Side View 

## 438 Find All Anagrams in a String 

## 32 Longest Valid Parentheses 102 Binary Tree Level Order Traversal 

## 1209 Remove All Adjacent Duplicates in String II 

## 79 Word Search 

## 80 Remove Duplicates from Sorted Array II 

## 105 Construct Binary Tree from Preorder and Inorder Traversal 

## 252 Meeting Rooms 287 Find the Duplicate Number 

## 53 Maximum Subarray 

## 278 First Bad Version 

## 19 Remove Nth Node From End of List 

## 210 Course Schedule II 

## 71 Simplify Path 

## 36 Valid Sudoku 

## 416 Partition Equal Subset Sum 

## 28 Implement strStr\(\) 994 Rotting Oranges 1143 Longest Common Subsequence 70 Climbing Stairs 204 Count Primes 

## 509 Fibonacci Number 

## 73 Set Matrix Zeroes 

## 35 Search Insert Position


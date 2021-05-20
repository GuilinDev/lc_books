# SalesForce

## 488 Zuma Game 38.2% Hard

[https://leetcode-cn.com/problems/zuma-game/solution/zu-ma-you-xi-by-617076674/](https://leetcode-cn.com/problems/zuma-game/solution/zu-ma-you-xi-by-617076674/)

## 146 LRU Cache 36.7% Medium

## 920 Number of Music Playlists 48.3% Hard

[https://leetcode-cn.com/problems/number-of-music-playlists/solution/bo-fang-lie-biao-de-shu-liang-by-leetcode/](https://leetcode-cn.com/problems/number-of-music-playlists/solution/bo-fang-lie-biao-de-shu-liang-by-leetcode/)

## 532 K-diff Pairs in an Array 35.8% Medium

[https://leetcode-cn.com/problems/k-diff-pairs-in-an-array/solution/yi-tiao-hen-guan-jian-de-pan-duan-yu-ju-by-shu-zi-/](https://leetcode-cn.com/problems/k-diff-pairs-in-an-array/solution/yi-tiao-hen-guan-jian-de-pan-duan-yu-ju-by-shu-zi-/)

## 1283 Find the Smallest Divisor Given a Threshold 50.2% Medium

[https://leetcode-cn.com/problems/find-the-smallest-divisor-given-a-threshold/solution/er-fen-cha-zhao-ding-wei-chu-shu-by-liweiwei1419/](https://leetcode-cn.com/problems/find-the-smallest-divisor-given-a-threshold/solution/er-fen-cha-zhao-ding-wei-chu-shu-by-liweiwei1419/)

## 17 Letter Combinations of a Phone Number 50.2% Medium

## 56 Merge Intervals 41.7% Medium

## 460 LFU Cache 36.8% Hard

## 1659 Maximize Grid Happiness 35.8% Hard

[https://leetcode-cn.com/problems/maximize-grid-happiness/solution/dong-tai-gui-hua-shi-jian-qia-de-you-dian-er-jin-y/](https://leetcode-cn.com/problems/maximize-grid-happiness/solution/dong-tai-gui-hua-shi-jian-qia-de-you-dian-er-jin-y/)

## 565 Array Nesting 56.0% Medium

[https://leetcode-cn.com/problems/array-nesting/solution/shu-zu-qian-tao-by-leetcode/](https://leetcode-cn.com/problems/array-nesting/solution/shu-zu-qian-tao-by-leetcode/)

## 151 Reverse Words in a String 24.5% Medium

## 1718 Construct the Lexicographically Largest Valid Sequence 47.7% Medium

[https://leetcode-cn.com/problems/construct-the-lexicographically-largest-valid-sequence/solution/javatan-xin-hui-su-zhong-gui-zhong-ju-by-o3dn/](https://leetcode-cn.com/problems/construct-the-lexicographically-largest-valid-sequence/solution/javatan-xin-hui-su-zhong-gui-zhong-ju-by-o3dn/)

## 706 Design HashMap 64.0% Easy

## 186 Reverse Words in a String II 46.4% Medium

每个单词得字符是分开的

Given a character array `s`, reverse the order of the **words**.

A **word** is defined as a sequence of non-space characters. The **words** in `s` will be separated by a single space.

Your code must solve the problem **in-place,** i.e. without allocating extra space.

**Example 1:**

```text
Input: s = ["t","h","e"," ","s","k","y"," ","i","s"," ","b","l","u","e"]
Output: ["b","l","u","e"," ","i","s"," ","s","k","y"," ","t","h","e"]
```

**Example 2:**

```text
Input: s = ["a"]
Output: ["a"]
```

**Constraints:**

* `1 <= s.length <= 105`
* `s[i]` is an English letter \(uppercase or lowercase\), digit, or space `' '`.
* There is **at least one** word in `s`.
* `s` does not contain leading or trailing spaces.
* All the words in `s` are guaranteed to be separated by a single space.

```java
class Solution {
    public void reverseWords(char[] s) {
        // Three step to reverse
        // 1, reverse the whole sentence
        reverse(s, 0, s.length - 1);
        // 2, reverse each word
        int start = 0;
        int end = -1;
        for (int i = 0; i < s.length; i++) {
            if (s[i] == ' ') {
                reverse(s, start, i - 1);
                start = i + 1;
            }
        }
        // 3, reverse the last word, if there is only one word this will solve the corner case
        reverse(s, start, s.length - 1);
    }

    public void reverse(char[] s, int start, int end) {
        while (start < end) {
            char temp = s[start];
            s[start] = s[end];
            s[end] = temp;
            start++;
            end--;
        }
    }
}
```

## 678 Valid Parenthesis String 31.8% Medium

## 232 Implement Queue using Stacks 53.1% Easy

## 1163 Last Substring in Lexicographical Order 36.2% Hard

[https://leetcode-cn.com/problems/last-substring-in-lexicographical-order/solution/hua-dong-chuang-kou-si-lu-jian-dai-ma-zh-7nrn/](https://leetcode-cn.com/problems/last-substring-in-lexicographical-order/solution/hua-dong-chuang-kou-si-lu-jian-dai-ma-zh-7nrn/)

## 218 The Skyline Problem 36.9% Hard

## 895 Maximum Frequency Stack 63.4% Hard

[https://leetcode-cn.com/problems/maximum-frequency-stack/solution/zui-da-pin-lu-zhan-by-leetcode/](https://leetcode-cn.com/problems/maximum-frequency-stack/solution/zui-da-pin-lu-zhan-by-leetcode/)

## 179 Largest Number 31.0% Medium

## 588 Design In-Memory File System 46.7% Hard



Design a data structure that simulates an in-memory file system.

Implement the FileSystem class:

* `FileSystem()` Initializes the object of the system.
* `List<String> ls(String path)`

  * If `path` is a file path, returns a list that only contains this file's name.
  * If `path` is a directory path, returns the list of file and directory names **in this directory**.

  The answer should in **lexicographic order**.

* `void mkdir(String path)` Makes a new directory according to the given `path`. The given directory path does not exist. If the middle directories in the path do not exist, you should create them as well.
* `void addContentToFile(String filePath, String content)`
  * If `filePath` does not exist, creates that file containing given `content`.
  * If `filePath` already exists, appends the given `content` to original content.
* `String readContentFromFile(String filePath)` Returns the content in the file at `filePath`.

**Example 1:**![](https://assets.leetcode.com/uploads/2021/04/28/filesystem.png)

```text
Input
["FileSystem", "ls", "mkdir", "addContentToFile", "ls", "readContentFromFile"]
[[], ["/"], ["/a/b/c"], ["/a/b/c/d", "hello"], ["/"], ["/a/b/c/d"]]
Output
[null, [], null, null, ["a"], "hello"]

Explanation
FileSystem fileSystem = new FileSystem();
fileSystem.ls("/");                         // return []
fileSystem.mkdir("/a/b/c");
fileSystem.addContentToFile("/a/b/c/d", "hello");
fileSystem.ls("/");                         // return ["a"]
fileSystem.readContentFromFile("/a/b/c/d"); // return "hello"
```

**Constraints:**

* `1 <= path.length, filePath.length <= 100`
* `path` and `filePath` are absolute paths which begin with `'/'` and do not end with `'/'` except that the path is just `"/"`.
* You can assume that all directory names and file names only contain lowercase letters, and the same names will not exist in the same directory.
* You can assume that all operations will be passed valid parameters, and users will not attempt to retrieve file content or list a directory or file that does not exist.
* `1 <= content.length <= 50`
* At most `300` calls will be made to `ls`, `mkdir`, `addContentToFile`, and `readContentFromFile`.

```java
class FileSystem {

    class File {
        boolean isFile = false;
        Map<String, File> children = new HashMap<>();
        String content = "";
    }
    
    File root = null;
    
    public FileSystem() {
        root = new File();
    }
    
    public List<String> ls(String path) {
        String[] dirs = path.split("/");
        File node = root;
        List<String> result = new ArrayList<>();
        String name = "";
        for (String dir : dirs) {
            if (dir.length() == 0) continue;
            if (!node.children.containsKey(dir)) {
                return result;
            }
            node = node.children.get(dir);
            name = dir;
        }
        
        if (node.isFile) {
            result.add(name);
        }
        else {
            for (String key : node.children.keySet()) {
                result.add(key);
            }
        }
        
        Collections.sort(result);
        
        return result;
    }
    
    public void mkdir(String path) {
        String[] dirs = path.split("/");
        File node = root;
        for (String dir : dirs) {
            if (dir.length() == 0) continue;
            if (!node.children.containsKey(dir)) {
                File file = new File();
                node.children.put(dir, file);
            }
            node = node.children.get(dir);
        }
    }
    
    public void addContentToFile(String filePath, String content) {
        String[] dirs = filePath.split("/");
        File node = root;
        for (String dir : dirs) {
            if (dir.length() == 0) continue;
            if (!node.children.containsKey(dir)) {
                File file = new File();
                node.children.put(dir, file);
            }
            node = node.children.get(dir);
        }
        node.isFile = true;
        node.content += content;
    }
    
    public String readContentFromFile(String filePath) {
        String[] dirs = filePath.split("/");
        File node = root;
        for (String dir : dirs) {
            if (dir.length() == 0) continue;
            if (!node.children.containsKey(dir)) {
                File file = new File();
                node.children.put(dir, file);
            }
            node = node.children.get(dir);
        }

        return node.content;
    }

}

/**
 * Your FileSystem object will be instantiated and called as such:
 * FileSystem obj = new FileSystem();
 * List<String> param_1 = obj.ls(path);
 * obj.mkdir(path);
 * obj.addContentToFile(filePath,content);
 * String param_4 = obj.readContentFromFile(filePath);
 */
```

## 776 Split BST 56.8% Medium

## 127 Word Ladder 32.4% Hard

## 224 Basic Calculator 38.4% Hard

## 1 Two Sum 46.9% Easy

## 314 Binary Tree Vertical Order Traversal 47.5% Medium

## 403 Frog Jump 41.8% Hard

[https://leetcode-cn.com/problems/frog-jump/solution/qing-wa-guo-he-by-leetcode-solution-mbuo/](https://leetcode-cn.com/problems/frog-jump/solution/qing-wa-guo-he-by-leetcode-solution-mbuo/)

## 42 Trapping Rain Water 52.0% Hard

## 348 Design Tic-Tac-Toe 55.9% Medium

## 295 Find Median from Data Stream 47.8% Hard

## 412 Fizz Buzz 64.0% Easy

## 23 Merge k Sorted Lists 43.4% Hard

## 495 Teemo Attacking 56.0% Medium

[https://leetcode-cn.com/problems/teemo-attacking/solution/ti-mo-gong-ji-by-leetcode/](https://leetcode-cn.com/problems/teemo-attacking/solution/ti-mo-gong-ji-by-leetcode/)

## 1110 Delete Nodes And Return Forest 67.9% Medium

## 582 Kill Process 64.2% Medium

You have `n` processes forming a rooted tree structure. You are given two integer arrays `pid` and `ppid`, where `pid[i]` is the ID of the `ith` process and `ppid[i]` is the ID of the `ith` process's parent process.

Each process has only **one parent process** but may have multiple children processes. Only one process has `ppid[i] = 0`, which means this process has **no parent process** \(the root of the tree\).

When a process is **killed**, all of its children processes will also be killed.

Given an integer `kill` representing the ID of a process you want to kill, return _a list of the IDs of the processes that will be killed. You may return the answer in **any order**._

**Example 1:**![](https://assets.leetcode.com/uploads/2021/02/24/ptree.jpg)

```text
Input: pid = [1,3,10,5], ppid = [3,0,5,3], kill = 5
Output: [5,10]
Explanation: The processes colored in red are the processes that should be killed.
```

**Example 2:**

```text
Input: pid = [1], ppid = [0], kill = 1
Output: [1]
```

**Constraints:**

* `n == pid.length`
* `n == ppid.length`
* `1 <= n <= 5 * 104`
* `1 <= pid[i] <= 5 * 104`
* `0 <= ppid[i] <= 5 * 104`
* Only one process has no parent.
* All the values of `pid` are **unique**.
* `kill` is **guaranteed** to be in `pid`.

```java
public class Solution {
    public List<Integer> killProcess(List<Integer> pid, List<Integer> ppid, int kill) {
        if (kill == 0) return pid;
        
        int n = pid.size();
        Map<Integer, Set<Integer>> tree = new HashMap<>();
        for (int i = 0; i < n; i++) {
            tree.put(pid.get(i), new HashSet<Integer>());
        }
        for (int i = 0; i < n; i++) {
            if (tree.containsKey(ppid.get(i))) {
                Set<Integer> children = tree.get(ppid.get(i));
                children.add(pid.get(i));
                tree.put(ppid.get(i), children);
            }
        }
        
        List<Integer> result = new ArrayList<>();
        traverse(tree, result, kill);
        
        return result;
    }
    
    private void traverse(Map<Integer, Set<Integer>> tree, List<Integer> result, int pid) {
        result.add(pid);
        
        Set<Integer> children = tree.get(pid);
        for (Integer child : children) {
            traverse(tree, result, child);
        }
    }
}
```

## 200 Number of Islands 50.0% Medium

## 20 Valid Parentheses 40.1% Easy

## 49 Group Anagrams 60.1% Medium

## 724 Find Pivot Index 46.6% Easy

## 41 First Missing Positive 34.2% Hard

##  76 Minimum Window Substring 36.4% Hard

## 153 Find Minimum in Rotated Sorted Array 46.6% Medium

## 139 Word Break 42.2% Medium

## 22 Generate Parentheses 66.1% Medium

## 692 Top K Frequent Words 53.4% Medium

## 37 Sudoku Solver 47.6% Hard

## 215 Kth Largest Element in an Array 59.3% Medium

## 240 Search a 2D Matrix II 45.6% Medium

## 380 Insert Delete GetRandom O\(1\) 49.3% Medium

## 394 Decode String 53.3% Medium

## 33 Search in Rotated Sorted Array 36.3% Medium

## 212 Word Search II 37.7% Hard

## 48 Rotate Image 61.4% Medium

## 278 First Bad Version 38.1% Easy

## 557 Reverse Words in a String III 72.7% Easy

[https://app.gitbook.com/@guilindev/s/interview/~/drafts/-M\_xJktt8kLs83P7wPuK/leetcode/string\#557-reverse-words-in-a-string-iii](https://app.gitbook.com/@guilindev/s/interview/~/drafts/-M_xJktt8kLs83P7wPuK/leetcode/string#557-reverse-words-in-a-string-iii)

## 206 Reverse Linked List 66.1% Easy

## 1465 Maximum Area of a Piece of Cake After Horizontal and Vertical Cuts 34.4% Medium

[https://leetcode-cn.com/problems/maximum-area-of-a-piece-of-cake-after-horizontal-and-vertical-cuts/solution/javafen-bie-zhao-chu-chang-he-kuan-de-zui-da-zhi-b/](https://leetcode-cn.com/problems/maximum-area-of-a-piece-of-cake-after-horizontal-and-vertical-cuts/solution/javafen-bie-zhao-chu-chang-he-kuan-de-zui-da-zhi-b/)

## 31 Next Permutation 34.0% Medium

## 545 Boundary of Binary Tree 40.7% Medium

## 138 Copy List with Random Pointer 42.0% Medium

## 135 Candy 33.6% Hard

## 211 Design Add and Search Words Data Structure 40.9% Medium

## 8 String to Integer \(atoi\) 15.8% Medium

## 98 Validate Binary Search Tree 29.1% Medium

## 238 Product of Array Except Self 61.4% Medium

## 62 Unique Paths 56.6% Medium

## 53 Maximum Subarray 48.0% Easy

## 3 Longest Substring Without Repeating Characters 31.7% Medium

## 121 Best Time to Buy and Sell Stock 52.0% Easy

## 2 Add Two Numbers 36.0% Medium

## 445 Add Two Numbers II 56.8% Medium

## 173 Binary Search Tree Iterator 61.1% Medium

## 739 Daily Temperatures 65.0% Medium

## 122 Best Time to Buy and Sell Stock II 59.1% Easy

## 547 Number of Provinces 61.0% Medium

## 66 Plus One 42.2% Easy

## 973 K Closest Points to Origin 64.8% Medium

## 696 Count Binary Substrings 61.2% Easy

[https://leetcode-cn.com/problems/count-binary-substrings/solution/ji-shu-er-jin-zhi-zi-chuan-by-leetcode-solution/](https://leetcode-cn.com/problems/count-binary-substrings/solution/ji-shu-er-jin-zhi-zi-chuan-by-leetcode-solution/)

## 11 Container With Most Water 53.0% Medium

## 45 Jump Game II 32.7% Medium

## 1150 Check If a Number Is Majority Element in a Sorted Array 57.1% Easy

Given an array `nums` sorted in **non-decreasing** order, and a number `target`, return `True` if and only if `target` is a majority element.

A _majority element_ is an element that appears **more than `N/2`** times in an array of length `N`.

**Example 1:**

```text
Input: nums = [2,4,5,5,5,5,5,6,6], target = 5
Output: true
Explanation: 
The value 5 appears 5 times and the length of the array is 9.
Thus, 5 is a majority element because 5 > 9/2 is true.
```

**Example 2:**

```text
Input: nums = [10,100,101,101], target = 101
Output: false
Explanation: 
The value 101 appears 2 times and the length of the array is 4.
Thus, 101 is not a majority element because 2 > 4/2 is false.
```

**Constraints:**

* `1 <= nums.length <= 1000`
* `1 <= nums[i] <= 10^9`
* `1 <= target <= 10^9`

```java
class Solution {
    public boolean isMajorityElement(int[] nums, int target) {
        int firstIndex = firstOccur(nums, target);
        int plusNBy2Index = firstIndex + nums.length/2;
        
        if (plusNBy2Index<nums.length 
            && nums[plusNBy2Index] == target) {
            return true;
        }
        
        return false;
    }
    
    private int firstOccur(int[] nums, int target) {
        int low = 0;
        int high = nums.length;
        while (low < high) {
            int mid = low + (high-low)/2;
            if (nums[mid] < target) low = mid + 1;
            else if (nums[mid] >= target) high = mid;
        }
        return low;
    }
    
}
```

## 1051 Height Checker 72.6% Easy

[https://leetcode-cn.com/problems/height-checker/solution/onjie-fa-yong-shi-yu-nei-cun-ji-bai-100-javayong-h/](https://leetcode-cn.com/problems/height-checker/solution/onjie-fa-yong-shi-yu-nei-cun-ji-bai-100-javayong-h/)

## 1701 Average Waiting Time

[https://leetcode-cn.com/problems/average-waiting-time/solution/ping-jun-deng-dai-shi-jian-by-yi-shen-du-inj4/](https://leetcode-cn.com/problems/average-waiting-time/solution/ping-jun-deng-dai-shi-jian-by-yi-shen-du-inj4/)

## Find Common Nodes in TWO BST

```java
import java.util.ArrayList;
import java.util.Stack;

public class CommonNodesTwoBST {
    /**
     * BST Node class
     */
    static class Node {
        private Node(){}
        int val;
        Node left;
        Node right;
    }

    /**
     * Create a new Node
     * @param val - value for a new Node
     * @return a new Node
     */
    public static Node newNode(int val) {
        Node n = new Node();
        n.val = val;
        n.left = null;
        n.right = null;

        return n;
    }

    /**
     * insert a node into a BST
     */
    private static Node insert(Node root, int val) {
        if (root == null) {
            return newNode(val);
        }
        if (val < root.val) {
            root.left = insert(root.left, val);
        } else if (val > root.val){
            root.right = insert(root.right, val);
        }
        return root;
    }

    /**
     * inorder traversal
     */
    private static void inorder(Node root, Stack<Node> stack) {
        if (root == null) {
            return;
        }
        inorder(root.left, stack);
//        System.out.println(root.val);
        stack.push(root);
        inorder(root.right, stack);
    }

    /**
     * find common nodes for 2 BST
     */
    public static ArrayList<Integer> findCommonNodesTwoBST(Node node1, Node node2) {
        ArrayList<Integer> result = new ArrayList<>();
        if (node1 == null || node2 == null) {
            return result;
        }

        Stack<Node> stack1 = new Stack<>();
        Stack<Node> stack2 = new Stack<>();
        Node p1 = node1;
        Node p2 = node2;

        inorder(p1, stack1);
        inorder(p2, stack2);

        // bigger elements are on the top of stack
        while (!stack1.isEmpty() && !stack2.isEmpty()) {
            if (stack1.peek().val == stack2.peek().val) {
                result.add(0, stack1.peek().val);
                stack1.pop();
                stack2.pop();
            } else if (stack1.peek().val > stack2.peek().val) {
                stack1.pop();
            } else {
                stack2.pop();
            }
        }
        return result;
    }

    /**
     * Test
     */
    public static void main(String[] args) {
        Node n1 = newNode(100);
        Node n2 = newNode(200);

        insert(n1, 50);
        insert(n1, 200);
        insert(n2, 100);
        insert(n2, 300);

        ArrayList<Integer> commonNodes = new ArrayList<>();
        commonNodes = findCommonNodesTwoBST(n1, n2);

        System.out.println("Result: " + commonNodes.size());
        commonNodes.stream().forEach(System.out::println); // 100, 200
    }
}
```


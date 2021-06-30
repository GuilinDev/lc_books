# Tree1

## 814 Binary Tree Pruning

Given the `root` of a binary tree, return _the same tree where every subtree \(of the given tree\) not containing a_ `1` _has been removed_.

A subtree of a node `node` is `node` plus every node that is a descendant of `node`.

**Example 1:**![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/04/06/1028_2.png)

```text
Input: root = [1,null,0,0,1]
Output: [1,null,0,null,1]
Explanation: 
Only the red nodes satisfy the property "every subtree not containing a 1".
The diagram on the right represents the answer.
```

**Example 2:**![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/04/06/1028_1.png)

```text
Input: root = [1,0,1,0,0,0,1]
Output: [1,null,1,null,1]
```

**Example 3:**![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/04/05/1028.png)

```text
Input: root = [1,1,0,1,1,0,1,0]
Output: [1,1,0,1,1,null,1]
```

**Constraints:**

* The number of nodes in the tree is in the range `[1, 200]`.
* `Node.val` is either `0` or `1`.

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode pruneTree(TreeNode root) {
        if (root == null) {
            return root;
        }
        
        root.left = pruneTree(root.left);  //null
        root.right = pruneTree(root.right);
        
        if (root.val == 1 || root.left != null || root.right != null) {
            return root;
        }
        
        return null;
    }
}
```

## 508 Most Frequent Subtree Sum

Given the `root` of a binary tree, return the most frequent **subtree sum**. If there is a tie, return all the values with the highest frequency in any order.

The **subtree sum** of a node is defined as the sum of all the node values formed by the subtree rooted at that node \(including the node itself\).

**Example 1:**![](https://assets.leetcode.com/uploads/2021/04/24/freq1-tree.jpg)

```text
Input: root = [5,2,-3]
Output: [2,-3,4]
```

**Example 2:**![](https://assets.leetcode.com/uploads/2021/04/24/freq2-tree.jpg)

```text
Input: root = [5,2,-5]
Output: [2]
```

**Constraints:**

* The number of nodes in the tree is in the range `[1, 104]`.
* `-105 <= Node.val <= 105`

后序遍历

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    int maxCount = 0;
    public int[] findFrequentTreeSum(TreeNode root) {
        HashMap<Integer, Integer> map = new HashMap<>();
        findSum(root, map);
        List<Integer> resultList = new ArrayList<>();
        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            if (entry.getValue() == maxCount) {
                resultList.add(entry.getKey());
            }
        }
        // int[] result = new int[resultList.size()];
        
        // for (int i = 0; i < resultList.size(); i++) {
        //     result[i] = resultList.get(i);
        // }
        // return result;
        return resultList.stream()
                    .filter(Objects::nonNull)
                    .mapToInt(Integer::intValue)
                    .toArray();
    }

    private int findSum(TreeNode root, HashMap<Integer, Integer> map) {
        if (root == null) {
            return 0;
        }
        int sum = findSum(root.left, map) + findSum(root.right, map) + root.val;
        int count = map.getOrDefault(sum, 0) + 1;
        maxCount = Math.max(maxCount, count);
        map.put(sum, count);
        
        return sum;
    }
}
```


# Segment Tree

## 315 Count of Smaller Numbers After Self

### 题目

You are given an integer array nums and you have to return a new counts array. The counts array has the property where `counts[i]` is the number of smaller elements to the right of `nums[i]`.

**Example 1:**

```text
Input: nums = [5,2,6,1]
Output: [2,1,1,0]
Explanation:
To the right of 5 there are 2 smaller elements (2 and 1).
To the right of 2 there is only 1 smaller element (1).
To the right of 6 there is 1 smaller element (1).
To the right of 1 there is 0 smaller element.
```

**Constraints:**

* `0 <= nums.length <= 10^5`
* `-10^4 <= nums[i] <= 10^4`

### 分析

1\) 暴力法，每次遍历到一个数，就套一层循环去检查它后面比它小的数，O\(n^2\)，这个不行。

2\) 从右到左扫描，应用二分法将当前数据插入到一个额外数组中前面已排好序的位置，二分法查找的时间复杂度为O\(logn\)，但由于需要插入数据，所以每个数据查找和插入的总时间是O\(nlogn\)，总体的时间复杂度O\(n^2\*logn\)，但勉强能够通过测试。

3\) 应用合并排序。比较两个子数组，后数组的元素小于前数组，产生逆序。所以如果我们对数组进行排序，那么对于某个特定的数据，其后面的逆序数等于在排序过程中需要移动到该数前面的个数。时间复杂度O\(nlogn\)。我们定义数组pos\[i\] = j，表示第排名i个数据的元素下标是j。

4\) 使用BST进行统计。时间复杂度O\(nlogn\)。可以先对数组排序，以便使BST均衡

5\) 使用线段树。时间复杂度O\(nlogn\)

6\) 使用树状数组，要点是按名次进行计数。时间复杂度O\(nlogn\)

### 代码

二分搜索

```java
class Solution {
    public List<Integer> countSmaller(int[] nums) {
        int len = nums.length;
        int[] smaller = new int[len]; // 额外数组记录较小数的个数的结果
        for (int i = len - 2; i >= 0; i--) { // 统计nums[i]右侧的数字
            int left = i + 1;
            int right = len - 1;
            while (left <= right) {
                int mid = (left + right) / 2;
                if (nums[i] > nums[mid]) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            }
            smaller[i] = len - left; // 找到当前元素
            int temp = nums[i];
            if (right - i >= 0) {
                System.arraycopy(nums, i + 1, nums, i, right - i);
            }
            nums[right] = temp;
        }
        List<Integer> results = new ArrayList<>(len);
        for (int j : smaller) {
            results.add(j);
        }
        return results;
    }
}
```

Merge Sort

```java
class Solution {
    private void sort(int[] nums, int[] smaller, int[] pos, int from, int to) {
        if (from >= to) return;
        int m = (from + to) / 2;
        sort(nums, smaller, pos, from, m);
        sort(nums, smaller, pos, m + 1, to);
        int[] merged = new int[to - from + 1];
        int i = from, j = m + 1, k = 0, jump = 0;
        while (i <= m || j <= to) {
            if (i > m) {
                jump++;
                merged[k++] = pos[j++];
            } else if (j > to) {
                smaller[pos[i]] += jump;
                merged[k++] = pos[i++];
            } else if (nums[pos[i]] <= nums[pos[j]]) {
                smaller[pos[i]] += jump;
                merged[k++] = pos[i++];
            } else {
                jump++;
                merged[k++] = pos[j++];
            }
        }
        if (merged.length >= 0) System.arraycopy(merged, 0, pos, from, merged.length);
    }

    public List<Integer> countSmaller(int[] nums) {
        int[] smaller = new int[nums.length];
        int[] pos = new int[nums.length];
        for (int i = 0; i < pos.length; i++) pos[i] = i;
        sort(nums, smaller, pos, 0, nums.length - 1);
        List<Integer> result = new ArrayList<>(nums.length);
        for (int i = 0; i < nums.length; i++) result.add(smaller[i]);
        return result;
    }
}
```

BST用class实现

```java
class Solution {
    TreeNode root;

    private int smaller(TreeNode current, int val) {
        current.size++;
        if (current.val < val) {
            if (current.right == null) current.right = new TreeNode(val);
            return current.size - 1 - current.right.size + smaller(current.right, val);
        } else if (current.val > val) {
            if (current.left == null) current.left = new TreeNode(val);
            return smaller(current.left, val);
        } else {
            return current.left == null ? 0 : current.left.size;
        }
    }

    public List<Integer> countSmaller(int[] nums) {
        List<Integer> result = new ArrayList<>(nums.length);
        int[] smaller = new int[nums.length];
        if (nums == null || nums.length == 0) return result;
        root = new TreeNode(nums[nums.length - 1]);
        for (int i = nums.length - 1; i >= 0; i--) {
            smaller[i] = smaller(root, nums[i]);
        }
        for (int i = 0; i < smaller.length; i++) result.add(smaller[i]);
        return result;
    }
}

class TreeNode {
    int val;
    int size;
    TreeNode left, right;

    TreeNode(int val) {
        this.val = val;
    }
}
```

BST用Array实现

```java
class Solution {
    private void update(int[] tree, int[] smaller, int value) {
        int i = 0, j = tree.length - 1;
        while (i <= j) {
            int m = (i + j) / 2;
            if (value < tree[m]) {
                smaller[m]++;
                j = m - 1;
            } else {
                i = m + 1;
            }
        }
    }

    private int smaller(int[] tree, int[] smaller, int value) {
        int sum = 0;
        int i = 0, j = tree.length - 1;
        while (i <= j) {
            int m = (i + j) / 2;
            if (tree[m] <= value) {
                sum += smaller[m];
                i = m + 1;
            } else {
                j = m - 1;
            }
        }
        return sum;
    }

    public List<Integer> countSmaller(int[] nums) {
        int[] tree = Arrays.copyOf(nums, nums.length);
        Arrays.sort(tree);
        int[] smaller = new int[nums.length];
        int[] count = new int[nums.length];
        for (int i = nums.length - 1; i >= 0; i--) {
            count[i] = smaller(tree, smaller, nums[i]);
            update(tree, smaller, nums[i]);
        }
        List<Integer> results = new ArrayList<>(nums.length);
        for (int i = 0; i < count.length; i++) results.add(count[i]);
        return results;
    }
}
```

线段树

```java
class Solution {
    private int smaller(Node node, int val) {
        node.count++;
        if (node.min == node.max) return 0;
        int m = (node.min + node.max) / 2;
        if (m < val) {
            if (node.right == null) node.right = new Node(m + 1, node.max);
            return node.count - 1 - node.right.count + smaller(node.right, val);
        } else if (m > val) {
            if (node.min == m) return 0;
            if (node.left == null) node.left = new Node(node.min, m - 1);
            return smaller(node.left, val);
        } else {
            if (node.left == null) return 0;
            return node.left.count;
        }
    }

    public List<Integer> countSmaller(int[] nums) {
        int min = Integer.MAX_VALUE;
        int max = Integer.MIN_VALUE;
        for (int num : nums) {
            min = Math.min(min, num);
            max = Math.max(max, num);
        }
        Node root = new Node(min, max);
        int[] smaller = new int[nums.length];
        for (int i = nums.length - 1; i >= 0; i--) {
            smaller[i] = smaller(root, nums[i]);
        }
        List<Integer> result = new ArrayList<>(nums.length);
        for (int i = 0; i < smaller.length; i++) result.add(smaller[i]);
        return result;
    }
}

class Node {
    int min, max;
    int count;
    Node left, right;

    Node(int min, int max) {
        this.min = min;
        this.max = max;
    }
}
```

树状数组

```java
class Solution {
    private void sort(int[] nums, int[] pos, int from, int to) {
        if (from >= to) return;
        int m = (from + to) / 2;
        sort(nums, pos, from, m);
        sort(nums, pos, m + 1, to);
        int[] merged = new int[to - from + 1];
        int i = from, j = m + 1, p = 0;
        while (i <= m || j <= to) {
            if (i > m) {
                merged[p++] = pos[j++];
            } else if (j > to) {
                merged[p++] = pos[i++];
            } else if (nums[pos[i]] <= nums[pos[j]]) {
                merged[p++] = pos[i++];
            } else {
                merged[p++] = pos[j++];
            }
        }
        if (merged.length >= 0) System.arraycopy(merged, 0, pos, from, merged.length);
    }

    private int count(int[] sum, int s) {
        int count = 0;
        while (s > 0) {
            count += sum[s];
            s -= (s & -s);
        }
        return count;
    }

    private void update(int[] sum, int s) {
        while (s < sum.length) {
            sum[s]++;
            s += (s & -s);
        }
    }

    public List<Integer> countSmaller(int[] nums) {
        int[] pos = new int[nums.length];
        for (int i = 0; i < nums.length; i++) pos[i] = i;
        sort(nums, pos, 0, nums.length - 1);
        int[] seq = new int[nums.length];
        for (int i = 0, s = 0; i < pos.length; i++) {
            if (i == 0 || nums[pos[i]] != nums[pos[i - 1]]) seq[pos[i]] = ++s;
            else seq[pos[i]] = s;
        }
        int[] sum = new int[nums.length + 1];
        int[] smaller = new int[nums.length];
        for (int i = nums.length - 1; i >= 0; i--) {
            smaller[i] = count(sum, seq[i] - 1);
            update(sum, seq[i]);
        }
        List<Integer> result = new ArrayList<>(nums.length);
        for (int j : smaller) result.add(j);
        return result;
    }
}
```

反过来做

```java
public class Test {

    public static void main(String[] args) {
        int[] smaller = {0, 1, 2, 1, 0};
        int n = 5;
        List<Integer> temp = new ArrayList<>();
        for (int i = 0; i <= n; i++) {
            temp.add(i);
        }
        // temp :[1,2,3,4,5]: put  n - smaller[i] - i of temp into resut
        int[] result = new int[n];
        for (int i = 0; i < n; i++) {
            int k = n - smaller[i] - i;
            result[i] = temp.get(k);

            temp.remove(k);
        }

        for (int r : result) {
            System.out.println(r);
        }
    }
}
```


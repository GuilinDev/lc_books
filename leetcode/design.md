# Design

## 642 Design Search Autocomplete System

### 题目

Design a search autocomplete system for a search engine. Users may input a sentence \(at least one word and end with a special character `'#'`\). For **each character** they type **except '\#'**, you need to return the **top 3** historical hot sentences that have prefix the same as the part of sentence already typed. Here are the specific rules:

1. The hot degree for a sentence is defined as the number of times a user typed the exactly same sentence before.
2. The returned top 3 hot sentences should be sorted by hot degree \(The first is the hottest one\). If several sentences have the same degree of hot, you need to use ASCII-code order \(smaller one appears first\).
3. If less than 3 hot sentences exist, then just return as many as you can.
4. When the input is a special character, it means the sentence ends, and in this case, you need to return an empty list.

Your job is to implement the following functions:

The constructor function:

`AutocompleteSystem(String[] sentences, int[] times):` This is the constructor. The input is **historical data**. `Sentences` is a string array consists of previously typed sentences. `Times` is the corresponding times a sentence has been typed. Your system should record these historical data.

Now, the user wants to input a new sentence. The following function will provide the next character the user types:

`List<String> input(char c):` The input `c` is the next character typed by the user. The character will only be lower-case letters \(`'a'` to `'z'`\), blank space \(`' '`\) or a special character \(`'#'`\). Also, the previously typed sentence should be recorded in your system. The output will be the **top 3** historical hot sentences that have prefix the same as the part of sentence already typed.  

**Example:**  
 **Operation:** AutocompleteSystem\(\["i love you", "island","ironman", "i love leetcode"\], \[5,3,2,2\]\)  
 The system have already tracked down the following sentences and their corresponding times:  
 `"i love you"` : `5` times  
 `"island"` : `3` times  
 `"ironman"` : `2` times  
 `"i love leetcode"` : `2` times  
 Now, the user begins another search:  
  
 **Operation:** input\('i'\)  
 **Output:** \["i love you", "island","i love leetcode"\]  
 **Explanation:**  
 There are four sentences that have prefix `"i"`. Among them, "ironman" and "i love leetcode" have same hot degree. Since `' '` has ASCII code 32 and `'r'` has ASCII code 114, "i love leetcode" should be in front of "ironman". Also we only need to output top 3 hot sentences, so "ironman" will be ignored.  
  
 **Operation:** input\(' '\)  
 **Output:** \["i love you","i love leetcode"\]  
 **Explanation:**  
 There are only two sentences that have prefix `"i "`.  
  
 **Operation:** input\('a'\)  
 **Output:** \[\]  
 **Explanation:**  
 There are no sentences that have prefix `"i a"`.  
  
 **Operation:** input\('\#'\)  
 **Output:** \[\]  
 **Explanation:**  
 The user finished the input, the sentence `"i a"` should be saved as a historical sentence in system. And the following input will be counted as a new search.  

**Note:**

1. The input sentence will always start with a letter and end with '\#', and only one blank space will exist between two words.
2. The number of **complete sentences** that to be searched won't exceed 100. The length of each sentence including those in the historical data won't exceed 100.
3. Please use double-quote instead of single-quote when you write test cases even for a character input.
4. Please remember to **RESET** your class variables declared in class AutocompleteSystem, as static/class variables are **persisted across multiple test cases**. Please see [here](https://leetcode.com/faq/#different-output) for more details.

### 分析

大体意思就是设计一个搜索的自动补全系统，它需要包含如下两个方法：

* AutocompleteSystem\(String\[\] sentences, int\[\] times\): 输入句子sentences，及其出现次数times
* Listinput\(char c\): 输入字符c可以是26个小写英文字母，也可以是空格，以’\#’结尾。返回输入字符前缀对应频率最高的至多3个句子，频率相等时按字典序排列。

1\) 暴力解法的思路（TLE）：

![](../.gitbook/assets/image%20%2869%29.png)

2\) Trie

前缀树包含节点和边，可以像树一样存储字符串。每个节点最多有多个子节点和边（根据存储的字符范围，比如全小写26个，全字符128或256个），边用于连接父节点和子节点，每条边对应 1 个存储的字符。

根据字符串中每个位置出现的字符，从上到下存储字符串。第一层存储字符串中出现的所有首字母，第二层存储字符串中第二个位置出现的所有字母。

前缀树常用于存储字典中的所有单词。数的每一个层表示字符串对应位置上的字符。从词根开始到一个叶节点，就是一个单词。

这道题需要修改普通前缀树的结构，给每个单词增加访问次数标记 times，它存储在对应单词的叶节点。

在方法 AutoComplete 中，将 sentences 数组中所有句子插入到单词查找树中，并在叶节点上添加每个句子出现的次数 times。

下图句子 "A","to", "tea", "ted", "ten", "i", "in"，每个句子出现次数为 15, 7, 3, 4, 12, 11, 5, 9 的Trie。

![](../.gitbook/assets/image%20%2868%29.png)

### 代码

* AutocompleteSystem\(\) 方法的时间复杂度为 O\(k\*l\)，遍历 i 条长度为 k 的句子，并将它们插入到Trie中。
* input\(\) 方法的时间复杂度为O\(p+q+mlog\(m\)\)，其中 p 表示当前已输入句子cursent 的长度，q表示单词查找树中的节点数量，最后对长度为 m 的 list 排序需要时间O\(mlog\(m\)\)。

```java
/**
 将整个句子都视作单词加入字典树。前缀匹配回溯较多，修改字典树结点结构，使得每个结点维护其后计数最大的三个字符串，加速匹配。
 */
class AutocompleteSystem {
    // 构建Trie
    class Trie {
        // 确定路径上的某个字符是否是某个单词（句子）的结尾字符
        boolean isEnding;

        int count;
        String s;

        Trie[] children = new Trie[27]; // 26个字符加‘ ’

        // 小顶堆保存最大的k个
        int k;
        PriorityQueue<Trie> queue;

        Trie(int k) {
            this.k = k;
            // min Heap
            this.queue = new PriorityQueue<>((a, b) -> a.count == b.count ? b.s.compareTo(a.s) : a.count - b.count);
            // 字典树的一条路径的最后一位存空格
            this.children = new Trie[27];

        }

        private void insert(String word, int val) {
            if (word.isEmpty()) {
                return;
            }

            Trie temp = this;
            // 记录路径上的节点
            List<Trie> path = new LinkedList<>();
            for (char c : word.toCharArray()) {
                int index = (c == ' ') ? 26 : (c - 'a');
                if (temp.children[index] == null) {
                    temp.children[index] = new Trie(k);
                }

                temp = temp.children[index];
                path.add(temp);
            }

            // 结尾的字符特殊标记，并进行整个路径的计数更新
            temp.isEnding = true;
            temp.count += val;
            temp.s = word;

            // 关联终止节点到路径上的每个节点
            for (Trie cur : path) {
                // remove old value
                if (cur.queue.contains(temp)) {
                    cur.queue.remove(temp);
                }
                // update new value
                cur.queue.add(temp);
                // 大于k个，将最小的弹出
                while (cur.queue.size() > k) {
                    cur.queue.poll();
                }
            }
        }

        private void search(List<String> results) {
            List<Trie> temp = new ArrayList<>();
            // 加入堆中元素
            while (!queue.isEmpty()) {
                Trie trie = queue.poll();
                temp.add(trie);
                results.add(trie.s);
            }
            queue.addAll(temp);
            Collections.reverse(results);
        }
    }

    // 字典树
    private final Trie root;
    // 记录前一个节点
    private Trie pre;
    //记录历史字符串
    private StringBuilder sb;

    public AutocompleteSystem(String[] sentences, int[] times) {
        this.root = new Trie(3); // 每条路径最多三个单词
        this.pre = root;
        sb = new StringBuilder();

        for (int i = 0; i < sentences.length; i++) {
            root.insert(sentences[i], times[i]);
        }
    }
    
    public List<String> input(char c) {
        List<String> results = new LinkedList<>();
        // 遇到‘#’终止
        if (c == '#') {
            // 更新历史记录
            saveHistory(sb.toString());
            // 清空状态
            pre = root;
            sb = new StringBuilder();
            return results;
        }

        // 尚未终止
        // 记录历史
        sb.append(c);

        // 更新节点
        if (pre != null) {
            pre = (c == ' ') ? pre.children[26] : pre.children[c - 'a'];
        }
        // 搜索其后的所有值
        if (pre != null) {
            pre.search(results);
        }
        return results;
    }

    private void saveHistory(String s) {
        root.insert(s, 1);
    }

    
}

/**
 * Your AutocompleteSystem object will be instantiated and called as such:
 * AutocompleteSystem obj = new AutocompleteSystem(sentences, times);
 * List<String> param_1 = obj.input(c);
 */
```

## 303 Range Sum Query - Immutable

### 题目

Given an integer array nums, find the sum of the elements between indices i and j \(i ≤ j\), inclusive.

**Example:**

```text
Given nums = [-2, 0, 3, -5, 2, -1]

sumRange(0, 2) -> 1
sumRange(2, 5) -> -1
sumRange(0, 5) -> -3
```

**Constraints:**

* You may assume that the array does not change.
* There are many calls to sumRange function.
* `0 <= nums.length <= 10^4`
* `-10^5 <= nums[i] <= 10^5`
* `0 <= i <= j < nums.length`

### 分析

题意简单，求给定作为parameter的Array的区间元素的和，这道题直接做也能通过，但题目的tag的有个DP的标签，于是也可以强行写成DP的方式 - 新设一个类变量数组，nums\[i\]表示从0到i元素的和，那么求sum query的时候直接就nums\[j\] - nums\[i - 1\]就行，包含nums\[i\]，这个算是自顶向下的记忆化搜索了。

### 代码

直接做也可以

```java
class NumArray {
    int[] nums;
    int len;

    public NumArray(int[] nums) {
        len = nums.length;
        this.nums = nums;        
    }
    
    public int sumRange(int i, int j) {
        int result = 0;
        
        if (len == 0) {
            return result;
        }
        
        for(int index = i; index <= j; index++) {
            result += nums[index];
        }
        return result;
    }
}

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * int param_1 = obj.sumRange(i,j);
 */
```

DP的方式

```java
class NumArray {
    int[] nums;
    int len;

    public NumArray(int[] nums) {
        if (nums == null || nums.length == 0) {
            return;
        }
        len = nums.length;
        this.nums = new int[len];
        
        // 记忆化
        this.nums[0] = nums[0];
        for (int i = 1; i < len; i++) {
            this.nums[i] = this.nums[i - 1] + nums[i];
        }      
    }
    
    public int sumRange(int i, int j) {
        // corner cases
        if (nums.length == 0) {
            return 0;
        }
        
        if (i <= 0) { // 这里需要包括等于
            return nums[j];
        }
        
        if (j >= nums.length) {
            j = nums.length - 1; 
        }
        
        return nums[j] - nums[i - 1];
    }
}

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * int param_1 = obj.sumRange(i,j);
 */
```

## 307 Range Sum Query - Mutable

### 题目

Given an integer array nums, find the sum of the elements between indices i and j \(i ≤ j\), inclusive.

The update\(i, val\) function modifies nums by updating the element at index i to val.

**Example:**

```text
Given nums = [1, 3, 5]

sumRange(0, 2) -> 9
update(1, 2)
sumRange(0, 2) -> 8
```

**Constraints:**

* The array is only modifiable by the update function.
* You may assume the number of calls to update and sumRange function is distributed evenly.
* `0 <= i <= j <= nums.length - 1`

### 分析

相比上一题，这道题传入数组可能被一个叫update的方法修改里面的元素。

1\) 方法1，同上一道题，暴力法，别的不管，直接迭代求和。

时间复杂度O\(n\)，空间复杂度O\(1\)。

2\) 方法2，看到一种Divide and Conquer的办法，实际就是记忆化搜索换了个花样，这里学习下，思想是将数组分割成块，块的长度为 sqrt\(n\)。然后计算每个块的和，并将其存储在辅助存储器 b 中。要查询 RSQ\(i, j\)，将添加位于内部的所有块和部分在范围 \[i…j\] 重叠的块的总和。

![](../.gitbook/assets/image%20%28111%29.png)

在上面的示例中，数组 nums 的长度为 9，它被拆分为大小为 sqrt\(9\)的块。为了得到 RSQ\(1, 7\)，添加 b\[1\]。它存储范围 \[3，5\] 的和，以及 块0 和 块2 的部分和，它们是重叠的边界块。

时间复杂度O\(n\)，空间复杂度O\(sqrt\(n\)\)。

3\) 方法3，线段树，主要用来解决多种范围查询问题，比如在对数时间内从数组中找到最小值、最大值、总和、最大公约数、最小公倍数等。

先介绍线段树，如下：

![](../.gitbook/assets/image%20%28112%29.png)

数组 A\[0,1,…,n−1\] 的线段树是一个二叉树，其中每个节点都包含数组的一个子范围 \[i…j\] 上的聚合信息（最小值、最大值、总和等），其左、右子树分别包含范围 \[i…  \(i+j\)/2 \] 和\[\(i+j\)/2 ​+1, j\] 上的信息。

线段树既可以用数组也可以用树来实现。对于数组实现，如果索引 i 处的元素不是一个叶节点，那么其左子树和右子树分别存储在索引为 2i 和 2i+1 的元素处 \(相当于把树给拉直了\)。

在上图所给出的示例中，每个叶节点都包含初始的数组元素 {2,4,5,7,8,9}。内部节点包含范围内相应元素的总和 - \(11\) 是从索引 0 到索引 2 的元素之和。而根节点 \(35\) 是它的两个子节点 \(6\) 和 \(29\) 的和,也是整个数组的和。

线段树可以分为以下三个步骤：

1. 从给定数组**构建**线段树的预处理步骤。 
2. 修改元素时**更新**线段树。 
3. 使用线段树进行**区域和检索**。

**构建线段树**

使用一种非常有效的自下而上的方法来构建线段树。从上面已经知道，如果某个节点 p 包含范围\[i…j\] 的和，那么其左、右子节点分别包含范围 \[i… \(i+j\)/2\] 和 \[\(i+j\)/2 +1, j\] 上的和。

因此，为了找到节点 p 的和，需要提前计算其左、右子子树的和。

从叶节点开始，用输入数组的元素 a\[0,1,…,n−1\] 初始化它们。然后逐步向上移动到更高一层来计算父节点的和，直到最后到达线段树的根节点。

```java
int[] tree;
int n;
public NumArray(int[] nums) {
    if (nums.length > 0) {
        n = nums.length;
        tree = new int[n * 2];
        buildTree(nums);
    }
}
private void buildTree(int[] nums) {
    for (int i = n, j = 0;  i < 2 * n; i++,  j++)
        tree[i] = nums[j];
    for (int i = n - 1; i > 0; --i)
        tree[i] = tree[i * 2] + tree[i * 2 + 1];
}

```

这一步时间复杂度O\(n\)，空间复杂度O\(n\)。

**更新线段树**

当更新数组中某个索引 i 处的元素时，需要重建线段树，因为一些树节点上的和值也会随之产生变化。再次使用自下而上的方法。首先更新存储 a\[i\] 元素的叶节点。从那里一路向上，直到根节点，并用其子节点值的总和来更新每个父节点的值。

```java
void update(int pos, int val) {
    pos += n;
    tree[pos] = val;
    while (pos > 0) {
        int left = pos;
        int right = pos;
        if (pos % 2 == 0) {
            right = pos + 1;
        } else {
            left = pos - 1;
        }
        // parent is updated after child is updated
        tree[pos / 2] = tree[left] + tree[right];
        pos /= 2;
    }
}
```

这一步时间复杂度O\(logn\)，空间复杂度O\(1\)。

**区域和检索**

可以通过以下方式使用线段树进行区域和检索 \[L, R\]：算法保持循环不变：l ≤ r 以及已经算出 \[L…l\] 和 \[r…R\] 的总和，其中 l 和 r 分别是计算总和时的左边界和右边界。每次迭代的范围 \[l,r\] 都会缩小，直到在算法的大约 logn 次迭代后两个边界相遇为止。

```java
public int sumRange(int l, int r) {
    // get leaf with value 'l'
    l += n;
    // get leaf with value 'r'
    r += n;
    int sum = 0;
    while (l <= r) {
        if ((l % 2) == 1) {
           sum += tree[l];
           l++;
        }
        if ((r % 2) == 0) {
           sum += tree[r];
           r--;
        }
        l /= 2;
        r /= 2;
    }
    return sum;
}
```

这一步时间复杂度O\(logn\)，空间复杂度O\(1\)。

### 代码

方法1

```java
class NumArray {
    
    int[] nums;

    public NumArray(int[] nums) {        
        this.nums = new int[nums.length];
        for (int i = 0; i < nums.length; i++) {
            this.nums[i] = nums[i];
        }
    }
    
    public void update(int i, int val) {
        nums[i] = val;
    }
    
    public int sumRange(int i, int j) {
        int sum = 0;
        for (int index = i; index <= j; index++) {
            sum += nums[index];
        }
        return sum;
    }
}

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * obj.update(i,val);
 * int param_2 = obj.sumRange(i,j);
 */
```

方法2

```java
class NumArray {
    
    private int[] b;
    private int len;
    private int[] nums;

    public NumArray(int[] nums) {
        this.nums = nums;
        double l = Math.sqrt(nums.length); //分段
        len = (int) Math.ceil(nums.length/l);
        b = new int [len];
        for (int i = 0; i < nums.length; i++) { // 分段存储
            b[i / len] += nums[i];
        }
    }

    public int sumRange(int i, int j) {
        int sum = 0;
        int startBlock = i / len;
        int endBlock = j / len;
        if (startBlock == endBlock) {
            for (int k = i; k <= j; k++)
                sum += nums[k];
        } else {
            for (int k = i; k <= (startBlock + 1) * len - 1; k++) {
                sum += nums[k];
            }
            for (int k = startBlock + 1; k <= endBlock - 1; k++) {
                sum += b[k];
            }
            for (int k = endBlock * len; k <= j; k++) {
                sum += nums[k];
            }
        }
        return sum;
    }

    public void update(int i, int val) {
        int b_l = i / len;
        b[b_l] = b[b_l] - nums[i] + val;
        nums[i] = val;
    }

}
```

方法3

```java
class NumArray {

    int[] tree;
    int n;
    public NumArray(int[] nums) {
        if (nums.length > 0) {
            n = nums.length;
            tree = new int[n * 2];
            buildTree(nums);
        }
    }
    private void buildTree(int[] nums) { // 线段树构建代码
        for (int i = n, j = 0;  i < 2 * n; i++,  j++)
            tree[i] = nums[j];
        for (int i = n - 1; i > 0; --i)
            tree[i] = tree[i * 2] + tree[i * 2 + 1];
    }

    public int sumRange(int i, int j) {
        // get leaf with value 'l'
        i += n;
        // get leaf with value 'r'
        j += n;
        
        int sum = 0;
        while (i <= j) {
            if ((i % 2) == 1) {
               sum += tree[i];
               i++;
            }
            if ((j % 2) == 0) {
               sum += tree[j];
               j--;
            }
            i /= 2;
            j /= 2;
        }
        return sum;
    }

    public void update(int i, int val) {
        i += n;
        tree[i] = val;
        while (i > 0) {
            int left = i;
            int right = i;
            if (i % 2 == 0) {
                right = i + 1;
            } else {
                left = i - 1;
            }
            // parent is updated after child is updated
            tree[i / 2] = tree[left] + tree[right];
            i /= 2;
        }
    }

}
```




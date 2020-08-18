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


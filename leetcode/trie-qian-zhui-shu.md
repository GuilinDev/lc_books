# Trie前缀树

## 212 Word Search II

### 题目

Given a 2D board and a list of words from the dictionary, find all words in the board.

Each word must be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once in a word.

**Example:**

```text
Input: 
board = [
  ['o','a','a','n'],
  ['e','t','a','e'],
  ['i','h','k','r'],
  ['i','f','l','v']
]
words = ["oath","pea","eat","rain"]

Output: ["eat","oath"]
```

### 分析

这道题意思就是从二维数组中某个字符出发，可以向左向右向上向下移动，走过的路径构成一个字符串，判断是否能走出给定字符串的 word ，走过的字符不能够再走第二次。比如 eat，从第二行最后一列的 e 出发，向左移动，再向左移动，就走出了 eat。题目中给一个 word 列表，要求找出里面哪些单词可以由 board 生成。

1\) [第79 题Word Search](https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking#79-word-search)是判断某个单词能否在 board 中生成，所以暴力解法可以是遍历这道题给出的words的所有单词，每个单词都用79题的DFS的办法来看能不能被生成。

2\) Trie前缀树，[这里是LC的思路](https://leetcode.com/problems/word-search-ii/discuss/59780/Java-15ms-Easiest-Solution-%28100.00%29)。

上面的解法的核心思想是从 words 中依次`选定一个单词 -> 从图中的每个位置出发，看能否找到这个单词`。其实可以倒过来：`从二维数组中的每个位置出发 -> 看遍历过程中是否遇到了 words 中的某个单词`。

想知道遍历过程中判断是否遇到了某个单词，可以事先把所有单词存到前缀树中。这样的话，如果当前走的路径不是前缀树的前缀，就可以提前结束了，而如果是前缀树的中的单词，就将其存到结果中。

关于实现，可以在遍历过程中，将当前路径的单词传进函数，然后判断当前路径构成的单词是否是在前缀树中出现，这个想法可行，但不够好（是上面方法1把DFS换成了Trie），因为每次都从前缀树中判断当前路径的单词，会带来重复的判断。比如先判断了 pan 存在于前缀树中，接下来假如路径变成 panda ，判断它在不在前缀树中，又需要判断一遍 pan 。

因此，可以把前缀树用递归中来传递前缀树的节点，判断当前节点的孩子是否为 null，如果是 null 说明当前的前缀不存在，可以提前结束。如果不是 null，再判断当前节点是否是单词的结尾，如果是结尾直接将当前单词加入。由于递归过程中没有加路径，所以我们改造一下前缀树的节点，将单词直接存入节点，这样的话就可以直接取到了。

### 代码

79题代码套过来的暴力解法，可以accept

```java
class Solution {
    public List<String> findWords(char[][] board, String[] words) {
        List<String> results = new ArrayList<>();
        for (String word : words) {
            if (exist(board, word)) {
                results.add(word);
            }
        }
        return results;
    }
    
    // 下面的代码是把79题的代码抄过来，判断每个单词是否能够被生成
    public boolean exist(char[][] board, String word) {
        if (board == null || board.length == 0 || board[0].length == 0) {
            return false;
        }
        int rows = board.length;
        int cols = board[0].length;
        
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (dfs(board, i, j, word, 0)) {
                    return true;
                }
            }
        }
        return false;
    }
    
    private boolean dfs(char[][] board, int i, int j, String word, int index) {
        if (index == word.length()) {
            return true;
        }
        int m = board.length;
        int n = board[0].length;
        if (i < 0 || i >= m || j < 0 || j >= n || board[i][j] != word.charAt(index)) {
            return false;
        }
        char temp = board[i][j];
        board[i][j] = '#';
        boolean result = dfs(board, i + 1, j, word, index + 1) ||
            dfs(board, i - 1, j, word, index + 1) ||
            dfs(board, i, j + 1, word, index + 1) ||
            dfs(board, i, j - 1, word, index + 1);
        board[i][j] = temp; // 这步不能忘记，检查完当前起点为止的字符串后要恢复访问记录        
        return result;
    }
}
```

Trie解法，具体步骤：

* 首先构造前缀树，使用模板，要熟悉，这一步骤是208 - Implement Trie \(Prefix Tree\)的题解
* 构造前缀树，将其作为全局变量
* 遍历矩阵的每一个点作为起点，做回溯backtrack
  * 回溯方法的参数是row\(行坐标\),col\(纵坐标\),stringbuilder\(存储backtrack之前的字符串\),is\_visit\(记忆哪些点已经访问过\)
  * 回溯的终止条件 - 如果到达矩阵的边界或者当前节点遍历过\(is\_visit\)就直接return
  * stringbuilder加入当前点的字符，并将is\_visit置为true
  * 如果前缀树包含此字符串，且此字符串在前缀树是最终字符串，就加入结果集\(同时去重复\)
  * 如果前缀树搜索含有这个前缀，就继续上下左右搜索
  * 最后删除stringbuilder的最后一个字符，并将is\_visit置为false。

```java
class Solution {
    //------构建前缀树的类，需要掌握，208 - Implement Trie (Prefix Tree)
    class Trie {
        private boolean is_string = false;
        private Trie next[] = new Trie[26];

        public Trie() {
        }

        public void insert(String word) {//插入单词
            Trie root = this;
            char w[] = word.toCharArray();
            for (int i = 0; i < w.length; i++) {
                if (root.next[w[i] - 'a'] == null) {
                    root.next[w[i] - 'a'] = new Trie();
                }
                root = root.next[w[i] - 'a'];
            }
            root.is_string = true;
        }

        public boolean search(String word) {//查找单词
            Trie root = this;
            char w[] = word.toCharArray();
            for (int i = 0; i < w.length; ++i) {
                if (root.next[w[i] - 'a'] == null) return false;
                root = root.next[w[i] - 'a'];
            }
            return root.is_string;
        }

        public boolean startsWith(String prefix) {//查找前缀
            Trie root = this;
            char p[] = prefix.toCharArray();
            for (char c : p) {
                if (root.next[c - 'a'] == null) {
                    return false;
                }
                root = root.next[c - 'a'];
            }
            return true;
        }
    }
    //------上面是构建前缀树的类

    char[][] _board = null;
    ArrayList<String> _result = new ArrayList<>();
    Trie root = new Trie();

    public List<String> findWords(char[][] board, String[] words) {

        // 第一步，构建前缀树
        for (String word : words) {
            root.insert(word);
        }

        this._board = board;
        // 对于矩阵中的每一个点进行回溯
        for (int row = 0; row < board.length; ++row) {
            for (int col = 0; col < board[row].length; ++col) {
                StringBuilder sb = new StringBuilder();//每次到一个点，就先进建一个Stringbuilder类型
                boolean[][] is_visit = new boolean[board.length][board[0].length];
                backtracking(row, col, sb, is_visit);
            }
        }
        return this._result;
    }

    private void backtracking(int row, int col, StringBuilder sb, boolean[][] is_visit) {
        //退出回溯的条件，如果row和col是在边缘
        if (row > _board.length - 1 || row < 0 || col < 0 || col > _board[0].length - 1) {
            return;
        }

        //如果当前节点访问过了就return
        if (is_visit[row][col]) {
            return;
        }
        //如果不是边缘的话，那么sb加入当前的字符
        sb.append(_board[row][col]);
        is_visit[row][col] = true;
        //前缀树包含这个单词的话

        if (root.search(sb.toString())) {
            if (!_result.contains(sb.toString())) {
                _result.add(sb.toString());
            }
        }
        //如果前缀树搜索含有这个前缀，就继续上下左右搜索
        if (root.startsWith(sb.toString())) {
            backtracking(row + 1, col, sb, is_visit);
            backtracking(row - 1, col, sb, is_visit);
            backtracking(row, col - 1, sb, is_visit);
            backtracking(row, col + 1, sb, is_visit);


        }
        sb.deleteCharAt(sb.length() - 1);
        is_visit[row][col] = false;
        //return;
    }
}
```


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

第79 题Word Search是判断某个单词能否在 board 中生成，所以暴力解法可以是遍历这道题给出的words的所有单词，每个单词都用79题的DFS的办法来看能不能被生成

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

解法

```text

```


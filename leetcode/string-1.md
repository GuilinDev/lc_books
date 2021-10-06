# String 1

## 271 Encode and Decode Strings

Design an algorithm to encode **a list of strings** to **a string**. The encoded string is then sent over the network and is decoded back to the original list of strings.

Machine 1 \(sender\) has the function:

```text
string encode(vector<string> strs) {
  // ... your code
  return encoded_string;
}
```

Machine 2 \(receiver\) has the function:

```text
vector<string> decode(string s) {
  //... your code
  return strs;
}
```

So Machine 1 does:

```text
string encoded_string = encode(strs);
```

and Machine 2 does:

```text
vector<string> strs2 = decode(encoded_string);
```

`strs2` in Machine 2 should be the same as `strs` in Machine 1.

Implement the `encode` and `decode` methods.

You are not allowed to solve the problem using any serialize methods \(such as `eval`\).

**Example 1:**

```text
Input: dummy_input = ["Hello","World"]
Output: ["Hello","World"]
Explanation:
Machine 1:
Codec encoder = new Codec();
String msg = encoder.encode(strs);
Machine 1 ---msg---> Machine 2

Machine 2:
Codec decoder = new Codec();
String[] strs = decoder.decode(msg);
```

**Example 2:**

```text
Input: dummy_input = [""]
Output: [""]
```

**Constraints:**

* `1 <= strs.length <= 200`
* `0 <= strs[i].length <= 200`
* `strs[i]` contains any possible characters out of `256` valid ASCII characters.

**Follow up:** Could you write a generalized algorithm to work on any possible set of characters?

### 分析

**Approach 1: Non-ASCII Delimiter**

**Intuition**

Naive solution here is to join strings using delimiters.

> What to use as a delimiter? Each string may contain any possible characters out of 256 valid ascii characters.

Seems like one has to use non-ASCII unichar character, for example `unichr(257)` in Python and `Character.toString((char)257)` in Java \(it's character `ā`\).

![fig](https://leetcode.com/problems/encode-and-decode-strings/Figures/271/delimiter.png)

Here it's convenient to use two different non-ASCII characters, to distinguish between situations of "empty array" and of "array of empty strings".

**Implementation**

Use `split` in Java with a second argument `-1` to make it work as `split` in Python.

**Complexity Analysis**

* Time complexity : O\(N\) both for encode and decode, where N is a number of strings in the input array.
* Space complexity : O\(1\) for encode to keep the output, since the output is one string.O\(N\) for decode keep the output, since the output is an array of strings.



**Approach 2: Chunked Transfer Encoding**

Pay attention to this approach because last year Google likes to ask that sort of low-level optimisation. [Serialize and deserialize BST problem](https://leetcode.com/articles/serialize-and-deserialize-bst/) is a similar example.

This approach is based on the [encoding used in HTTP v1.1](https://en.wikipedia.org/wiki/Chunked_transfer_encoding). It doesn't depend on the set of input characters, and hence is more versatile and effective than Approach 1.

> Data stream is divided into chunks. Each chunk is preceded by its size in bytes.

**Encoding Algorithm**

![fig](https://leetcode.com/problems/encode-and-decode-strings/Figures/271/encodin.png)

* Iterate over the array of chunks, i.e. strings.
  * For each chunk compute its length, and convert that length into 4-bytes string.
  * Append to encoded string :
    * 4-bytes string with information about chunk size in bytes.
    * Chunk itself.
* Return encoded string.

**Decoding Algorithm**

![fig](https://leetcode.com/problems/encode-and-decode-strings/Figures/271/decodin.png)

* Iterate over the encoded string with a pointer `i` initiated as 0. While `i < n`:
  * Read 4 bytes `s[i: i + 4]`. It's chunk size in bytes. Convert this 4-bytes string to integer `length`.
  * Move the pointer by 4 bytes `i += 4`.
  * Append to the decoded array string `s[i: i + length]`.
  * Move the pointer by `length` bytes `i += length`.
* Return decoded array of strings.

**Complexity Analysis**

* Time complexity : O\(N\) both for encode and decode, where N is a number of strings in the input array.
* Space complexity : O\(1\) for encode to keep the output, since the output is one string. O\(N\) for decode keep the output, since the output is an array of strings.

### 代码

1.

```java
public class Codec {
  // Encodes a list of strings to a single string.
  public String encode(List<String> strs) {
    if (strs.size() == 0) return Character.toString((char)258);

    String d = Character.toString((char)257);
    StringBuilder sb = new StringBuilder();
    for(String s: strs) {
      sb.append(s);
      sb.append(d);
    }
    sb.deleteCharAt(sb.length() - 1);
    return sb.toString();
  }

  // Decodes a single string to a list of strings.
  public List<String> decode(String s) {
    String d = Character.toString((char)258);
    if (s.equals(d)) return new ArrayList();

    d = Character.toString((char)257);
    return Arrays.asList(s.split(d, -1));
  }
}

// Your Codec object will be instantiated and called as such:
// Codec codec = new Codec();
// codec.decode(codec.encode(strs));
```

2.

```java
public class Codec {
  // Encodes string length to bytes string
  public String intToString(String s) {
    int x = s.length();
    char[] bytes = new char[4];
    for(int i = 3; i > -1; --i) {
      bytes[3 - i] = (char) (x >> (i * 8) & 0xff);
    }
    return new String(bytes);
  }

  // Encodes a list of strings to a single string.
  public String encode(List<String> strs) {
    StringBuilder sb = new StringBuilder();
    for(String s: strs) {
      sb.append(intToString(s));
      sb.append(s);
    }
    return sb.toString();
  }

  // Decodes bytes string to integer
  public int stringToInt(String bytesStr) {
    int result = 0;
    for(char b : bytesStr.toCharArray())
      result = (result << 8) + (int)b;
    return result;
  }

  // Decodes a single string to a list of strings.
  public List<String> decode(String s) {
    int i = 0, n = s.length();
    List<String> output = new ArrayList();
    while (i < n) {
      int length = stringToInt(s.substring(i, i + 4));
      i += 4;
      output.add(s.substring(i, i + length));
      i += length;
    }
    return output;
  }
}
```


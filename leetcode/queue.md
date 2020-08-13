# Queue

## 621 - Task Scheduler

### 题目

You are given a char array representing tasks CPU need to do. It contains capital letters A to Z where each letter represents a different task. Tasks could be done without the original order of the array. Each task is done in one unit of time. For each unit of time, the CPU could complete either one task or just be idle.

However, there is a non-negative integer `n` that represents the cooldown period between two **same tasks** \(the same letter in the array\), that is that there must be at least `n` units of time between any two same tasks.

You need to return the **least** number of units of times that the CPU will take to finish all the given tasks.

**Example 1:**

```text
Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8
Explanation: 
A -> B -> idle -> A -> B -> idle -> A -> B
There is at least 2 units of time between any two same tasks.
```

**Example 2:**

```text
Input: tasks = ["A","A","A","B","B","B"], n = 0
Output: 6
Explanation: On this case any permutation of size 6 would work since n = 0.
["A","A","A","B","B","B"]
["A","B","A","B","A","B"]
["B","B","B","A","A","A"]
...
And so on.
```

**Example 3:**

```text
Input: tasks = ["A","A","A","A","A","A","B","C","D","E","F","G"], n = 2
Output: 16
Explanation: 
One possible solution is
A -> B -> C -> A -> D -> E -> A -> F -> G -> A -> idle -> idle -> A -> idle -> idle -> A
```

**Constraints:**

* The number of tasks is in the range `[1, 10000]`.
* The integer `n` is in the range `[0, 100]`.

### 分析

安排CPU的任务，简单模拟CPU上下文切换时任务调度器的工作过程，规定在两个相同任务之间至少隔给定的n个时间点，求完成时间总长。

思路比较多，看下面各种方法的代码和解释。

### 代码

1）Greedy，由于题目中规定了两个相同任务之间至少隔n个时间点，那们首先应该处理的出现次数最多的那个任务，先确定好这些高频任务，然后再来安排那些低频任务。如果任务X的出现频率最高，为k次，那么用n个空位将每两个X分隔开，然后按顺序加入其他低频的任务。

来看一个例子：

AAAABBBEEFFGG 3

发现任务A出现了4次，频率最高，于是我们在每个A中间加入n = 3个空位，如下：

A---A---A---A

AB--AB--AB--A \(加入B\)

ABE-ABE-AB--A \(加入E\)

ABEFABE-ABF-A \(加入F，每次尽可能填满或者是均匀填充\)

ABEFABEGABFGA \(加入G\)，完成。



再来看一个例子：

ACCCEEE 2

这次发现任务C和E都出现了三次，那么就将CE看作一个整体，在中间加入一个位置即可：

CE-CE-CE

CEACE-CE \(加入A\)

注意最后那个idle没有可以填充的任务了，只能是idle，不然就不满足相同两个任务之间要隔2个时间点。

仔细观察上面两个例子可以发现，都分成了\(max - 1\)个块 + 最后面的字母，其中max为任务最大出现次数。比如例子1中，A出现了4次，所以有A---模块出现了max - 1 = 4 - 1 =3次，加上最后的A，出现3次的每个模块的长度为4。例子2中，CE-出现了2次，再加上最后的CE，出现2次的每个模块长度为3。

可以发现，模块的次数为任务最大出现次数减1，模块的长度为n + 1，最后加上的字母个数为出现次数最多的那个任务，如果并列最多就当一整块处理。

这就都搞清楚了，按照思路：

1. 统计每个任务出现的次数；
2. 排序，这样出现次数最多的字母就到了末尾（或者开头）；
3. 向前遍历（或向后），找出出现次数一样多的任务个数；
4. 求出总时间长。

```java
class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] counts = new int[26]; // 只有大写字母，所以长度26
        for (char task : tasks) {// 统计任务出现次数
            counts[task - 'A']++;
        }
        
        Arrays.sort(counts);
        int maxCount = counts[25]; // 出现次数最多的任务，在最后面
        
        // 检查是否有并列的出现次数最多的任务
        int otherMaxCount = 25;
        while (otherMaxCount >= 0 && counts[otherMaxCount] == counts[25]) {
            otherMaxCount--;
        }
        
        return Math.max(tasks.length, (maxCount - 1) * (n + 1) + 25 - otherMaxCount);
    }
}
```



2）PriorityQueue：同样的思路，直接思考，先哈希表统计每个任务出现的次数，并建立一个优先队列，把统计好的个数都存入优先队列中，让出现次数多的任务排在队列的前面。

对任务上进行分块，每块能装n + 1个任务，这样每个块中均为最多一种任务类型（才能保证相同任务相隔n），往块中安排任务从优先队列中取，每个任务先取一个，都装到一个临时数组代表的块上，然后遍历取出的任务，对于被取出的每个任务，将哈希表中映射的次数减1，如果减1后，次数仍大于0，则将此任务次数再次排入队列中。

遍历完后如果队列不为空，说明当前的块全部被填满，则结果加上当前块的长度n+1。

上面在队列中取任务是用个变量count来记录取出任务的个数，当前块如果填满了就取出n+1个，如果队列中任务数少于n+1个，那就用count来记录真实取出的个数，当队列为空时，就加上count的个数。

```java
class Solution {
    public int leastInterval(char[] tasks, int n) {
        Map<Character, Integer> map = new HashMap<>(); // 统计各任务个数
        for (char task : tasks) {
            map.put(task, map.getOrDefault(task, 0) + 1);
        }
        
        PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a); // 出现次数多，数字大的任务在前面
        pq.addAll(map.values());
        
        int result = 0;
        int oneChunkTime = n + 1;
        
        while (!pq.isEmpty()) {
            int worktime = 0; // 一个chunk没塞满时候的实际工作时间
            List<Integer> tempChunk = new ArrayList<>(); // 装任务的块
            for (int i = 0; i < n + 1; i++) { // 装任务到块
                if (!pq.isEmpty()) {
                    tempChunk.add(pq.poll());
                    worktime++;
                }
            }
            
            for (int count : tempChunk) { // 检查各任务是否还有剩余
                count--;
                if (count > 0) { // 该类任务还有没完成的，加回pq中待装
                    pq.offer(count);
                }
            }
            
            // 没装满一个块就累加实际时间，装满了就累加n + 1
            result += pq.isEmpty() ? worktime : oneChunkTime;
        }
        return result;
    }
}
```

3） 接下来这种做法前半部分跟上面1\)解法一样，求出最多的次数mx，还有同时出现mx次的不同任务的个数mxCnt。这个解法的思想是先算出所有空出来的位置，然后计算出所有需要填入的task的个数，如果超出了空位的个数，就需要最后再补上相应的个数。注意这里如果有多个任务出现次数相同，那么将其整体放一起，就像上面的第二个例子中的CE一样，那么此时每个part中的空位个数就是n - \(mxCnt - 1\)，那么空位的总数就是part的总数乘以每个part中空位的个数了，那么我们此时除去已经放入part中的，还剩下的task的个数就是task的总个数减去mx \* mxCnt，然后此时和之前求出的空位数相比较，如果空位数要大于剩余的task数，那么则说明还需补充多余的空位，否则就直接返回task的总数即可。

```java
public class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] counter = new int[26];
        int max = 0;
        int maxCount = 0;
        for(char task : tasks) {
            counter[task - 'A']++;
            if(max == counter[task - 'A']) {
                maxCount++;
            }
            else if(max < counter[task - 'A']) {
                max = counter[task - 'A'];
                maxCount = 1;
            }
        }

        int partCount = max - 1;
        int partLength = n - (maxCount - 1);
        int emptySlots = partCount * partLength;
        int availableTasks = tasks.length - max * maxCount;
        int idles = Math.max(0, emptySlots - availableTasks);

        return tasks.length + idles;
    }
}
```

## 346 Moving Average from Data Stream

### 题目

Given a stream of integers and a window size, calculate the moving average of all integers in the sliding window.

Example:

```text
MovingAverage m = new MovingAverage(3); 
m.next(1) = 1 
m.next(10) = (1 + 10) / 2 
m.next(3) = (1 + 10 + 3) / 3 
m.next(5) = (10 + 3 + 5) / 3
```

### 分析

澄清返回的结果是double，实现的方式根据自己熟悉的和复杂度来，如果用array时间复杂度为O\(n\)，因为要移动整个数组元素。

用LinkedList或者Deque，都可以进行两端的操作。

### 代码

```java
class MovingAverage {

    LinkedList<Integer> list;
    int count;
    int sum;

    /** Initialize your data structure here. */
    public MovingAverage(int size) {
        list = new LinkedList<>();
        count = size;
        sum = 0;
    }
    
    public double next(int val) {
        if (list.size() < count) {
            list.add(val);
            sum += val;
            return (double) sum / list.size();
        } else { // list.size() == count
            sum -= list.pollFirst();
            list.add(val);
            sum += val;
        }
        return (double) sum / count;
    }
}

/**
 * Your MovingAverage object will be instantiated and called as such:
 * MovingAverage obj = new MovingAverage(size);
 * double param_1 = obj.next(val);
 */
```

```java
class MovingAverage {

    Deque<Integer> deque;
    int count;
    int sum;

    /** Initialize your data structure here. */
    public MovingAverage(int size) {
        deque = new ArrayDeque<>();
        count = size;
        sum = 0;
    }
    
    public double next(int val) {
        deque.offer(val);
       if (deque.size() <= count) {
           sum += val;
           return (double) sum / deque.size();
       } else {
           sum -= deque.poll();
           sum += val;           
       }
       return (double) sum / count;
    }
}

/**
 * Your MovingAverage object will be instantiated and called as such:
 * MovingAverage obj = new MovingAverage(size);
 * double param_1 = obj.next(val);
 */
```


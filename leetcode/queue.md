# Queue

### 621 - Task Scheduler

#### 原题概述

Given a char array representing tasks CPU need to do. It contains capital letters A to Z where different letters represent different tasks.Tasks could be done without original order. Each task could be done in one interval. For each interval, CPU could finish one task or just be idle.

However, there is a non-negative cooling interval **n** that means between two **same tasks**, there must be at least n intervals that CPU are doing different tasks or just be idle.

You need to return the **least** number of intervals the CPU will take to finish all the given tasks.

**Example 1:**

```text
Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8
Explanation: A -> B -> idle -> A -> B -> idle -> A -> B.
```

**Note:**

1. The number of tasks is in the range \[1, 10000\].
2. The integer n is in the range \[0, 100\].

#### 题意和分析

安排CPU的任务，规定在两个相同任务之间至少隔n个时间点，求时间总长。思路比较多，这里的做法是建立一个优先队列，然后把统计好的个数都存入优先队列中，那么大的次数会在队列的前面。这题还是要分块，每块能装n+1个任务，装任务是从优先队列中取，每个任务取一个，装到一个临时数组中，然后遍历取出的任务，对于每个任务，将其哈希表映射的次数减1，如果减1后，次数仍大于0，则将此任务次数再次排入队列中，遍历完后如果队列不为空，说明该块全部被填满，则结果加上n+1。我们之前在队列中取任务是用个变量count来记录取出任务的个数，我们想取出n+1个，如果队列中任务数少于n+1个，那就用count来记录真实取出的个数，当队列为空时，就加上count的个数。

#### 代码

```java
class Solution {
    public int leastInterval(char[] tasks, int n) {
        Map<Character, Integer> counts = new HashMap<>();
        for (char t : tasks) {
            counts.put(t, counts.getOrDefault(t, 0) + 1);
        }
        PriorityQueue<Integer> pq = new PriorityQueue<Integer>((a, b) -> b - a);
        pq.addAll(counts.values());

        int alltime = 0;
        int cycle = n + 1;
        while (!pq.isEmpty()) {
            int worktime = 0;
            List<Integer> tmp = new ArrayList<Integer>();
            for (int i = 0; i < cycle; i++) {
                if (!pq.isEmpty()) {
                    tmp.add(pq.poll());
                    worktime++;
                }
            }
            for (int cnt : tmp) {
                if (--cnt > 0) {
                    pq.offer(cnt);
                }
            }
            alltime += !pq.isEmpty() ? cycle : worktime;
        }

        return alltime;
    }
}
```

由于题目中规定了两个相同任务之间至少隔n个时间点，那么我们首先应该处理的出现次数最多的那个任务，先确定好这些高频任务，然后再来安排那些低频任务。如果任务F的出现频率最高，为k次，那么我们用n个空位将每两个F分隔开，然后我们按顺序加入其他低频的任务，来看一个例子：

AAAABBBEEFFGG 3

我们发现任务A出现了4次，频率最高，于是我们在每个A中间加入三个空位，如下：

A---A---A---A

AB--AB--AB--A \(加入B\)

ABE-ABE-AB--A \(加入E\)

ABEFABE-ABF-A \(加入F，每次尽可能填满或者是均匀填充\)

ABEFABEGABFGA \(加入G\)

再来看一个例子：

ACCCEEE 2

我们发现任务C和E都出现了三次，那么我们就将CE看作一个整体，在中间加入一个位置即可：

CE-CE-CE

CEACE-CE \(加入A\)

注意最后面那个idle不能省略，不然就不满足相同两个任务之间要隔2个时间点了。

仔细观察上面两个例子可以发现，都分成了\(mx - 1\)块，再加上最后面的字母，其中mx为最大出现次数。比如例子1中，A出现了4次，所以有A---模块出现了3次，再加上最后的A，每个模块的长度为4。例子2中，CE-出现了2次，再加上最后的CE，每个模块长度为3。我们可以发现，模块的次数为任务最大次数减1，模块的长度为n+1，最后加上的字母个数为出现次数最多的任务，可能有多个并列。这样三个部分都搞清楚了，写起来就不难了，我们统计每个大写字母出现的次数，然后排序，这样出现次数最多的字母就到了末尾，然后我们向前遍历，找出出现次数一样多的任务个数，就可以迅速求出总时间长了。

```java
class Solution {
    public int leastInterval(char[] tasks, int n) {

        int[] c = new int[26];
        for(char t : tasks){
            c[t - 'A']++;
        }
        Arrays.sort(c);
        int i = 25;
        while(i >= 0 && c[i] == c[25]) i--;

        return Math.max(tasks.length, (c[25] - 1) * (n + 1) + 25 - i);
    }
}
```

接下来这种做法前半部分跟上面一种解法一样，求出最多的次数mx，还有同时出现mx次的不同任务的个数mxCnt。这个解法的思想是先算出所有空出来的位置，然后计算出所有需要填入的task的个数，如果超出了空位的个数，就需要最后再补上相应的个数。注意这里如果有多个任务出现次数相同，那么将其整体放一起，就像上面的第二个例子中的CE一样，那么此时每个part中的空位个数就是n - \(mxCnt - 1\)，那么空位的总数就是part的总数乘以每个part中空位的个数了，那么我们此时除去已经放入part中的，还剩下的task的个数就是task的总个数减去mx \* mxCnt，然后此时和之前求出的空位数相比较，如果空位数要大于剩余的task数，那么则说明还需补充多余的空位，否则就直接返回task的总数即可。

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


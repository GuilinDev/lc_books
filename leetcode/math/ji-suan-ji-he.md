# 计算几何

## 149 Max Point On a Line

### 原体概述

Given _n_ points on a 2D plane, find the maximum number of points that lie on the same straight line.

**Example 1:**

```text
Input: [[1,1],[2,2],[3,3]]
Output: 3
Explanation:
^
|
|        o
|     o
|  o  
+------------->
0  1  2  3  4
```

**Example 2:**

```text
Input: [[1,1],[3,2],[5,3],[4,1],[2,3],[1,4]]
Output: 4
Explanation:
^
|
|  o
|     o        o
|        o
|  o        o
+------------------->
0  1  2  3  4  5  6
```

### 题意和分析

给一堆二维点，找出一条直线包含最多的点，两点确定一条直线（非重复的两点），直线的方程是y = ax + b； 两个点\(x1, y1\)和\(x2, y2\)的斜率k表示为\(y2 - y1\) / \(x2 - x1\)，斜率使用hashmap保存除数和被除数， 但当x1 = x2时斜率无法计算，需要特殊处理。用HashMap来记录斜率和共线点个数之间的映射，其中第一种重合点的情况假定其斜率为INT\_MIN，第二种情况假定其斜率为INT\_MAX，这样都可以用map映射了。我们还需要顶一个变量duplicate来记录重合点的个数，最后只需和哈希表中的数字相加即为共线点的总数。

### 代码

```java
/**
 * Definition for a point.
 * class Point {
 *     int x;
 *     int y;
 *     Point() { x = 0; y = 0; }
 *     Point(int a, int b) { x = a; y = b; }
 * }
 */
class Solution {
    public int maxPoints(Point[] points) {
        int result = 0;
        for (int i = 0; i < points.length; i++) {
            Map<Map<Integer, Integer>, Integer> map = new HashMap<>();
            int duplicate = 1;//重复的点，不能确定一条直线
            for (int j = i + 1; j < points.length; j++) {
                if (points[i].x == points[j].x && points[i].y == points[j].y) {//重复的点
                    duplicate++;
                    continue;
                }
                int dx = points[j].x - points[i].x;
                int dy = points[j].y - points[i].y;
                int d = gcd(dx, dy);//找到最大公约数
                Map<Integer, Integer> t = new HashMap<>();
                t.put(dx/d, dy/d);
                map.put(t, map.getOrDefault(t, 0) + 1);
            }
            result = Math.max(result, duplicate);
            for (Map.Entry<Map<Integer, Integer>, Integer> entry : map.entrySet()) {
                result = Math.max(result, entry.getValue() + duplicate);
            }
        }
        return result;
    }

    private int gcd(int a, int b) {
        return (b == 0) ? a : gcd(b, a % b);//最大公约数的办法，注意递归的办法
    }
}
```


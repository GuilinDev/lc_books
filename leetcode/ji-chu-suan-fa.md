# 1. 基础算法和算法思想

## 基础排序算法

![](../.gitbook/assets/image%20%2823%29.png)

### 选择排序

针对整型的选择排序

```java
public class SelectionSort {

    //算法类不允许产生实例
    private SelectionSort(){};

    public static void sort (int[] arr) {
       for (int i = 0; i < arr.length; i++) {
           //寻找[i, n)之间的最小值的索引
           int minIndex = i;
           for (int j = i + 1; j < arr.length; j++) {
               if (arr[j] < arr[minIndex]) {
                   minIndex = j;
               }
           }
           //交换i位置和i后面找到的最小值
           swap(arr, i, minIndex);
       }
    }

    private static void swap (int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

    //测试
    public static void main(String[] args) {
        int [] nums = new int[10];
        for (int i = 0; i < nums.length; i++) {
            nums[i] = (int)(Math.random() * 11);
        }

        System.out.println("Before Selection Sorting:");
        for (int num : nums) {
            System.out.print(num + " ");
        }

        System.out.println();

        SelectionSort.sort(nums);
        System.out.println("After Selection Sorting:");
        for (int num : nums) {
            System.out.print(num + " ");
        }
    }
}
```





### 插入排序

## 高级排序算法

![](../.gitbook/assets/image%20%2818%29.png)



## 堆

## 二叉树

## 图

## 基础算法 - DFS，BFS，BST，递归...

## 基本算法思想：递归，分治，回溯搜索，贪心，动态规划...




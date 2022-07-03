
归并排序与与快速排序一样，是采用分治法（Divide and Conquer）的一个非常典型的应用，在排序算法中，同时了解归并排序和快速排序，相互印证，能理解得更深。

<!--more-->

## 详细描述

归并排序的基本思想是，将已有序的子序列合并，可以得到有序的完整序列；即先使每个子序列有序，再使子序列段间有序。若将两个有序表合并成一个有序表，称为**二路归并**。

归并排序详细的执行步骤如下：

1. 申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列；
2. 设定两个指针，最初位置分别为两个已经排序序列的起始位置；
3. 比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置；
4. 重复步骤 3 直到某一指针超出序列尾。

## 算法图解

![归并排序](assets/归并排序.gif)

## 问题解疑

### 归并排序和快速排序比较怎么样？

归并排序的比较次数小于快速排序的比较次数，移动次数一般多于快速排序的移动次数。

### 归并排序的应用在什么场景？

归并排序速度仅次于快速排序，为稳定排序算法，一般用于对总体无序，但是各子项相对有序的序列。

### 归并排序的时间复杂度是多少？

每次合并操作的平均时间复杂度为 $O(n)$，而完全二叉树的深度为 $\log_2n$，总的平均时间复杂度为 $O(n \log n)$。

并且，归并排序的最好、最坏、平均时间复杂度均为 $O(n \log n)$。

## 代码实现

### 排序接口

```java
package cn.fatedeity.algorithm.sort;

/**
 * 排序接口
 */
public interface Sort {
    int[] sort(int[] numbers);
}
```

### 排序抽象类

```java
package cn.fatedeity.algorithm.sort;

/**
 * 排序抽象类
 */
public abstract class AbstractSort implements Sort {
    protected void swap(int[] numbers, int src, int target) {
        int temp = numbers[src];
        numbers[src] = numbers[target];
        numbers[target] = temp;
    }
}
```

### 归并排序类

```java
package cn.fatedeity.algorithm.sort;

/**
 * 归并排序类
 */
public class MergeSort extends AbstractSort {
    private void merge(int[] numbers, int low, int mid, int high) {
        int i = low;
        int j = mid + 1;
        int[] newNumbers = new int[high - low + 1];
        int k = 0;

        while (i <= mid && j <= high) {
            if (numbers[i] < numbers[j]) {
                newNumbers[k++] = numbers[i++];
            } else if (numbers[i] > numbers[j]) {
                newNumbers[k++] = numbers[j++];
            } else {
                newNumbers[k++] = numbers[i++];
                newNumbers[k++] = numbers[j++];
            }
        }
        while (i <= mid) {
            newNumbers[k++] = numbers[i++];
        }
        while (j <= high) {
            newNumbers[k++] = numbers[j++];
        }

        if (high + 1 - low >= 0) {
            System.arraycopy(newNumbers, 0, numbers, low, high + 1 - low);
        }
    }

    private int[] sort(int[] numbers, int low, int high) {
        if (low < high) {
            int mid = (low + high) >> 1;
            this.sort(numbers, low, mid);
            this.sort(numbers, mid + 1, high);
            this.merge(numbers, low, mid, high);
        }
        return numbers;
    }

    @Override
    public int[] sort(int[] numbers) {
        if (numbers.length <= 1) {
            return numbers;
        }

        return this.sort(numbers, 0, numbers.length - 1);
    }
}
```


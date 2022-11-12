# 插入排序的简单理解


插入排对是一个容易被低估的排序算法，虽然其时间复杂度看起来与冒泡排序和选择排序差不多，但是用在相对有序的短序列中却格外的好。

<!--more-->

## 详细描述

插入排序的基本思想是：将一个记录插入到已经排好序的有序表中，从而得到一个新的、记录数增 1 的有序表。

在其实现过程中使用双层循环，外层循环针对除了第一个元素之外的所有元素，内层循环针对当前元素前面的有序表进行待插入位置查找，并进行移动。

选择排序详细的执行步骤如下：

1. 从第一个元素开始，该元素可以认为已经被排序；
2. 取出下一个元素，在已经排序的元素序列中从后向前扫描；
3. 如果该元素（已排序）大于新元素，将该元素移到下一位置；
4. 重复步骤 3，直到找到已排序的元素小于或者等于新元素的位置；
5. 将新元素插入到该位置；
6. 重复步骤 2~5。

## 算法图解

![插入排序](assets/插入排序.gif)

## 问题解疑

### 插入排序是原地排序算法吗？

插入排序算法的运行并不需要额外的存储空间，所以空间复杂度是 $O(1)$，也就是说，这是一个原地排序算法。

### 插入排序是稳定的排序算法吗？

对于值相同的元素，可以选择将后面出现的元素，插入到前面出现元素的后面，这样就可以保持原有的前后顺序不变，所以插入排序是稳定的排序算法。

### 插入排序的时间复杂度是多少？

最好情况时间复杂度为 $O(n)$；最坏情况时间复杂度为 $O(n^2)$；平均时间复杂度为 $O(n^2)$。

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

### 插入排序类

```java
package cn.fatedeity.algorithm.sort;

/**
 * 插入排序类
 */
public class InsertionSort extends AbstractSort {
    @Override
    public int[] sort(int[] numbers) {
        if (numbers.length <= 1) {
            return numbers;
        }

        for (int i = 1; i < numbers.length; i++) {
            for (int j = i; j > 0; j--) {
                // 一直交换到顺序相反
                if (numbers[j - 1] <= numbers[j]) {
                    break;
                }
                this.swap(numbers, j, j - 1);
            }
        }
        return numbers;
    }
}
```



<!--more-->

## 题目信息

源地址：[寻找两个正序数组的中位数](https://leetcode.cn/problems/median-of-two-sorted-arrays/)

给定两个大小分别为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2`。请你找出并返回这两个正序数组的**中位数** 。

算法的时间复杂度应该为 $O(log (m+n))$。

## 提示信息

### 示例 1

```plaintext
输入：nums1 = [1,3], nums2 = [2]
输出：2.00000
解释：合并数组 = [1,2,3] ，中位数 2
```

### 示例 2

```plaintext
输入：nums1 = [1,2], nums2 = [3,4]
输出：2.50000
解释：合并数组 = [1,2,3,4] ，中位数 (2 + 3) / 2 = 2.5
```

### 提示

* `nums1.length == m`
* `nums2.length == n`
* `0 <= m <= 1000`
* `0 <= n <= 1000`
* `1 <= m + n <= 2000`
* `-10^6 <= nums1[i], nums2[i] <= 10^6`

## 实现逻辑

### 归并法

最先想到的解题方法就是，将两个有序数组合并成一个有序数组，然后再对结果集计算中位数。这种方法算是一个比较直接的思路。

但是，合并两个数组是此方法最耗时的操作，并且达到 $O(m+n)$ 的时间复杂度，因此，并不符合题目中算法时间复杂度达到 $O(log (m+n))$ 的要求。

而且，采用此方法的空间复杂度，也达到了 $O(m+n)﻿$ 的程度，其空间占用也不算优。

```java
package cn.fatedeity.algorithm.leetcode;

public class MedianOfTwoSortedArrays {
    public double answer(int[] nums1, int[] nums2) {
        int i = 0, j = 0;
        int[] numbers = new int[nums1.length + nums2.length];
        int index = 0;
        while (i < nums1.length || j < nums2.length) {
            if (i == nums1.length) {
                for (int k = j; k < nums2.length; k++) {
                    numbers[index++] = nums2[k];
                }
                break;
            } else if (j == nums2.length) {
                for (int k = i; k < nums1.length; k++) {
                    numbers[index++] = nums1[k];
                }
                break;
            }

            if (nums1[i] < nums2[j]) {
                numbers[index++] = nums1[i++];
            } else if (nums2[j] < nums1[i]) {
                numbers[index++] = nums2[j++];
            } else {
                numbers[index++] = nums1[i++];
                numbers[index++] = nums2[j++];
            }
        }
        if ((numbers.length & 1) == 0) {
            // 数组长度是偶数
            int mid = numbers.length >> 1;
            return (numbers[mid - 1] + numbers[mid]) / 2f;
        } else {
            return numbers[numbers.length >> 1];
        }
    }
}
```

### 双指针

面对两个数组，也可以采用双指针的方式，只要找到中位数的位置即可。由于两个数组的长度已知，因此中位数对应的两个数组的下标之和也是已知。

基本思路就是，通过双指针将两个数组中较小的值一个一个地过滤掉，直到过滤到中位数的下标位置，即可得到两个数组的中位数。

整个方法相较归并法更优，空间复杂度直接降到了 $O(1)$ 的程度，在运行效率上，其只循环了 $\frac{m+n}2$ 次，但时间复杂度仍然是 $O(m+n)$，未达到题目中 $O(log (m+n))$ 的要求。

```java
package cn.fatedeity.algorithm.leetcode;

public class MedianOfTwoSortedArrays {
    public double answer(int[] nums1, int[] nums2) {
        int m = nums1.length;
        int n = nums2.length;
        int len = m + n;
        int mStart = 0, nStart = 0;
        int left = 0, right = 0;
        for (int i = 0; i <= len >> 1; i++) {
            left = right;
            if (mStart < m && (nStart >= n || nums1[mStart] < nums2[nStart])) {
                right = nums1[mStart++];
            } else {
                right = nums2[nStart++];
            }
        }
        if ((len & 1) == 0) {
            return (left + right) / 2f;
        } else {
            return right;
        }
    }
}
```

### 二分查找

一般要求时间复杂度达到 $O(log (n))$ 的题目，大部分都是可以使用二分的思想来求解。

这道题目也可以采用二分法来使时间复杂度达到 $O(log (m+n))$ 的要求，但确实一个有点反思维逻辑的二分查找。

主要解题思路是，通过合并后的长度来确定 $\frac{1}{2}$ 的选值，比较两个数组中的第 $\frac{1}{2}$ 个值，较小的部分直接过滤掉，直到过滤掉 $\frac{m+n}{2}$ 个数值，则两个数组剩下的第一索引则用来计算中位数。

```java
package cn.fatedeity.algorithm.leetcode;

public class MedianOfTwoSortedArrays {
    public double answer(int[] nums1, int[] nums2) {
        int len1 = nums1.length;
        int len2 = nums2.length;
        int total = len1 + len2;
        int left = (total + 1) >> 1;
        int right = (total + 2) >> 1;
        if (left == right) {
            return this.findK(nums1, 0, nums2, 0, left);
        } else {
            return (this.findK(nums1, 0, nums2, 0, left) + this.findK(nums1, 0, nums2, 0, right)) / 2.0;
        }
    }

    private int findK(int[] nums1, int i, int[] nums2, int j, int k) {
        if (i >= nums1.length) {
            // num1 已经被过滤完
            return nums2[j + k - 1];
        } else if (j >= nums2.length) {
            // num2 已经被过滤完
            return nums1[i + k - 1];
        }
        if (k == 1) {
            // 数组中第一个数值比较
            return Math.min(nums1[i], nums2[j]);
        }

        // 确定每次要比较的两个值
        int mid1 = (i + (k >> 1) - 1) < nums1.length ? nums1[i + (k >> 1) - 1] : Integer.MAX_VALUE;
        int mid2 = (j + (k >> 1) - 1) < nums2.length ? nums2[j + (k >> 1) - 1] : Integer.MAX_VALUE;

        if (mid1 < mid2) {
            return findK(nums1, i + (k >> 1), nums2, j, k - (k >> 1));
        } else {
            return findK(nums1, i, nums2, j + (k >> 1), k - (k >> 1));
        }
    }
}
```


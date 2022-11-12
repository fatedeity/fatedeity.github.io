# LeetCode - 三数之和


<!--more-->

## 题目信息

源地址：[三数之和](https://leetcode.cn/problems/3sum/)

给你一个包含 n 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 `a`，`b`，`c`，使得 `a + b + c = 0`，请你找出所有和为 0 且不重复的三元组。

注意：答案中不可以包含重复的三元组。

## 提示信息

### 示例 1

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
```

### 示例 2

```
输入：nums = []
输出：[]
```

### 示例 3

```
输入：nums = [0]
输出：[]
```

### 限制

- `0 <= nums.length <= 3000`
- `-105 <= nums[i] <= 105`

## 实现逻辑

### 暴力枚举

最先想到的应该是暴力枚举的方法，当然这也是最简单的的方法。

既然这里要找出符合要求的三个数，当然是使用三层循环依次去匹配，缺点就是时间复杂度达到了 $O(n^3)$，当数组长度特别长的时候，程序运行时间也会特别长。

### 哈希匹配

从“两数之和”题目中可以知道，使用哈希表结构采用空间换时间的方式，时间复杂度能从 $O(n^2)$ 降到了 $O(n)$。

如果将这种空间换时间的方式代入这个题目，可以将暴力破解 $O(n^3)$ 的时间复杂度降到 $O(n^2)$，这也是一个非常大的提升。

### 排序 + 双指针

现在思考一下如何将时间复杂度从 $O(n^2)$ 降下去。

1. 首先一层循环是必不可少的，因为至少要对给定的数组做一次枚举；
2. 假如拿到数组的第一个元素，需要思考一下怎么从数组剩下的元素中找到匹配的两个元素，换一个思路其实就是从剩下的数组元素中找到和等于第一个元素相反数的两个元素，所以这里退变成了“两数之和”的逻辑；
3. 如果对剩下的元素采用哈希匹配的方法，其实就和上面的方法一致了，由于答案中不可以包含重复的三元组，这里先对数组进行排序，方便后续跳过相同的答案，再采用数组首尾双指针的方法查找匹配的两个数。

```java
package cn.fatedeity.algorithm.leetcode;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class ThreeSum {
    public List<List<Integer>> answer(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(nums);
        for (int i = 0; i < nums.length; i++) {
            if (i != 0 && nums[i] == nums[i - 1]) {
                // 答案不可以包含重复的三元组，这里相同的数直接过掉
                continue;
            }
            int target = -nums[i];
            int left = i + 1;
            int right = nums.length - 1;
            while (left < right) {
                int diff = target - (nums[left] + nums[right]);
                if (diff > 0) {
                    left++;
                } else if (diff < 0) {
                    right--;
                } else {
                    boolean leftFlag = left == 0 || nums[left] != nums[left - 1];
                    boolean rightFlag = right == nums.length - 1 || nums[right] != nums[right + 1];
                    if (leftFlag || rightFlag) {
                        // 这里的 if 判断仍然是避免答案出现重复的三元组
                        List<Integer> list = new ArrayList<>();
                        list.add(nums[i]);
                        list.add(nums[left]);
                        list.add(nums[right]);
                        result.add(list);
                    }
                    left++;
                    right--;
                }
            }
        }
        return result;
    }
}
```

使用这个方式解决这个题目，耗时的地方有两个：对原数组进行排序；对排序后的数组查找出和为目标数的两个数。

排序的时间复杂度可以看做是 $O(n\log_2n)$，查找出剩余两个数采用了遍历数组并嵌套使用双指针遍历，时间复杂度为 $O(n \times n)$，最终总体的时间复杂度在 $O(n^2)$。

在整个过程当中，除了存储答案需要额外的空间，实际的空间复杂度是 $O(1)$。


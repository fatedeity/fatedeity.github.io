# LeetCode - 两数之和


<!--more-->

## 题目信息

源地址：[两数之和](https://leetcode.cn/problems/two-sum/)

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出和为目标值 `target` 的那两个整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

## 提示信息

### 示例 1

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

### 示例 2

```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

### 示例 3

```
输入：nums = [3,3], target = 6
输出：[0,1]
```

### 限制

- `2 <= nums.length <= 10^3`
- `-10^9 <= nums[i] <= 10^9`
- `-10^9 <= target <= 10^9`
- 只会存在一个有效答案

## 实现逻辑

### 暴力枚举

最先想到的逻辑肯定是使用双层循环暴力查找。

当然，采用这种方式的时间复杂度是 $O(n^2)$，空间复杂度是 $O(1)$，实际效率是非常地低。

```java
package cn.fatedeity.algorithm.leetcode;

public class TwoSum {
    public int[] answer(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] + nums[j] == target) {
                    return new int[]{i, j};
                }
            }
        }
        return new int[0];
    }
}
```

### 哈希匹配

如果采用“空间换时间”的方法，利用哈希表结构查找时间复杂度为 $O(1)$ 的特性，就可以一次循环快速得到结果。

最终，时间复杂度降到 $O(n)$，空间复杂度则变成 $O(n)$。

```java
package cn.fatedeity.algorithm.leetcode;

import java.util.HashMap;

public class TwoSum {
    public int[] answer(int[] nums, int target) {
        HashMap<Integer, Integer> hashMap = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int diff = target - nums[i];
            if (hashMap.containsKey(diff)) {
                return new int[]{hashMap.get(diff), i};
            }
            hashMap.put(nums[i], i);
        }
        return new int[0];
    }
}
```


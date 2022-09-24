
<!--more-->

## 题目信息

源地址：[无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

给定一个字符串 `s`，请你找出其中不含有重复字符的 **最长子串** 的长度。

## 提示信息

### 示例 1

```
输入: s = "abcabcbb"
输出: 3
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

### 示例 2

```
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

### 示例 3

```
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

### 限制

- `0 <= s.length <= 5 - 10^4`
- `s` 由英文字母、数字、符号和空格组成

## 实现逻辑

### 滑动窗口

滑动窗口是一种比较重要的算法思想，常用与解决线性表结构数据的子序列问题，运用之后可以将双层嵌套循环的问题转换为单层循环的问题，降低时间复杂度以提高效率。

这道题目的基本思想是将字符串理解成一个滑动的窗口，然后将这个窗口在这个字符串上滑动，在窗口滑动的过程中，左边会出一个元素，右边会进一个元素，然后只需要计算当前窗口内的元素值即可。

```java
package cn.fatedeity.algorithm.leetcode;

import java.util.HashMap;

public class LongestSubstringWithoutRepeatingCharacters {
    public int answer(String s) {
        int start = 0;
        int result = 0;
        HashMap<Character, Integer> hashMap = new HashMap<>();
        for (int current = 0; current < s.length(); current++) {
            char c = s.charAt(current);
            if (hashMap.containsKey(c)) {
                start = Math.max(hashMap.get(c) + 1, start);
            }
            hashMap.put(c, current);
            result = Math.max(current - start + 1, result);
        }
        return result;
    }
}
```


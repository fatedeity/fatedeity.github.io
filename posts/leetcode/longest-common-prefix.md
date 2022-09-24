
<!--more-->

## 题目信息

源地址：[最长公共前缀](https://leetcode.cn/problems/longest-common-prefix/)

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 `""`。

## 提示信息

### 示例 1

```
输入：strs = ["flower","flow","flight"]
输出："fl"
```

### 提示 2

```
输入：strs = ["dog","racecar","car"]
输出：""
解释：输入不存在公共前缀。
```

### 提示

- `1 <= strs.length <= 200`
- `0 <= strs[i].length <= 200`
- `strs[i]` 仅由小写英文字母组成

## 实现逻辑

### 逐一对比

此方法如其名称，即将数组中的字符串逐一进行对比，先找出第一、第二个字符串的最长公共前缀，再找出第二、第三个字符串的最长公共前缀，以此类推，直至完成所有字符串的比较。

当然，当在对比过程中发现最长公共前缀已经为空，则直接返回即可。

此方式是较为常规的思路，时间复杂度为 $O(mn)$，其中 m 是字符串数组中字符串的平均长度，n 是字符串数组的长度，最坏情况下，字符串数组中的每个字符串都会被比较一次。

根据代码的写法不同，若利用额外的空间则空间复杂度是 $O(n)$，若使用数组中的第一个字符串做相关增减，则空间复杂度是 $O(1)$。

```java
package cn.fatedeity.algorithm.leetcode;

public class LongestCommonPrefix {
    public String answer(String[] strs) {
        StringBuilder stringBuilder = new StringBuilder();
        for (int i = 0; i < strs.length; i++) {
            if (stringBuilder.length() > strs[i].length()) {
                stringBuilder.setLength(strs[i].length());
            }
            for (int j = 0; j < strs[i].length(); j++) {
                char c = strs[i].charAt(j);
                if (i == 0) {
                    stringBuilder.append(c);
                    continue;
                } else if (stringBuilder.charAt(j) != c) {
                    stringBuilder.replace(j, stringBuilder.length(), "");
                }
                if (j >= stringBuilder.length() - 1) {
                    break;
                }
            }
            if (stringBuilder.isEmpty()) {
                return stringBuilder.toString();
            }
        }
        return stringBuilder.toString();
    }
}
```

### 同时对比

同时对比指的是，每一次对比的时候，都将数组中所有的字符串拿出来进行比较，如同时比较数组中所有字符串的第一个字符，然后比较数组中所有字符串的第二个字符，以此类推，直到已对比完整个字符串，或者所在位置的字符有所不同。

此方法也是常规思路中的一种，时间复杂度为 $O(mn)$，其中 m 是字符串数组中字符串的最小长度，n 是字符串数组的长度，最坏情况下，字符串数组中的每个字符串都会被比较一次。

空间复杂度同逐一对比方法一样，最低可以做到 $O(1)$ 的空间复杂度。

```java
package cn.fatedeity.algorithm.leetcode;

public class LongestCommonPrefix {
    public String answer(String[] strs) {
        StringBuilder commonStr = new StringBuilder(strs[0]);
        if (strs.length == 1) {
            return strs[0];
        }
        for (int i = 0; i < strs[0].length(); i++) {
            for (int j = 1; j < strs.length; j++) {
                if (strs[j].length() == i || commonStr.charAt(i) != strs[j].charAt(i)) {
                    commonStr.replace(i, commonStr.length(), "");
                    return commonStr.toString();
                }
            }
        }
        return commonStr.toString();
    }
}
```

### 逐一对比优化

这里是一个优化的逐一对比方法，其实也是使用了编程语言的一些特性，直接判断当前的公共前缀是否是下一个字符串的公共前缀，只有满足所有字符串要求的前缀才是最终结果。

实际落实到代码中就是，没有自己将字符串的每一个字符都做比较，而是使用编程代码中已有的方法做比较，实际的时间复杂度没有脱离 $O(mn)$ 的范畴，但实际效果比自行开发的比较要好。

```java
package cn.fatedeity.algorithm.leetcode;

public class LongestCommonPrefix {
    public String answer(String[] strs) {
        if (strs.length == 1) {
            return strs[0];
        }

        String prefix = strs[0];
        for (String str : strs) {
            while (!str.startsWith(prefix)) {
                prefix = prefix.substring(0, prefix.length() - 1);
                if (prefix.length() == 0) {
                    return "";
                }
            }
        }
        return prefix;
    }
}
```


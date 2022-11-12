# LeetCode - 整数反转


<!--more-->

## 题目信息

源地址：[整数反转](https://leetcode.cn/problems/reverse-integer/)

给你一个 32 位的有符号整数 `x`，返回将 `x` 中的数字部分反转后的结果。

如果反转后整数超过 32 位的有符号整数的范围 `[−2^31,  2^31 − 1]`，就返回 0。

**假设环境不允许存储 64 位整数（有符号或无符号）。**

## 提示信息

### 示例 1

```
输入：x = 123
输出：321
```

### 示例 2

```
输入：x = -123
输出：-321
```

### 示例 3

```
输入：x = 120
输出：21
```

### 示例 4

```
输入：x = 0
输出：0
```

### 提示

- `-2^31 <= x <= 2^31 - 1`

## 实现逻辑

### 投机取巧

假设这道题目没有 **环境不允许存储 64 位整数（有符号或无符号）** 的限制，其实问题很容易解决，只需要将数字转换成正整数，然后从个位开始反转，最后再根据原始整数的符号来设定结果整数的符号。

这种方法的缺陷就是，当数字较大时，需要使用到 64 位整数变量存储结果整数，这样肯定是不符合题目要求了，可以称之为投机取巧。

```java
package cn.fatedeity.algorithm.leetcode;

public class ReverseInteger {
    public int answer(int x) {
        long reverse = 0;
        int rest = Math.abs(x);
        while (rest >= 10) {
            reverse = reverse * 10 + rest % 10;
            rest = (int) (rest / 10.0);
        }
        reverse = reverse * 10 + rest;
        if (x < 0) {
            reverse = -reverse;
        }
        if (reverse < Integer.MIN_VALUE || reverse > Integer.MAX_VALUE) {
            return 0;
        }
        return (int) reverse;
    }
}
```

### 预判超量

这里的思路仍然是将数字从个位一点点转换成结果整数，与投机取巧不同的是，需要预判结果整数是否超过了 32 位的有符号位整数。

```java
package cn.fatedeity.algorithm.leetcode;

public class ReverseInteger {
    public int answer(int x) {
        int reverse = 0;
        while (x != 0) {
            // -2147483648 and 2147483647
            if (reverse < Integer.MIN_VALUE / 10 || reverse > Integer.MAX_VALUE / 10) {
                return 0;
            }
            reverse = reverse * 10 + x % 10;
            x /= 10;
        }
        return reverse;
    }
}
```

可能有人疑惑，为什么中途判断的时候只需要是 $\frac{1}{10}$ 即可，不需要再将 `x % 10` 的值算上。

其根本原因还是，给定的整数范围是 [-2147483648, 2147483647]，在这个范围内，给定整数最高位只能是 2，得到的结果集最高位却支持到 7，因此，个位上的比较也就不重要了。


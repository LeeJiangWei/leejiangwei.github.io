---
title: 最长回文子序列
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
  - 字符串
index_img: /img/code.jpg
sticky: -10
date: 2022-02-11 23:58:35
---

# 最长回文子序列

https://leetcode-cn.com/problems/longest-palindromic-subsequence/

## 动态规划

对于这种一个字符串，但是需要用到双向信息的题，需要一个二维的 DP 表来储存结果。

在子数组 `s[i..j]` 中，我们要求的子序列（最长回文子序列）的长度为 `dp[i][j]`

先不去想怎么求出这个长度，用数学归纳法的思想，假设已经知道了它掐头去尾的子串 `s[i+1, ..., j-1]` 中最长回文子串的长度，如何求出 `dp[i][j]` 呢？

答案是看这两个新进来的字符 `s[i]` 和 `s[j]`，如果它们相等，那一定能拼出一个更长的回文串，则 `dp[i][j] = dp[i+1][j-1] + 2`。如果它们不相等，再去看它们只加进去 1 个字符后产生的回文串长度，即 `dp[i][j] = dp[i][j] = max(dp[i+1][j], dp[i][j-1])`。

考虑完递归条件，考虑 base case。显然 base case 就是只有一个字符串时回文长度就是 1，以及空字符串为 0。最后画出 DP 表，由于我们需要求整个序列的长度 `dp[0][n-1]`，从表中可以看出 base case 和最终状态的关系，按照合适的顺序遍历表计算结果即可。

![DP 表](https://github.com/labuladong/fucking-algorithm/raw/master/pictures/%E6%9C%80%E9%95%BF%E5%9B%9E%E6%96%87%E5%AD%90%E5%BA%8F%E5%88%97/4.jpg)

![DP 表的遍历顺序](https://github.com/labuladong/fucking-algorithm/raw/master/pictures/%E6%9C%80%E9%95%BF%E5%9B%9E%E6%96%87%E5%AD%90%E5%BA%8F%E5%88%97/5.jpg)

```go
func longestPalindromeSubseq(s string) int {
    n := len(s)
    dp := make([][]int, n)
    for i := range dp {
        dp[i] = make([]int, n)
    }

    // base case: single char
    for i := 0; i < n; i++ { 
        dp[i][i] = 1
    }

    for i := n - 1; i >= 0; i-- {
        for j := i + 1; j < n; j++ {
            if s[i] == s[j] {
                dp[i][j] = dp[i+1][j-1] + 2
            } else {
                dp[i][j] = max(dp[i+1][j], dp[i][j-1])
            }
        }
    }
    return dp[0][n-1]
}

func max(values ...int) int {
    res := values[0]
    for _, v := range values {
        if res < v {
            res = v
        }
    }
    return res
}
```

---
title: 让字符串成为回文串的最少插入次数
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
date: 2022-02-14 22:52:11
---

# 让字符串成为回文串的最少插入次数

https://leetcode-cn.com/problems/minimum-insertion-steps-to-make-a-string-palindrome/

## 动态规划

解决回文问题其实都差不多，用二维 DP 表表示状态，然后从两端看字符匹配与否。

这里 `dp[i][j]` 表示让字符串 `s[i..j]` 成为回文串所需要的最少插入次数。

base case 很简单，当 `i == j` 时或空串时，字符串自己就是回文了，无需任何操作，为 0。

现在知道了 `dp[i+1][j-1]`，要想知道 `dp[i][j]`，只需要看 `s[i]` 和 `s[j]`两个字符（因为中间的已经可以视作是回文了。如果它们已经相等了，则无需任何操作；如果不相等，往其中一边插入一个字符（不一定需要两边都插）。

```go
func minInsertions(s string) int {
    n := len(s)
    dp := make([][]int, n)
    for i := range dp {
        dp[i] = make([]int, n)
    }

    for i := n - 1; i >= 0; i-- {
        for j := i + 1; j < n; j++ {
            if s[i] == s[j] {
                // 相等，无需操作
                dp[i][j] = dp[i+1][j-1]
            } else {
                // 不相等，往其中一侧插入，取操作数最少的那一侧
                dp[i][j] = 1 + min(dp[i][j-1], dp[i+1][j])
            }
        }
    }
    return dp[0][n-1]
}

func min(values ...int) int {
    res := values[0]
    for _, v := range values {
        if v < res {
            res = v
        }
    }
    return res
}
```
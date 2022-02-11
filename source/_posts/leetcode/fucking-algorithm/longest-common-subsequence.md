---
title: 最长公共子序列
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
date: 2022-02-11 10:39:13
---

# 最长公共子序列

https://leetcode-cn.com/problems/longest-common-subsequence/

> 子序列类型的问题，穷举出所有可能的结果都不容易，而动态规划算法做的就是穷举 + 剪枝，它俩天生一对儿。所以可以说只要涉及子序列问题，十有八九都需要动态规划来解决，往这方面考虑就对了。

## 带备忘录的递归

核心思想：如果两个字符串出现了一样的字符，那它必在 LCS 中。

用两个指针 `i` 和 `j` 从后往前遍历 s1 和 s2，如果 `s1[i]==s2[j]`，那么这个字符一定在 LCS 中；否则的话，`s1[i]` 和 `s2[j]` 这两个字符至少有一个不在 LCS 中，需要丢弃一个，即其中一个指针往前移。至于是哪个，答案是都试一次，然后取最大的。

```go
func longestCommonSubsequence(text1 string, text2 string) int {
    m := len(text1)
    n := len(text2)
    memo := make([][]int, m)
    for i := range memo {
        memo[i] = make([]int, n)
        for j := range memo[i] {
            memo[i][j] = -1
        }
    }

    return dp(memo, text1, text2, len(text1)-1, len(text2)-1)
}

func dp(memo [][]int, text1 string, text2 string, i int, j int) int {
    // base case
    if i == -1 || j == -1 {
        return 0
    }

    // 查备忘录
    if memo[i][j] != -1 {
        return memo[i][j]
    }

    if text1[i] == text2[j] {
        memo[i][j] = 1 + dp(memo, text1, text2, i-1, j-1) // LCS 长度加1
        return memo[i][j]
    } 
    memo[i][j] = max(dp(memo, text1, text2, i, j-1), dp(memo, text1, text2, i-1, j))
    return memo[i][j]
}

func max(a int, b int) int {
    if a > b {
        return a
    }
    return b
}
```

## 动态规划

字符串的 DP 表一般都是比字符串本身多出一位以表示处理完（空字符串）的情况，如图：

![DP 表](https://github.com/labuladong/fucking-algorithm/raw/master/pictures/LCS/dp.png)

`dp[i][j]` 的含义是：对于 `s1[1..i]` 和 `s2[1..j]`，它们的 LCS 长度是 `dp[i][j]`。当 i 或 j 为 0 时，空串和任何字符串的 LCS 显然都是 0。

```go
func longestCommonSubsequence(text1 string, text2 string) int {
    m := len(text1)
    n := len(text2)
    dp := make([][]int, m + 1)
    for i := range dp {
        dp[i] = make([]int, n + 1)
    }
    
    for i := 1; i <= m; i++ {
        for j := 1; j <= n; j++ {
            if text1[i-1] == text2[j-1] {
                dp[i][j] = 1 + dp[i-1][j-1]
            } else {
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
            }
        }
    }
    return dp[m][n]
}

func max(a int, b int) int {
    if a > b {
        return a
    }
    return b
}
```

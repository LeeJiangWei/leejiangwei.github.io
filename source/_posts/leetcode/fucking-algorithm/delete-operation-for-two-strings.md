---
title: 两个字符串的删除操作
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
date: 2022-02-11 13:49:53
---

# 两个字符串的删除操作

https://leetcode-cn.com/problems/delete-operation-for-two-strings/

跟编辑距离几乎是一样的，不过是只有删除操作。

## 带备忘录的递归

核心思想：如果字符串不匹配，2 种删除操作都试一遍（删除 s1 当前字符或 s2 当前字符），找出操作数最少的。

DP 函数定义：

```go
func minDistance(word1 string, word2 string) int {
    m := len(word1)
    n := len(word2)
    memo := make([][]int, m)
    for i := range memo {
        memo[i] = make([]int, n)
        for j := range memo[i] {
            memo[i][j] = -1
        }
    }

    return dp(memo, word1, word2, m-1, n-1)
}

func dp(memo [][]int, word1 string, word2 string, i int, j int) int {
    // base case
    if i == -1 {
        return j + 1
    }
    if j == -1 {
        return i + 1
    }

    // 查备忘录
    if memo[i][j] != -1 {
        return memo[i][j]
    }

    if word1[i] == word2[j] {
        memo[i][j] = dp(memo, word1, word2, i-1, j-1)
        return memo[i][j]
    }

    memo[i][j] = min(dp(memo, word1, word2, i-1, j)+1, dp(memo, word1, word2,i,j-1)+1)
    return memo[i][j]
}

func min(a int, b int) int {
    if a < b {
        return a
    }
    return b
}
```

## 动态规划

和编辑距离是一样的思路

```go
func minDistance(word1 string, word2 string) int {
    m := len(word1)
    n := len(word2)
    dp := make([][]int, m + 1)
    for i := range dp {
        dp[i] = make([]int, n + 1)
    }

    // base cases
    for i := 1; i <= m; i++ {
        dp[i][0] = i
    }
    for j := 1; j <= n; j++ {
        dp[0][j] = j
    }

    // dp
    for i := 1; i <= m; i++ {
        for j := 1; j <= n; j++ {
            if word1[i-1] == word2[j-1] {
                dp[i][j] = dp[i-1][j-1]
            } else {
                dp[i][j] = min(dp[i-1][j]+1, dp[i][j-1]+1)
            }
        }
    }
    return dp[m][n]
}

func min(a int, b int) int {
    if a < b {
        return a
    }
    return b
}
```

---
title: 两个字符串的最小ASCII删除和
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
date: 2022-02-11 18:02:53
---

# 两个字符串的最小ASCII删除和

https://leetcode-cn.com/problems/delete-operation-for-two-strings/

跟编辑距离几乎是一样的，只是把操作数换成 ASCII 码罢了。

## 动态规划

```go
func minimumDeleteSum(s1 string, s2 string) int {
    m := len(s1)
    n := len(s2)
    dp := make([][]int, m + 1)
    for i := range dp {
        dp[i] = make([]int, n + 1)
    }

    for i := 1; i <= m; i++ {
        dp[i][0] = dp[i-1][0] + int(s1[i-1])
    }
    for j := 1; j <= n; j++ {
        dp[0][j] = dp[0][j-1] + int(s2[j-1])
    }

    for i := 1; i <= m; i++ {
        for j := 1; j <= n; j++ {
            if s1[i-1] == s2[j-1] {
                dp[i][j] = dp[i-1][j-1]
            } else {
                dp[i][j] = min(dp[i-1][j]+int(s1[i-1]), dp[i][j-1]+int(s2[j-1]))
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

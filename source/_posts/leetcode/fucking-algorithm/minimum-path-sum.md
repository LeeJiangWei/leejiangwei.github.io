---
title: 最小路径和
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-02-13 11:04:31
---

# 最小路径和

https://leetcode-cn.com/problems/minimum-path-sum/

## 动态规划

```go
func minPathSum(grid [][]int) int {
    m, n := len(grid), len(grid[0])
    dp := make([][]int, m)
    for i := range dp {
        dp[i] = make([]int, n)
    }

    dp[0][0] = grid[0][0]
    for i := 1; i < m; i++ {
        dp[i][0] = dp[i-1][0] + grid[i][0]
    }
    for j := 1; j < n; j++ {
        dp[0][j] = dp[0][j-1] + grid[0][j]
    }

    for i := 1; i < m; i++ {
        for j := 1; j < n; j++ {
            dp[i][j] = min(dp[i-1][j], dp[i][j-1]) + grid[i][j]
        }
    }
    
    return dp[m-1][n-1]
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

## 空间压缩

![](https://labuladong.gitee.io/algo/images/%e6%9c%80%e7%9f%ad%e8%b7%af%e5%be%84%e5%92%8c/1.jpeg)

很容易看出来，得到初始值后可以逐行更新，每一行的结果只依赖于上一行以及自己的前一个结果，可以按从上到下、从左到右的顺序遍历，只需要 1 维的 DP 数组。

```go
func minPathSum(grid [][]int) int {
    m, n := len(grid), len(grid[0])
    dp := make([]int, m)
    
    dp[0] = grid[0][0]
    for i := 1; i < m; i++ {
        dp[i] = dp[i-1] + grid[i][0]
    }

    for j := 1; j < n; j++ {
        dp[0] = grid[0][j] + dp[0]
        for i := 1; i < m; i++ {
            dp[i] = min(dp[i-1], dp[i]) + grid[i][j]
        }
    }
    
    return dp[m-1]
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
---
title: 下降路径最小和
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-02-11 10:39:13
---

# 下降路径最小和

https://leetcode-cn.com/problems/minimum-falling-path-sum/

## 带备忘录的递归

核心思想：下落到 `matrix[i][j]` 的最小路径，一定是 `matrix[i][j]` 自己的值，加上从它上面落下来的最小路径，即 `matrix[i-1][j-1]` `matrix[i-1][j]` `matrix[i-1][j+1]` 中最小的一个。

DP 函数定义：

```go
func dp(matrix [][]int, i int, j int) int {
    // 返回下落到 matrix[i][j] 的最小路径
}
```

```go
func minFallingPathSum(matrix [][]int) int {
    memo := make([][]int, len(matrix))
    for i := range memo {
        memo[i] = make([]int, len(matrix[0]))
        for j := range memo[i] {
            memo[i][j] = 66666
        }
    }
    res := math.MaxInt32
    for j := 0; j < len(matrix[0]); j++ {
        res = min(res, dp(memo, matrix, len(matrix)-1, j))
    }
    return res
}

func dp(memo [][]int, matrix [][]int, i int, j int) int {
    // invalid matrix index
    if i < 0 || j < 0 || j >= len(matrix[0]) {
        return 99999
    }

    // base case
    if i == 0 {
        return matrix[0][j]
    }
    
    // 检查备忘录
    if memo[i][j] != 66666 {
        return memo[i][j]
    }
    v1 := dp(memo, matrix, i-1, j-1)
    v2 := dp(memo, matrix, i-1, j)
    v3 := dp(memo, matrix, i-1, j+1)

    v := min(v1, min(v2, v3))
    memo[i][j] = v + matrix[i][j]
    return memo[i][j]
}

func min(a int, b int) int {
    if a < b {
        return a
    }
    return b
}
```

备忘录初始化：需要是一个不合法的值，题目给定了 `matrix` 最多是 100 x 100 的，元素在 [-100, 100] 之间，因此合法值范围为 [-10000, 10000]，备忘录初始化以及越界返回值都应该在此值之外。由于用了 `min()` 来找最小值，越界的话给一个很大的值即可。

## 动态规划

构建 DP 表：`dp[i][j]` 存储 下落到 `matrix[i][j]` 的最小路径和。特殊处理一下 j=0 和 j=n-1 的特殊情况即可，从第一行开始逐步往后加。

```go
func minFallingPathSum(matrix [][]int) int {
    n := len(matrix)
    dp := make([][]int, n)
    for i := range dp {
        dp[i] = make([]int, n)
    }

    // base case
    for j := 0; j < n; j++ {
        dp[0][j] = matrix[0][j]
    }

    // dp
    for i := 1; i < n; i++ {
        // j == 0 
        dp[i][0] = matrix[i][0] + min(dp[i-1][0], dp[i-1][1])

        for j := 1; j < n - 1; j++ {
            dp[i][j] = matrix[i][j] + min(dp[i-1][j-1], min(dp[i-1][j], dp[i-1][j+1]))
        }
        
        // j == n-1
        dp[i][n-1] = matrix[i][n-1] + min(dp[i-1][n-1], dp[i-1][n-2])
    }

    res := math.MaxInt32
    for j := 0; j < n; j++ {
        res = min(res, dp[n-1][j])
    }
    return res
}

func min(a int, b int) int {
    if a < b {
        return a
    }
    return b
}
```

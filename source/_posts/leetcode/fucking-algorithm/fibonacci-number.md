---
title: 斐波那契数列
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-02-09 17:02:35
---

# 斐波那契数列

https://leetcode-cn.com/problems/fibonacci-number/

## 带备忘录的递归

```go
func fib(n int) int {
    memo := make([]int, n + 1)
    return helper(memo, n)
}

func helper(memo []int, n int) int {
    if n == 0 || n == 1 {
        return n
    }
    if memo[n] != 0 {
        return memo[n]
    } else {
        memo[n] = helper(memo, n - 1) + helper(memo, n - 2)
        return memo[n]
    }
}
```

## 动态规划

直接构建 DP 数组

```go
func fib(n int) int {
    // base case
    if n == 0 || n == 1 {
        return n
    }

    dp := make([]int, n + 1)
    dp[1] = 1

    for i := 2; i < len(dp); i++ {
        dp[i] = dp[i - 1] + dp[i - 2]
    }

    return dp[n]
}
```

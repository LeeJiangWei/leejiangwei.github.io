---
title: 零钱兑换
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-02-09 17:45:35
---

# 零钱兑换

https://leetcode-cn.com/problems/coin-change/

## 带备忘录的递归

```go
func coinChange(coins []int, amount int) int {
    dict := make(map[int]int)
    return dp(dict, coins, amount)
}

func dp(memo map[int]int, coins[]int, amount int) int {
    // base case
    if amount < 0 {
        return -1
    }
    if amount == 0 {
        return 0
    }

    // check memory if exists
    if val, ok := memo[amount]; ok {
        return val
    }

    res := math.MaxInt16

    for _, coin := range coins {
        subproblem := dp(memo, coins, amount - coin)
        if subproblem == -1 {
            continue
        }
        res = min(res, 1 + subproblem)
    }
    
    if res == math.MaxInt16 {
        res = -1
    }

    memo[amount] = res
    return res
}

func min(a int, b int) int {
    if a < b {
        return a
    } else {
        return b
    }
}
```

## 动态规划

直接构建 DP 数组：
`dp[i]` 表示数额为 i 时要的最少硬币个数。数组的索引就是变量，储存的值是需要计算的值。

```go
func coinChange(coins []int, amount int) int {
    // init
    dp := make([]int, amount + 1)
    for i, _ := range dp {
        dp[i] = math.MaxInt16
    }

    // base case
    dp[0] = 0

    for i, _ := range dp {
        for _, coin := range coins {
            // no solution
            if i - coin < 0 {
                continue
            }
            dp[i] = min(dp[i], 1 + dp[i - coin])
        }
    }

    if dp[amount] == math.MaxInt16 {
        return - 1
    }
    return dp[amount]
}

func min(a int, b int) int {
    if a < b {
        return a
    } else {
        return b
    }
}
```

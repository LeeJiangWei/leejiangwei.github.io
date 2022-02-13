---
title: 地下城游戏
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-02-13 12:14:31
---

# 地下城游戏

https://leetcode-cn.com/problems/dungeon-game/

## 动态规划

跟 [最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/) 有点像，但是不同。由于会扣血，且骑士**任意时刻的生命值不能低于 1**，在选择下一个格子怎么走时，必须知道当前的血量。如果把 DP 表定义为走到 `grid[i][j]` 时所需的最少生命值，就无法得知当前的生命值了，就不能保证生命值一直大于 1 了。

重新定义 DP 表为：从 `grid[i][j]` 到达终点（右下角）所需的最少生命值是 `dp[i][j]`。因此如果知道了 `dp[i+1][j]` 和 `dp[i][j+1]`，就能根据当前格子状态 `grid[i][j]` 推断出 `dp[i][j]` 了。最终求的是 `dp[0][0]`。

```go
func calculateMinimumHP(dungeon [][]int) int {
    m, n := len(dungeon), len(dungeon[0])
    dp := make([][]int, m)
    for i := range dp {
        dp[i] = make([]int, n)
    }

    // base case: 最后一格需要的血量
    if dungeon[m-1][n-1] > 0 {
        dp[m-1][n-1] = 1
    } else {
        dp[m-1][n-1] = 1 - dungeon[m-1][n-1]
    }

    // base case: 沿着最下面和最右边走的路线
    for i := m - 2; i >= 0; i-- {
        dp[i][n-1] = dp[i+1][n-1] - dungeon[i][n-1]
        if dp[i][n-1] < 1 {  // 保证血量不低于 1
            dp[i][n-1] = 1
        }
    }
    for j := n - 2; j >= 0; j-- {
        dp[m-1][j] = dp[m-1][j+1] - dungeon[m-1][j]
        if dp[m-1][j] < 1 { // 保证血量不低于 1
            dp[m-1][j] = 1
        }
    }

    for i := m-2; i >= 0; i-- {
        for j := n-2; j >= 0; j-- {
            dp[i][j] = min(dp[i+1][j], dp[i][j+1]) - dungeon[i][j]
            if dp[i][j] < 1 { // 保证血量不低于 1
                dp[i][j] = 1
            }
        }
    }

    return dp[0][0]
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
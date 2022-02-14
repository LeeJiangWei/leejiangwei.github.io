---
title: 石子游戏
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-02-14 22:34:31
---

# 石子游戏

https://leetcode-cn.com/problems/stone-game/

## 动态规划

DP 表定义：面对 `[i, j]` 之间的石头，先、后手的人分别能获得的最大石头数量。`dp[i][j][0]` 表示先手所得的最大石头，`dp[i][j][1]` 表示后手所得的最大石头。

由于石头只能从两端拿，如果先手拿了左边的，则石头变为 `[i+1, j]`，且自己转换为后手，即自己的下一步能获得的最大石头 `dp[i+1][j][1]`。同理，后手者等待先手拿完后，面对 `[i+1, j]` 这一堆石头，自己变成了先手，能获得的最大石头为 `dp[i+1][j][0]`。

```go
func stoneGame(piles []int) bool {
    n := len(piles)
    dp := make([][][]int, n)
    for i := range dp {
        dp[i] = make([][]int, n)
        for j := range dp[i] {
            dp[i][j] = make([]int, 2)
        }
    }

    // base case: 当只剩下 1 堆石头时，先手者获得这些石头
    for i := 0; i < n; i++ {
        dp[i][i][0] = piles[i]
    }

    // 遍历顺序：由 DP 表图示决定
    for i := n-1; i >= 0; i-- {
        for j := i+1; j < n; j++ {

            left := piles[i] + dp[i+1][j][1]   // 拿了左边的，石堆从左边减少，自己变后手
            right := piles[j] + dp[i][j-1][1]  // 拿了右边的，石堆从右边减少，自己边后手

            // 先手的人选择最终结果最大的
            if left > right {
                dp[i][j][0] = left
                dp[i][j][1] = dp[i+1][j][0]  // 后手成为下一个石堆状态的先手
            } else {
                dp[i][j][0] = right
                dp[i][j][1] = dp[i][j-1][0] // 后手成为下一个石堆状态的先手
            }
        }
    }

    return dp[0][n-1][0] > dp[0][n-1][1]  // 判断游戏的先手者是否胜利
}
```
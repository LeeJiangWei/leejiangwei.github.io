---
title: 零钱兑换 II
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
  - 背包问题
index_img: /img/code.jpg
sticky: -10
date: 2022-02-12 16:05:31
---

# 零钱兑换 II

https://leetcode-cn.com/problems/coin-change-2/

此题是完全背包问题，即物品数量是无限的。

## 动态规划

对于背包问题，DP 表 `dp[i][j]` 表示：若只使用 `coins` 中的前 `i` 个硬币的面值，若想凑出金额 `j`，有 `dp[i][j]` 种凑法。

+ 如果你不把这第 `i` 个物品装入背包，也就是说你不使用 `coins[i]` 这个面值的硬币，那么凑出面额 j 的方法数 `dp[i][j]` 应该等于 `dp[i-1][j]`，继承之前的结果。
+ 如果你把这第 `i` 个物品装入了背包，也就是说你使用 `coins[i]` 这个面值的硬币，那么 `dp[i][j]` 应该等于 `dp[i][j-coins[i-1]]`。（注意这里是 dp[**i**][j-coins[i-1]] ，已经包括了再次重复使用这个硬币的情况）

综上就是两种选择，而我们想求的 `dp[i][j]` 是「共有多少种凑法」，所以 `dp[i][j]` 的值应该是以上两种选择的结果之和：

```go
func change(amount int, coins []int) int {
    n := len(coins)
    dp := make([][]int, n + 1)
    for i := range dp {
        dp[i] = make([]int, amount + 1)
    }

    // base case: 如果 amount 为 0，只有 1 种办法凑出（不放硬币）
    for i := 0; i <= n; i++ {
        dp[i][0] = 1
    }

    for i := 1; i <= n; i++ {
        for j := 1; j <= amount; j++ {
            if j - coins[i-1] >= 0 {
                dp[i][j] = dp[i-1][j] + dp[i][j - coins[i-1]]
            } else {
                dp[i][j] = dp[i-1][j]
            }
        }
    }

    return dp[n][amount]
}
```

## 压缩 DP 表

可以看出 `dp[i][..]` 的状态总是且仅依赖于 `dp[i-1][..]` 的状态，因此可以只使用 1D 数组来完成， 每次在数组上迭代就相当于 `i` 的递增。


```go
func change(amount int, coins []int) int {
    n := len(coins)
    dp := make([]int, amount + 1)

    // base case: 如果 amount 为 0，只有 1 种办法凑出（不放硬币）
    dp[0] = 1

    for i := 1; i <= n; i++ {
        for j := 1; j <= amount; j++ {
            if j - coins[i-1] >= 0 {
                dp[j] = dp[j] + dp[j - coins[i-1]]
            }
        }
    }

    return dp[amount]
}
```

与 [分割等和子集](https://leetcode-cn.com/problems/partition-equal-subset-sum/solution/) 不同，这里压缩 DP 表不需要从后遍历 `j`，因为观察压缩前的状态转移公式，`dp[i-1][j]` 只会用于更新 `dp[i][j]`，可以直接覆盖掉而不影响其它结果。

（在上一题中，`dp[i-1][j]` 可能会用于更新 `dp[i][j + nums[i]]` 的值，如果从头开始遍历，更新到 `dp[i-1][j + nums[i]]` 时，`dp[i-1][j]` 已经先于它更新了。
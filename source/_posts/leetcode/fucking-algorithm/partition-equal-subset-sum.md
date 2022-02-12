---
title: 分割等和子集
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
date: 2022-02-09 14:43:31
---

# 分割等和子集

https://leetcode-cn.com/problems/partition-equal-subset-sum/

此问题可以转换为背包问题。首先对数组求和然后除以2，得出单个子集的总和 `sum/2`，显然问题就是要从数组中选出几个数使得总和为 `sum/2`。就类似于背包问题，在这里只需要把“背包”填满即可。

## 动态规划

对于背包问题，DP 表 `dp[i][w]` 表示：对于前 `i` 个物品，当前背包的容量为 `w` 时，这种情况下可以装下的最大价值是 `dp[i][w]`。

在遍历到 `i` 时，如果当前容量为 `w`，我们要么把物品 `i` 装入背包，要么不装。

+ 如果不装，则没有变化，`dp[i][w]` 继承 `dp[i-1][w]` 的值。
+ 如果装入背包，则加上装入物品的价值，并且寻找背包容量 `w` 减少后能装入（之前的物品）的最大价值，即 `dp[i-1][w - weight[i]]`。这两项加起来就是装入这个物品后的最大价值，即`dp[i][j] = value[i] + dp[i-1][w - weight[i]]`。

最后要寻求这两种情况下最大值，就是 `dp[i][w] = max(dp[i-1][w], dp[i-1][w - wt[i-1]] + val[i-1])`。

对于此题，不需要计算物品的价值，只需要知道能不能正好装满，DP 表中储存 `true` 和 `false` 即可。

```go
func canPartition(nums []int) bool {
    sum := 0
    for _, v := range nums {
        sum += v
    }
    
    if sum % 2 != 0 {
        return false
    }

    // 转换为背包问题
    n := len(nums)
    vol := int(sum / 2)
    dp := make([][]bool, n + 1)
    for i := range dp {
        dp[i] = make([]bool, vol + 1)
    }

    // base cases
    for i := 0; i <= n; i++ {
        dp[i][0] = true
    }
    for j := 1; j <= vol; j++ {
        dp[0][j] = false
    }

    for i := 1; i <= n; i++ {
        for j := 1; j <= vol; j++ {
            if j - nums[i-1] < 0 {
                // 背包容量 j 不足以装下重量为 nums[i-1] 的物品
                dp[i][j] = dp[i-1][j]
            } else {
                // 装或不装，选择能装的那个（有一个选择为 true 则为 true）
                dp[i][j] = dp[i-1][j] || dp[i-1][j-nums[i-1]]
            }
        }
    }
    return dp[n][vol]
}
```

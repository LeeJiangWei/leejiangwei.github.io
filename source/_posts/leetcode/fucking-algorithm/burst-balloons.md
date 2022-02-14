---
title: 戳气球
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-02-14 21:55:31
---

# 戳气球

https://leetcode-cn.com/problems/burst-balloons/

## 动态规划

DP 表定义：戳破 `(i, j)` 之间的气球（不包括 `i` 和 `j`）能获得的最大分数为 `dp[i][j]`。按这个定义，要求的是 `dp[0][n + 1]` （已经在两端加入了分数为 1 的虚拟气球）。

反向思考，假设最后一个戳破的气球为 `k`，则最后一个戳破 `k`，则先要把 `i` 到 `k` 之间和 `k` 到 `j` 之间的气球戳破，破了以后香菱的气球就是正好是 `i` 跟 `j`。而这两个区间的气球戳破后的最大分数就是 `dp[i][k]` 和 `dp[k][j]`。

最后画出 DP 表，由于计算 `dp[i][j]` 时要算出所有的 `dp[i][k]` 和 `dp[k][j]` (`i < k < j`)，需要按图示两种顺序遍历，这里选择后面的横向遍历。

![DP 表的遍历顺序](https://github.com/labuladong/fucking-algorithm/raw/master/pictures/%E6%9C%80%E9%95%BF%E5%9B%9E%E6%96%87%E5%AD%90%E5%BA%8F%E5%88%97/5.jpg)


```go
func maxCoins(nums []int) int {
    n := len(nums)

    // 加入两个虚拟气球
    points := make([]int, n + 2)
    points[0], points[n + 1] = 1, 1
    for i := 1; i <= n; i++ {
        points[i] = nums[i-1]
    }

    dp := make([][]int, n + 2)
    for i := range dp {
        dp[i] = make([]int, n + 2)
    }

    // i 和 j 的遍历顺序根据 DP 表图示
    for i := n; i >= 0; i-- {
        for j := i + 1; j <= n + 1; j++ {
            // 最后戳破哪个气球，都尝试一遍
            for k := i + 1; k < j; k++ {
                dp[i][j] = max(dp[i][j], dp[i][k] + dp[k][j] + points[i]*points[k]*points[j])
            }
        }
    }
    
    return dp[0][n+1]
}

func max(values ...int) int {
    res := values[0]
    for _, v := range values {
        if v > res {
            res = v
        }
    }
    return res
}
```
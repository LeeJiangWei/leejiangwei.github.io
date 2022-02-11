---
title: 最大子数组和
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

# 最大子数组和

https://leetcode-cn.com/problems/maximum-subarray/

## 动态规划

构建 DP 数组：`dp[i]` 存储以 `nums[i]` 为结尾的最大子数组和，不用关心这个数组是从哪里开始的。如果 `nums[i-1]` 已被计算出，则对于 `nums[i]` 只有 2 种情况：要么连接上前面的子数组，要么自己成为一个子数组。取最大的即可。最后遍历 DP 数组，得到最大子序列长。

base case: 第一个元素前面没数组了，以它结尾的最大和就是它自己。

```go
func maxSubArray(nums []int) int {
    dp := make([]int, len(nums))

    // base case
    dp[0] = nums[0]

    // dp
    for i := 1; i < len(nums); i++ {
        dp[i] = max(dp[i-1] + nums[i], nums[i])
    }

    res := math.MinInt16
    for _, v := range dp {
        res = max(res, v)
    }
    return res
}

func max(a int, b int) int {
    if a > b {
        return a
    }
    return b
}
```

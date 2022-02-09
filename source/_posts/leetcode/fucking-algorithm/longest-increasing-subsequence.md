---
title: 最长递增子序列
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-02-09 19:13:35
---

# 最长递增子序列

https://leetcode-cn.com/problems/longest-increasing-subsequence/

## 动态规划

直接构建 DP 数组：
`dp[i]` 表示以 `nums[i]` 结尾的最长递增子序列长度。问题的答案就是找到这个数组中最大的元素。

知道了 `nums[5] = 3`，既然是递增子序列，我们只要找到前面那些结尾比 3 小的子序列，然后把 3 接到最后，就可以形成一个新的递增子序列，而且这个新的子序列长度加一。

```go
func lengthOfLIS(nums []int) int {
    // init
    dp := make([]int, len(nums))

    // base case
    for i, _ := range dp {
        dp[i] = 1
    }

    for i := 0; i < len(dp); i++ {
        for j := 0; j < i; j++ {
            if nums[j] < nums[i] {
                dp[i] = max(dp[i], 1 + dp[j]) 
            }
        }
    }
    
    // find biggest element
    res := 0
    for _, v := range dp {
        res = max(res, v)
    }
    return res
}

func max (a int, b int) int {
    if a > b {
        return a
    }
    return b
}
```

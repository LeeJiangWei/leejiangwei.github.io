---
title: 跳跃游戏 II
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
  - 贪心算法
index_img: /img/code.jpg
sticky: -10
date: 2022-02-15 15:06:00
---

# 跳跃游戏 II

https://leetcode-cn.com/problems/jump-game-ii/

## 贪心算法

一开始能挑到的最远范围设为 `end`。在能跳到的范围内，选择那个下一步能跳最远的点，更新 `end` 为最远距离，跳跃数加一，且在这个新的范围内寻找下一步能跳最远的。

```go
func jump(nums []int) int {
    n := len(nums)
    end, farthest := 0, 0
    count := 0

    for i := 0; i < n - 1; i++ {
        farthest = max(farthest, i + nums[i])
        // 尝试过区间 end 为止所有的跳法后，选择最远的跳，更新能跳到的最远终点，跳跃数 + 1
        if i == end {
            count++
            end = farthest
        }
    }
    return count
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
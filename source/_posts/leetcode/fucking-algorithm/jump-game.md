---
title: 跳跃游戏
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
date: 2022-02-15 14:48:00
---

# 跳跃游戏

https://leetcode-cn.com/problems/jump-game/

## 贪心算法

在每一个位置都尝试跳到最远，最终结果一直取全局最远。

```go
func canJump(nums []int) bool {
    n := len(nums)
    farthest := 0
    for i := 0; i < n - 1; i++ {
        // 判断在每一个位置上能跳到的最远距离
        farthest = max(farthest, i + nums[i])

        // 跳跃后，最远距离等于当前位置，说明这个位置上遇到了 0，跳不动了
        // 且这个位置不是最终的位置 (i != n - 1)
        if farthest <= i {
            return false
        }
    }

    // 最远能否到最后一个位置
    return farthest >= n - 1
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
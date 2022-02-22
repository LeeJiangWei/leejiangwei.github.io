---
title: 分割数组的最大值
categories:
  - LeetCode
  - Fucking
  - 数组
tags:
  - Go
  - 数组
index_img: /img/code.jpg
sticky: -10
date: 2022-02-22 16:43:00
---

# 分割数组的最大值

https://leetcode-cn.com/problems/split-array-largest-sum/

## 二分搜索

重新抽象问题，假设每个小数组和的最大值为 `max` 时，能把数组分割成 n 个，问题就变成了搜索一个最小的 `max`，使得能把数组分割成 m 个。 

```go
func splitArray(nums []int, m int) int {
    left, right := max(nums...), sum(nums...)

    for left <= right {
        mid := left + (right - left) / 2
        midValue := split(nums, mid)
        if midValue < m {
            right = mid - 1
        } else if midValue > m {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }
    return left
}

// 最大值为 max 时能分出的数组个数为 split(nums, max)
func split(nums []int, max int) int {
    count := 0
    remains := 0
    for i := 0; i < len(nums); i++ {
        if remains - nums[i] < 0 {
            remains = max
            count++
        }
        remains -= nums[i]
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

func sum(values ...int) int {
    res := 0
    for _, v := range values {
        res += v
    }
    return res
}
```
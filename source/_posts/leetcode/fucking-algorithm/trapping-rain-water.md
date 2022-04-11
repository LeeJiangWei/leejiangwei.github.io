---
title: 接雨水
categories:
  - LeetCode
  - Fucking
  - 数组
tags:
  - Go
  - 数组
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-04-11 14:10:00
---

# 接雨水

https://leetcode-cn.com/problems/trapping-rain-water/

## 暴力法

一个格子 `i` 能接多少水，取决于它左右两边最高的柱子的最小值，减去它自己的高度 `height[i]`。可以对于每个格子 `i` 都寻找它左右两边最高柱子，复杂度为 O(n^2)。

## 动态规划

可以利用动态规划预先记录每个格子左边以及右边的最高长度。

具体地，用两个数组记录，`leftMax[i]` 表示第 `[0..i]` 的最高柱子高度（包括了 `i` 自己），base case 就是 `leftMax[0] = height[0]`。右边的最高高度同理，算出这两个数组后，计算结果仅需 O(n) 复杂度。

```go
func trap(height []int) int {
    n := len(height)
    leftMax := make([]int, n)
    rightMax := make([]int, n)

    leftMax[0] = height[0]
    rightMax[n-1] = height[n-1]

    for i := 1; i < n; i++ {
        leftMax[i] = max(height[i], leftMax[i-1])
    }
    for j := n-2; j >= 0; j-- {
        rightMax[j] = max(height[j], rightMax[j+1])
    }

    res := 0
    for i := 0; i < n; i++ {
        res += min(leftMax[i], rightMax[i]) - height[i]        
    }

    return res
}
```

## 双指针

利用两个指针 `left` 和 `right` 从左右两边向中间靠近并计算 `height[left]` 或 `height[right]` 能装的雨水，同时更新左侧和右侧的最高值。

由于已经判断了 `leftMax < rightMax`，因此直接取其中更小者即可，而不用关心另一侧是不是没找到最高的柱子。

```go
func trap(height []int) int {
    n := len(height)

    leftMax, rightMax := 0, 0
    left, right := 0, n-1
    res := 0

    for left < right {
        leftMax = max(leftMax, height[left])
        rightMax = max(rightMax, height[right])

        if leftMax < rightMax {
            res += leftMax - height[left]
            left++
        } else {
            res += rightMax - height[right]
            right--
        }
    }

    return res
}
```

## 盛最多水的容器

https://leetcode-cn.com/problems/container-with-most-water/

类似的道理，利用双指针解决，用 `left` 和 `right` 两个指针从两端向中心收缩，一边收缩一边计算 `[left, right]` 之间的矩形面积，取最大的面积值即是答案。

```go
func maxArea(height []int) int {
    n := len(height)
    var leftMax, rightMax int
    left, right := 0, n-1

    res := 0
    for left < right {
        leftMax = max(leftMax, height[left])
        rightMax = max(rightMax, height[right])

        var curr int
        if leftMax < rightMax {
            curr = leftMax * (right - left)
            left++
        } else {
            curr = rightMax * (right - left)
            right--
        }

        if curr > res {
            res = curr
        }
    }

    return res
}
```
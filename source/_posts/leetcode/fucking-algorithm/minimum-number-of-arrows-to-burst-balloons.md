---
title: 用最少数量的箭引爆气球
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
date: 2022-02-15 14:25:32
---

# 用最少数量的箭引爆气球

https://leetcode-cn.com/problems/minimum-number-of-arrows-to-burst-balloons/

## 贪心算法

此题等价于找出最多有多少个不重叠区间，每一个不重叠区间都需要一支箭来射爆。

```go
func findMinArrowShots(points [][]int) int {
    sort.Slice(points, func(i, j int) bool {
        return points[i][1] < points[j][1]
    })

    count := 1
    end := points[0][1]
    for _, point := range points {
        if point[0] > end {
            count++
            end = point[1]
        }
    }

    return count
}
```
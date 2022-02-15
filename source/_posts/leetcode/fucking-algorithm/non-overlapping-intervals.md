---
title: 无重叠区间
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
  - 贪心算法
  - 区间调度问题
index_img: /img/code.jpg
sticky: -10
date: 2022-02-15 14:18:32
---

# 无重叠区间

https://leetcode-cn.com/problems/non-overlapping-intervals/

## 贪心算法

按照区间终点从小到大排序，从最小终点的区间开始依次选择不重叠区间。最终得到最多有多少个不重叠的区间，用总区间数目减去最多不重叠的数量，就是要求的删除区间的个数。

```go
func eraseOverlapIntervals(intervals [][]int) int {
    sort.Slice(intervals, func(i, j int) bool {
        return intervals[i][1] < intervals[j][1]
    })

    count := 1
    x_end := intervals[0][1]
    for _, interval := range intervals {
        if interval[0] >= x_end {
            count++
            x_end = interval[1]
        }
    }

    return len(intervals) - count
}
```
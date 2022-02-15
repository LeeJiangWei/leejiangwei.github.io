---
title: 合并区间
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
date: 2022-02-15 21:48:00
---

# 合并区间

https://leetcode-cn.com/problems/merge-intervals/

## 贪心算法

跟上一题几乎一样，依照惯例，按区间**起点升序**对区间排序，如果起点相同则按**终点降序**排序。

![](https://labuladong.gitee.io/algo/images/mergeInterval/1.jpg)

排序后，香菱区间的相对关系只需要考虑 2 种情况：

1. 区间相交，可以视作合并为一个更大的区间
2. 区间不相交

```go
func merge(intervals [][]int) [][]int {
    sort.Slice(intervals, func(i, j int) bool {
        if intervals[i][0] == intervals[j][0] {
            return intervals[i][1] > intervals[j][1]
        } else {
            return intervals[i][0] < intervals[j][0]
        }
    })

    result := make([][]int, 0)
    
    left, right := intervals[0][0], intervals[0][1]

    for i := 1; i < len(intervals); i++ {
        if intervals[i][0] <= right && intervals[i][1] > right {
            // 区间相交，更新合并
            right = intervals[i][1]
        } else if intervals[i][0] > right {
            // 区间完全不相交，记录上一个大区间，更新下一个区间的开始结束
            result = append(result, []int{left, right})

            left = intervals[i][0]
            right = intervals[i][1]
        }
    }
    // 记录最后一个区间
    result = append(result, []int{left, right})

    return result
}
```
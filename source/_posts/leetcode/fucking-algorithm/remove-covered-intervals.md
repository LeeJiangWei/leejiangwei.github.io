---
title: 删除被覆盖区间
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
  - 区间调度问题
index_img: /img/code.jpg
sticky: -10
date: 2022-02-15 21:48:00
---

# 删除被覆盖区间

https://leetcode-cn.com/problems/remove-covered-intervals/

## 排序并观察

依照惯例，按区间**起点升序**对区间排序，如果起点相同则按**终点降序**排序。

![](https://labuladong.gitee.io/algo/images/mergeInterval/1.jpg)

排序后，香菱区间的相对关系只有 3 个可能：

![](https://labuladong.gitee.io/algo/images/%e5%8c%ba%e9%97%b4%e5%90%88%e9%9b%86/1.jpeg)

1. 找到了覆盖的区间
2. 区间相交，可以视作合并为一个更大的区间
3. 区间不相交

```go
func removeCoveredIntervals(intervals [][]int) int {
    sort.Slice(intervals, func(i, j int) bool {
        if intervals[i][0] == intervals[j][0] {
            return intervals[i][1] > intervals[j][1]
        } else {
            return intervals[i][0] < intervals[j][0]
        }
    })

    left, right := intervals[0][0], intervals[0][1]
    count := 0

    for i := 1; i < len(intervals); i++ {
        // 找到覆盖区间
        if intervals[i][0] >= left && intervals[i][1] <= right {
            count++
        }

        // 区间相交
        if intervals[i][0] >= left && intervals[i][1] > right {
            right = intervals[i][1]
        }

        // 区间不相交，更新起点终点
        if intervals[i][0] > right {
            left = intervals[i][0]
            right = intervals[i][1]
        }
    }

    return len(intervals) - count
}
```
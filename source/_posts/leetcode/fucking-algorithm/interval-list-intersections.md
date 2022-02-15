---
title: 区间列表的交集
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
date: 2022-02-15 22:43:00
---

# 区间列表的交集

https://leetcode-cn.com/problems/interval-list-intersections/

## 观察规律

两个区间要么有交集，要么无交集。如果无交集，则条件很明显是 `secondEnd < firstStart || firstEnd < secondStart `。

取反就是有交集的条件 `secondEnd >= firstStart && firstEnd >= secondStart`，在这种条件下观察得出重叠区间的起点是两个起点的较大者，终点是两个终点的较小者。

得出交集后，谁更靠前，就移动谁的指针，很好理解。

```go
func intervalIntersection(firstList [][]int, secondList [][]int) [][]int {
    result := make([][]int, 0)
    m, n := len(firstList), len(secondList)
    i, j := 0, 0
    
    for i < m && j < n {
        firstStart, firstEnd := firstList[i][0], firstList[i][1]
        secondStart, secondEnd := secondList[j][0], secondList[j][1]

        // 两个区间存在交集
        if secondEnd >= firstStart && firstEnd >= secondStart {
            result = append(result, []int{max(firstStart, secondStart), min(firstEnd, secondEnd)})
        }
        if secondEnd < firstEnd  {
            j++
        } else {
            i++
        }
    }
    return result
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}

func min(a, b int) int {
    if a < b {
        return a
    }
    return b
}
```
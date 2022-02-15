---
title: 视频拼接
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
date: 2022-02-15 20:55:00
---

# 视频拼接

https://leetcode-cn.com/problems/video-stitching/

## 贪心算法

这道题其实是求最少用多少个区间能覆盖完整个区间。

按视频区间**起点升序**对区间排序，如果起点相同则按**终点降序**排序。使用贪心策略，在第一个区间的终点，寻找下一个终点最长的区间，依此类推。

![](https://labuladong.gitee.io/algo/images/%e5%89%aa%e8%a7%86%e9%a2%91/4.jpeg)

```go
func videoStitching(clips [][]int, time int) int {
    sort.Slice(clips, func(i, j int) bool {
        if clips[i][0] == clips[j][0] {
            return clips[i][1] > clips[j][1]
        } else {
            return clips[i][0] < clips[j][0]
        }
    })

    n := len(clips)
    count := 0
    currEnd, nextEnd := 0, 0
    
    for i := 0; i < n && clips[i][0] <= currEnd; {
        for i < n && clips[i][0] <= currEnd {
            // 寻找区间起点在 currEnd 前、区间终点最大的区间作为下一个区间
            nextEnd = max(nextEnd, clips[i][1])
            i++
        }

        // 选择这个区间，更新 currEnd 为这个区间的终点
        count++
        currEnd = nextEnd

        // 如果终点已经到达视频终点了，则可以拼凑出完整视频
        if currEnd >= time {
            return count
        }
    }

    return -1
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
---
title: 俄罗斯套娃信封问题
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-02-11 19:27:53
---

# 俄罗斯套娃信封问题

https://leetcode-cn.com/problems/russian-doll-envelopes/

此题用 Golang DP 会超出时间限制（官方代码也是）。

## 动态规划

利用 sort 标准库，给二维切片一个类型别名，然后在此类型上定义 `sort.Sort()` 函数所需要的 3 个方法，在 `Less()` 方法种完成比较的逻辑。排序后套用 [最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/) 的解题方法。

参考：https://books.studygolang.com/The-Golang-Standard-Library-by-Example/chapter03/03.1.html

```go
func maxEnvelopes(envelopes [][]int) int { 
    sort.Sort(Envelopes(envelopes))
    
    dp := make([]int, len(envelopes))
    for i := range dp {
        dp[i] = 1
    }

    for i := range dp {
        for j := 0; j < i; j++ {
            if envelopes[j][1] < envelopes[i][1] {
                dp[i] = max(dp[i], dp[j] + 1)
            }
        }
    }

    res := math.MinInt16
    for i := range dp {
        res = max(res, dp[i])
    }
    return res
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}

type Envelopes [][]int

func (envelopes Envelopes) Len() int {
    return len(envelopes)
}

func (envelopes Envelopes) Less(i, j int) bool {
    if envelopes[i][0] < envelopes[j][0] {
        return true
    }
    if envelopes[i][0] == envelopes[j][0] && envelopes[i][1] > envelopes[j][1] {
        return true
    }
    return false
}

func (envelopes Envelopes) Swap(i, j int) {
    envelopes[i], envelopes[j] = envelopes[j], envelopes[i]
}
```

## 动态规划（官方）

使用了 `sort.Slice()` 来对任何数据结构（空接口）的切片进行排序。

```go
func maxEnvelopes(envelopes [][]int) int {
    n := len(envelopes)
    if n == 0 {
        return 0
    }

    sort.Slice(envelopes, func(i, j int) bool {
        a, b := envelopes[i], envelopes[j]
        return a[0] < b[0] || a[0] == b[0] && a[1] > b[1]
    })

    f := make([]int, n)
    for i := range f {
        f[i] = 1
    }
    for i := 1; i < n; i++ {
        for j := 0; j < i; j++ {
            if envelopes[j][1] < envelopes[i][1] {
                f[i] = max(f[i], f[j]+1)
            }
        }
    }
    return max(f...)
}

func max(a ...int) int {
    res := a[0]
    for _, v := range a[1:] {
        if v > res {
            res = v
        }
    }
    return res
}
```
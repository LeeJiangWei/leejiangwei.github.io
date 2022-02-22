---
title: 二分搜索的应用
categories:
  - LeetCode
  - Fucking
  - 数组
tags:
  - Go
  - 数组
index_img: /img/code.jpg
sticky: -10
date: 2022-02-22 12:30:00
---

# 二分搜索的应用

将问题抽象成一个关于 `x` 的**单调函数** `f(x)`，问题就是让我们求一个 `x`，使得 `f(x) == target`，就可以使用二分搜索来寻找这个 `x` 值了。通常不仅要使得 `f(x) == target`，还要使得 `x` 最大或最小，这就是用二分搜索寻找边界值的问题。

![问题的抽象](https://labuladong.gitee.io/algo/images/%e4%ba%8c%e5%88%86%e8%bf%90%e7%94%a8/3.jpeg)

## 爱吃香蕉的珂珂

https://leetcode-cn.com/problems/koko-eating-bananas/

题目让我们求吃香蕉的速度，那它就是自变量 `x`，吃香蕉所用的时间就是 `f(x)`，题目希望我们求得 `f(x) == H` 时 `x` 的最小值，就是寻找一个左边界。

把 `f(x)` 的逻辑实现出来，确定搜索区间的起点和终点，就可以用二分查找搜索了。

```go
func minEatingSpeed(piles []int, h int) int {
    // 起点：一次只吃一根香蕉 终点：一次最多吃掉最大那一堆香蕉
    left, right := 1, max(piles...)

    for left <= right {
        mid := left + (right - left) / 2
        midValue := f(mid, piles)
        if midValue < h {
            right = mid - 1
        } else if midValue > h {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }

    // 由于不是搜索数组所以不会越界，只要搜索区间合理，一定能找出结果
    return left
}

func f(x int, piles []int) int {
    hours := 0
    for i := 0; i < len(piles); i++ {
        hours += piles[i] / x
        if piles[i] % x > 0 {
            hours += 1
        }
    }
    return hours
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

## 在 D 天内送达包裹的能力

https://leetcode-cn.com/problems/capacity-to-ship-packages-within-d-days/

这题十分类似，要注意的是搜索区间，最小运载能力就是运载最大货物的能力，而最大运载能力是一次将所有货物都运走。

```go
func shipWithinDays(weights []int, days int) int {
    left, right := max(weights...), sum(weights...)

    for left <= right {
        mid := left + (right - left) / 2
        midValue := f(mid, weights)
        if midValue < days {
            right = mid - 1
        } else if midValue > days {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }

    return left
}

func f(x int, weights []int) int {
    days := 0
    remains := 0
    for i := 0; i < len(weights); i++ {
        if remains - weights[i] < 0 {
            remains = x
            days++
        }
        remains -= weights[i]
    }
    return days
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
---
title: 小而美的算法技巧：差分数组
categories:
  - LeetCode
  - Fucking
  - 数组
tags:
  - Go
  - 数组
index_img: /img/code.jpg
sticky: -10
date: 2022-02-21 15:10:00
---

# 小而美的算法技巧：差分数组

差分数组的主要适用场景是频繁对原始数组的某个区间的元素进行增减。

![](https://labuladong.gitee.io/algo/images/%e5%b7%ae%e5%88%86%e6%95%b0%e7%bb%84/2.jpeg)

差分数组每一个元素 `df[i]` 储存数组元素与上一个元素的差值 `nums[i] - nums[i-1]`。

差分数组可以直接还原为原数组，只需遍历一遍差分数组，加上上一个元素即可。

```go
for i := 1; i < n; i++ {
    df[i] = df[i-1] + df[i]
}
```

## 航班预订统计

https://leetcode-cn.com/problems/corporate-flight-bookings/

```go
func corpFlightBookings(bookings [][]int, n int) []int {
    // 差分数组初始化为全 0
    df := make([]int, n)
    for _, booking := range bookings {
        df[booking[0]-1] += booking[2]
        // 如果区间终点是数组终点，不需要修改
        if booking[1] < n {
            df[booking[1]] -= booking[2]
        }
    }
    // 还原差分数组
    for i := 1; i < n; i++ {
        df[i] = df[i-1] + df[i]
    }
    return df
}
```
## 拼车

https://leetcode-cn.com/problems/car-pooling/

和上一题基本一样，有几点注意的：
+ 题目限定了路程长度最多为 1000，所以差分数组也初始化为 1000
+ 乘客会先下后上，所有到达终点时车的容量就会马上增加，因此在终点区间就可以减去人数
+ 注意起点 0 时可能上了多个乘客但是没判断到

```go
func carPooling(trips [][]int, capacity int) bool {
    // 差分数组初始化为全 0
    n := 1000
    df := make([]int, n)
    for _, trip := range trips {
        df[trip[1]] += trip[0]
        // 如果区间终点是数组终点，不需要修改
        if trip[2] < n {
            df[trip[2]] -= trip[0]
        }
    }
    if df[0] > capacity {
        return false
    }
    // 还原差分数组
    for i := 1; i < n; i++ {
        df[i] = df[i-1] + df[i]
        if df[i] > capacity {
            return false
        }
    }
    return true
}
```
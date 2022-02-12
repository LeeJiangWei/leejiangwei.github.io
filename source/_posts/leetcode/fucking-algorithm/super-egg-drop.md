---
title: 鸡蛋掉落
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-02-12 20:12:31
---

# 鸡蛋掉落

https://leetcode-cn.com/problems/super-egg-drop/

## 带备忘录的递归

**状态**：剩余鸡蛋数量 `eggs`，剩余未测楼层数 `floors`。随着鸡蛋数量的减少，剩下的楼层数就会减少。

**状态转移**：假设当前剩余 `eggs` 个鸡蛋和 `floors` 层楼，去第 `i` 层扔一个鸡蛋，会有 2 种情况：
+ 如果鸡蛋没碎，可以排除这层楼及以下的楼层，剩余楼层数为 `floors - i`；
+ 如果鸡蛋碎了，则排除这层楼及以上的楼层，剩余楼层数为 `i - 1`，且鸡蛋少了一个。

![状态转移](https://github.com/labuladong/fucking-algorithm/raw/master/pictures/%E6%89%94%E9%B8%A1%E8%9B%8B/1.jpg)

注意，只需要关注剩下几层楼没测，而不需要关注这层楼具体是多高，因为对于每层楼我们最终都会假设它碎或不碎两种情况，相当于是把所有可能性都尝试了一次。

`i` 怎么取？也就是说去哪层开始扔？答案是都试一次，扔完一个后楼层减少，继续用一样的方法尝试。关注**最坏情况**下*最少的操作数*，最坏情况就是扔了一次鸡蛋后剩余的楼层数最多，最少的操作数就是从每个不同的楼层 `i` 开始扔，哪个用的次数最少。即:

```python
res = 0
for 1 <= i <= floors:
    res = min(res, 1 + max(
        dp(eggs, floors-i),  # 鸡蛋没碎，剩余上面的楼层
        dp(eggs-1, i-1),     # 鸡蛋碎了，剩余下面的楼层，鸡蛋减一
    ))
```

最终用备忘录减少重复计算，代码写出来就是：

```go
func superEggDrop(k int, n int) int {
    memo := make(map[[2]int]int)
    return dp(memo, k, n)
}

func dp(memo map[[2]int]int, eggs int, floors int) int {
    // base case:
    if floors == 0 {
        return 0
    }
    if eggs == 1 {
        return floors
    }

    if v, ok := memo[[2]int{eggs, floors}]; ok {
        return v
    }

    times := math.MaxInt16
    for i := 1; i <= floors; i++ {
        times = min(times, max(dp(memo, eggs-1, i-1), dp(memo, eggs, floors-i)) + 1)
    }

    memo[[2]int{eggs, floors}] = times
    return times
}

func min(values ...int) int {
    res := values[0]
    for _, v := range values {
        if v < res {
            res = v
        }
    }
    return res
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

但是 leetcode 会超时。

## 用二分搜索优化

上面的方法有个致命的缺点，就是每次搜索都是线性搜索，且都是从 1 开始到 N，这效率并不够高，leetcode 题直接超时了。需要改进上面的线性搜索，也就是这一部分：

```go
for i := 1; i <= floors; i++ {
    times = min(times, max(dp(memo, eggs-1, i-1), dp(memo, eggs, floors-i)) + 1)
}
```

首先我们根据 `dp(K, N)` 数组的定义（有 K 个鸡蛋面对 N 层楼，最少需要扔几次），很容易知道 `K` 固定时，这个函数一定是单调递增的，无论你策略多聪明，随着总楼层增加，测试次数一定要增加。

那么注意 `dp(K - 1, i - 1)` 和 `dp(K, N - i)` 这两个函数，其中 i 是从 1 到 N 单增的，如果我们固定 `K` 和 `N`，把这两个函数看做关于 `i` 的函数，前者随着 `i` 的增加应该也是单调递增的，而后者随着 `i` 的增加应该是单调递减的：

![函数图像示意](https://github.com/labuladong/fucking-algorithm/raw/master/pictures/%E6%89%94%E9%B8%A1%E8%9B%8B/2.jpg)

```go
func superEggDrop(k int, n int) int {
    memo := make(map[[2]int]int)
    return dp(memo, k, n)
}

func dp(memo map[[2]int]int, eggs int, floors int) int {
    // base case:
    if floors == 0 {
        return 0
    }
    if eggs == 1 {
        return floors
    }

    if v, ok := memo[[2]int{eggs, floors}]; ok {
        return v
    }

    times := math.MaxInt16

    // 改进后的二分搜索
    low, high := 1, floors
    for low <= high {
        mid := (low + high) / 2

        brokenTimes := dp(memo, eggs-1, mid-1)      // 蛋碎了
        noBrokenTimes := dp(memo, eggs, floors-mid) // 蛋没碎

        if brokenTimes > noBrokenTimes {
            high = mid - 1
            times = min(times, brokenTimes + 1)
        } else {
            low = mid + 1
            times = min(times, noBrokenTimes + 1)
        }
    }

    memo[[2]int{eggs, floors}] = times
    return times
}

func min(values ...int) int {
    res := values[0]
    for _, v := range values {
        if v < res {
            res = v
        }
    }
    return res
}
```

此时 leetcode 终于不超时了。

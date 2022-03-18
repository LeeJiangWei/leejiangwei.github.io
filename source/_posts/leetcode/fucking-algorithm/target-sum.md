---
title: 小而美的算法技巧：差分数组
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
  - 回溯法
index_img: /img/code.jpg
sticky: -10
date: 2022-03-18 15:19:00
---

# 目标和

https://leetcode-cn.com/problems/target-sum/

https://labuladong.gitee.io/algo/3/24/75/

## 回溯法

很容易想到最简单的回溯法，就是在每一个位置决定是加法还是减法。

其中变量 `track` 表示当前回溯路径（当前已经积累的和）。

```go
var res int

func findTargetSumWays(nums []int, target int) int {
    res = 0
    backtrack(nums, 0, 0, target)
    return res
}

func backtrack(nums []int, i, track, target int) {
    n := len(nums)
    if i == n {
        if track == target {
            res++
        }
        return
    }

    backtrack(nums, i + 1, track - nums[i], target)
    backtrack(nums, i + 1, track + nums[i], target)
    return
}
```

## 备忘录优化

简单回溯，是具有重复子问题的。是否存在重复子问题，要关注递归函数中会变的参数 `i` 和 `track`。看这一段：

```go
    backtrack(nums, i + 1, track - nums[i], target)
    backtrack(nums, i + 1, track + nums[i], target)
```

当 `nums[i]` 为 0 时，显然是两个重复的函数调用。因此用备忘录记录会变的参数对 `i, track`，速度明显提升。

如果使用备忘录，需要让函数返回一个值。这里当回溯到终点且达到目标是，返回一个 1，表面这是一种可行的解法，否则返回 0。递归时将子问题加起来。

```go
var memo map[[2]int]int


func findTargetSumWays(nums []int, target int) int {
    memo = make(map[[2]int]int)
    return backtrack(nums, 0, 0, target)
}

func backtrack(nums []int, i, track, target int) int {
    n := len(nums)
    if i == n {
        if track == target {
            return 1
        }
        return 0
    }

    if v, ok := memo[[2]int{i, track}]; ok {
        return v
    }

    res := backtrack(nums, i + 1, track - nums[i], target) + backtrack(nums, i + 1, track + nums[i], target)
    memo[[2]int{i, track}] = res
    return res
}
```

## 动态规划

此问题可以转换成背包问题，类似 [分割等和子集](https://leetcode-cn.com/problems/partition-equal-subset-sum/)，然后用动态规划来解决。

首先，如果我们把 `nums` 划分成两个子集 A 和 B，分别代表分配 + 的数和分配 - 的数，那么他们和 target 存在如下关系：

```
sum(A) - sum(B) = target
=>  sum(A) = target + sum(B)
=>  sum(A) + sum(A) = target + sum(B) + sum(A)
=>  2 * sum(A) = target + sum(nums)
=>  sum(A) = (target + sum(nums)) / 2
```

即：需要在 `nums` 中找到一个子集 A ，使得它的和为 `(target + sum(nums)) / 2`。需要求一共有多少个这样的子集。

定义 `dp[i][j]` 为前 i 个物品，当前背包容量为 j，有 `dp[i][j]` 种凑法使得子集的和为 `(target + sum(nums)) / 2`。

base case： `dp[0][0]` 为 1，因为当只有 0 个物品且背包容量为 0，什么都不干就是一个这样的子集。

```go
func findTargetSumWays(nums []int, target int) int {
    sum := 0
    for _, n := range nums {
        sum += n
    }

    if sum < abs(target) || (target + sum) % 2 != 0 {
        return 0
    }
    vol := (target + sum) / 2

    return subsets(nums, vol)
}

func subsets(nums []int, target int) int {
    n := len(nums)

    dp := make([][]int, n+1)
    for i := range dp {
        dp[i] = make([]int, target+1)
    }

    dp[0][0] = 1

    for i := 1; i <= n; i++ {
        for j := 0; j <= target; j++ {
            if j - nums[i-1] >= 0 {
                dp[i][j] = dp[i-1][j] + dp[i-1][j-nums[i-1]]
            } else {
                dp[i][j] = dp[i-1][j]
            }
        }
    }

    return dp[n][target]
}

func abs(a int) int {
    if a < 0 {
        return -a
    }
    return a
}
```
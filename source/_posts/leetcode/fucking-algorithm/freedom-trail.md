---
title: 自由之路
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-02-14 15:14:31
---

# 自由之路

https://leetcode-cn.com/problems/freedom-trail/

## 带备忘录的递归

DP 函数定义：当圆盘指针指向 `ring[i]` 时，输入字符串 `key[j..]` 至少需要 `dp(ring, i, key, j)` 次操作。按照这个定义就是求 `dp(ring, 0, key, 0)`。

一个字符可能出现在圆盘上多次，拨动到哪一个上面，答案是都试一次。顺时针还是逆时针拨，判断哪个操作数更少。

```go
var memo [][]int
var charToIndex map[rune][]int

func findRotateSteps(ring string, key string) int {
    m, n := len(ring), len(key)
    memo = make([][]int, m)
    for i := range memo {
        memo[i] = make([]int, n)
    }
    charToIndex = make(map[rune][]int)
    // 记录盘上每一个字符的位置
    for i, v := range ring {
        charToIndex[v] = append(charToIndex[v], i)
    }
    return dp(ring, 0, key, 0)
}

func dp(ring string, i int, key string, j int) int {
    // base case: 已经完成输入
    if j == len(key) {
        return 0
    }
    if memo[i][j] != 0 {
        return memo[i][j]
    }
    
    m := len(ring)
    res := math.MaxInt16

    // 尝试拨动指针到当前字母 key[j] 在转盘上的每一个位置 k
    for _, k := range charToIndex[rune(key[j])] {
        delta := abs(k - i)  // 计算拨动指针的次数
        delta = min(delta, m - delta)  // 顺时针和逆时针哪个近

        subProblem := dp(ring, k, key, j + 1)  // 拨动后当前位置是 k，继续输入
        res = min(res, 1 + delta + subProblem)
    }

    memo[i][j] = res
    return res
}

func abs(a int) int {
    if a >= 0 {
        return a
    } else {
        return -a
    }
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
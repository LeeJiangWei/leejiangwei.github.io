---
title: 正则表达式匹配
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
  - 字符串
index_img: /img/code.jpg
sticky: -10
date: 2022-02-14 21:29:31
---

# 正则表达式匹配

https://leetcode-cn.com/problems/regular-expression-matching/

## 带备忘录的递归

DP 函数定义：若 `dp(s, i, p, j) = true`，则表示 `s[i..]` 可以匹配 `p[j..]`；若 `dp(s, i, p, j) = false`，则表示 `s[i..]` 无法匹配 `p[j..]`。

由于 `*` 通配符可以匹配 0 个或任意个字符，因此每一次匹配 `s[i]` 和 `p[j]` 时，都要考虑 `p[j]` 下一个会不会是 `*`，然后做特殊处理。

```go
var memo map[[2]int]bool

func isMatch(s string, p string) bool {
    memo = make(map[[2]int]bool)
    return dp(s, 0, p, 0)
}

func dp(s string, i int, p string, j int) bool {
    // base case: 模式匹配完，如果字符串也匹配完，则成功
    if j == len(p) {
        return i == len(s)
    }
    // base case: 字符串匹配完，模式要么匹配完，要么是 a*b*c* 的形式（因此剩下长度必须是偶数）
    if i == len(s) {
        if (len(p) - j) % 2 == 1 {
            return false
        }
        for ; j + 1 < len(p); j += 2 {
            if p[j+1] != '*' {
                return false
            }
        }
        return true
    }

    if v, ok := memo[[2]int{i, j}]; ok {
        return v
    }

    if s[i] == p[j] || p[j] == '.' {
        // 匹配
        if j + 1 < len(p) && p[j+1] == '*' {
            // 下一个模式是 * 通配符
            res := dp(s, i, p, j+2) || dp(s, i+1, p, j) // 匹配 0 次或多次
            memo[[2]int{i, j}] = res
            return res
        } else {
            res := dp(s, i+1, p, j+1)
            memo[[2]int{i, j}] = res
            return res
        }
    } else {
        // 不匹配
        if j + 1 < len(p) && p[j+1] == '*' {
            // 下一个模式是 * 通配符
            res := dp(s, i, p, j+2) // 匹配当前字符 0 次
            memo[[2]int{i, j}] = res
            return res
        } else {
            res := false
            memo[[2]int{i, j}] = res
            return res
        }
    }
}
```
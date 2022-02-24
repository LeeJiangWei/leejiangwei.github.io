---
title: 括号生成
categories:
  - LeetCode
  - Fucking
  - 暴力搜索算法
tags:
  - Go
index_img: /img/code.jpg
sticky: -10
date: 2022-02-24 19:25:44
---

# 括号生成

https://leetcode-cn.com/problems/generate-parentheses/

括号问题的两个重要性质：

1.  一个「合法」括号组合的左括号数量一定等于右括号数量.   
2. 对于一个「合法」的括号字符串组合 `p`，必然对于任何 `0 <= i < len(p)` 都有：子串 `p[0..i]` 中**左括号的数量都大于或等于右括号**的数量。

这一题用两个参数 `left` 和 `right` 分别记录剩余可使用的左右括号数量，同时为 0 则表示合法组合。

```go
var res []string

func generateParenthesis(n int) []string {
    res = make([]string, 0)
    backtrack(n, n, "")
    return res
}

func backtrack(left, right int, track string) {
    // 在某个子序列中，右括号比左括号多（剩余的右括号更少），不合法
    if right < left {
        return
    }
    // 括号数量小于 0，不合法
    if left < 0 || right < 0 {
        return
    }
    // 同时用完所有的括号，合法
    if left == 0 && right == 0 {
        res = append(res, track)
    }

    backtrack(left - 1, right, track + "(")
    backtrack(left, right - 1, track + ")")
}
```

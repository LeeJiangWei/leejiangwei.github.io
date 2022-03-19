---
title: 分治法
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 分治法
index_img: /img/code.jpg
sticky: -10
date: 2022-03-18 15:19:00
---

# 分治法

https://labuladong.gitee.io/algo/4/32/127/

## 为运算表达式设计优先级

https://leetcode-cn.com/problems/different-ways-to-add-parentheses/

```go
func diffWaysToCompute(expression string) []int {
    res := make([]int, 0)

    // 遍历字符串，如果是运算符就【分】
    for i := 0; i < len(expression); i++ {
        c := expression[i]
        if c == '+' || c == '-' || c == '*' {
            // 【分】成左右两边计算可能出现的结果
            left := diffWaysToCompute(expression[:i])
            right := diffWaysToCompute(expression[i+1:])

            // 【治】根据返回的结果，暴力计算出所有可能的值
            for _, a := range left {
                for _, b := range right {
                    switch c {
                        case '+':
                            res = append(res, a + b)
                        case '-':
                            res = append(res, a - b)
                        case '*':
                            res = append(res, a * b)
                    }
                }
            }
        }
    }

    // base case: expression 不包含运算符，它只是一个数字
    if len(res) == 0 {
        n, _ := strconv.Atoi(expression)
        res = append(res, n)
    }

    return res
}
```

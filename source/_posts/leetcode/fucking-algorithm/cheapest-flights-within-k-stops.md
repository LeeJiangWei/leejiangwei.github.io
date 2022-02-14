---
title: K 站中转内最便宜的航班
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-02-14 20:36:31
---

# K 站中转内最便宜的航班

https://leetcode-cn.com/problems/cheapest-flights-within-k-stops/

## 带备忘录的递归

DP 函数定义：从起点 `src` 出发，`k` 步之内（一步就是一条边）到达节点 `s` 的最小路径权重为 `dp(s, k)`。

```go
var SRC, DST int
var memo [][]int
var indegree map[int][][]int

func findCheapestPrice(n int, flights [][]int, src int, dst int, k int) int {
    SRC, DST = src, dst

    memo = make([][]int, n)
    for i := range memo {
        memo[i] = make([]int, k+2)
        for j := range memo[i] {
            memo[i][j] = -100
        }
    }

    // 记录每个节点的入节点以及价格
    indegree = make(map[int][][]int)
    for _, flight := range flights {
        indegree[flight[1]] = append(indegree[flight[1]], []int{flight[0], flight[2]})
    }


    return dp(dst, k+1)
}

func dp(s int, k int) int {
    // base case：到达起点
    if s == SRC {
        return 0
    }
    // base case：达到次数限制
    if k == 0 {
        return -1
    }
    if memo[s][k] != -100 {
        return memo[s][k]
    }

    res := math.MaxInt16

    if inNodes, ok := indegree[s]; ok {
        for _, node := range inNodes {
            from, price := node[0], node[1]
            subProblem := dp(from, k - 1) // 追寻到达前一个节点的最低代价，次数限制减少
            if subProblem != -1 {
                res = min(res, subProblem + price)
            }
        }
    }
    if res == math.MaxInt16 {
        return -1
    }
    memo[s][k] = res
    return res
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
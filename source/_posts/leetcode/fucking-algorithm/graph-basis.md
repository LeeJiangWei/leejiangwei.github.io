---
title: 图论基础
categories:
  - LeetCode
  - Fucking
  - 图
tags:
  - Go
  - 图
index_img: /img/code.jpg
sticky: -10
date: 2022-03-21 14:55:00
---

# 图论基础

https://labuladong.gitee.io/algo/2/20/38/

图遍历框架：

需要在函数内将自己当前节点加入 `path`，否则根节点不会被记录。

```go
visited []int
path    []int

func traverse(graph [][]int, curr int) {
    if visited[curr] {
        return
    }

    visited[curr] = true

    path[curr] = true  // 标记当前节点为路径
    for _, next := range graph[curr] {
        traverse(graph, next)
    }
    path[curr] = false // 退出节点后删除路径标记
}
```

## 所有可能的路径 

https://leetcode-cn.com/problems/all-paths-from-source-to-target/

```go
var res [][]int

func allPathsSourceTarget(graph [][]int) [][]int {
    res = make([][]int, 0)
    traverse(graph, 0, []int{})
    return res
}

func traverse(graph [][]int, curr int, path []int) {
    path = append(path, curr)

    if curr == len(graph) - 1 {
        r := make([]int, len(path))
        copy(r, path)
        res = append(res, r)
        return
    }

    for _, next := range graph[curr] {
        traverse(graph, next, path)
    }
    path = path[:len(path)-1]
}
```

---
title: 二分图
categories:
  - LeetCode
  - Fucking
  - 图
tags:
  - Go
  - 图
index_img: /img/code.jpg
sticky: -10
date: 2022-03-21 17:00:00
---

# 二分图

https://labuladong.gitee.io/algo/2/20/40/

二分图定义：如果能将一个图的节点集合分割成两个独立的子集 A 和 B ，并使图中的每一条边的两个节点一个来自 A 集合，一个来自 B 集合，就将这个图称为 **二分图** 。

经典二分图问题：双色问题

> 给你一幅图，请你用两种颜色将图中的所有顶点着色，且使得任意一条边的两个端点的颜色都不相同。

这个问题等价于判断二分图。如果一个图是二分图，那他就可以用这种方式染色。

## 判定二分图

https://leetcode-cn.com/problems/is-graph-bipartite/

利用双色问题的思想，遍历图尝试染色。如果发现相邻节点已被染色且颜色和自己相同，就说明不是二分图。

由于图不一定是联通的，可能存在多个子图，所以要对每一个节点都作为起点开始遍历。如果任意一个子图不是二分图，整幅图也不是二分图。

### DFS

```go
var validBipartite bool

var color   []bool
var visited []bool

func isBipartite(graph [][]int) bool {
    validBipartite = true
    
    n := len(graph)
    color = make([]bool, n)
    visited = make([]bool, n)

    for i := 0; i < n; i++ {
        if !visited[i] {
            traverse(graph, i)
        }
    }
    return validBipartite
}

func traverse(graph [][]int, curr int) {
    if !validBipartite {
        return
    }

    visited[curr] = true

    for _, next := range graph[curr] {
        if !visited[next] {
            color[next] = !color[curr]
            traverse(graph, next)
        } else {
            if color[next] == color[curr] {
                validBipartite = false
            }
        }
    }
}
```

### BFS

和 DFS 几乎是一样的，只是用队列来维护接下来访问的图节点。

```go
var validBipartite bool

var color   []bool
var visited []bool

func isBipartite(graph [][]int) bool {
    validBipartite = true

    n := len(graph)
    color = make([]bool, n)
    visited = make([]bool, n)

    for i := 0; i < n; i++ {
        if !visited[i] {
            traverse(graph, i)
        }
    }
    return validBipartite
}

func traverse(graph [][]int, start int) {
    queue := make([]int, 0)
    queue = append(queue, start)

    visited[start] = true

    for len(queue) > 0 && validBipartite {
        curr := queue[0]
        queue = queue[1:]
        visited[curr] = true

        for _, next := range graph[curr] {
            if !visited[next] {
                color[next] = !color[curr]
                queue = append(queue, next)
            } else {
                if color[next] == color[curr] {
                    validBipartite = false
                }
            }
        }
    }
}
```

## 可能的二分法

https://leetcode-cn.com/problems/possible-bipartition/

一样的道理，根据关系建图后，判断是否为二分图。要注意的是由于是无向图，建图的时候要同时将出入节点都加到邻接表里去。

```go
var validBipartite bool

var color   []bool
var visited []bool

func possibleBipartition(n int, dislikes [][]int) bool {
    validBipartite = true
    color = make([]bool, n)
    visited = make([]bool, n)

    graph := buildGrapth(n, dislikes)
    for i := 0; i < n; i++ {
        if !visited[i] {
            traverse(graph, i)
        }
    }
    return validBipartite
}

func traverse(graph [][]int, curr int) {
    if !validBipartite {
        return
    }

    visited[curr] = true
    for _, next := range graph[curr] {
        if !visited[next] {
            color[next] = !color[curr]
            traverse(graph, next)
        } else {
            if color[next] == color[curr] {
                validBipartite = false
                break
            }
        }
    }
}

func buildGrapth(n int, dislikes [][]int) [][]int {
    graph := make([][]int, n)

    for _, dislike := range dislikes {
        from, to := dislike[0]-1, dislike[1]-1
        graph[from] = append(graph[from], to)
        graph[to] = append(graph[to], from)
    }

    return graph
}
```

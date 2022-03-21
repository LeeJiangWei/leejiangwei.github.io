---
title: 拓扑排序
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

# 拓扑排序

https://labuladong.gitee.io/algo/2/20/39/


## 课程表 I

https://leetcode-cn.com/problems/course-schedule/

课和它的前置课程可以视为图的关系，如果出现有环图，则必不能完成课程。

注意，有的课可能也是没又前置课程的，也可能多个课依赖于一个前置课程，因此不能使用是否已经访问过 `visited[i]` 的值来判断是否走到了重复的环上，而应该在 `path[i]` 上判断当前路径上是否有环。

### DFS 遍历

用函数调用栈来完成 BFS。每访问一个图节点，就继续访问他它的邻接节点。

```go
var visited []bool
var path    []bool

var hasCycle bool

func canFinish(numCourses int, prerequisites [][]int) bool {
    graph := buildGrapth(numCourses, prerequisites)

    visited = make([]bool, numCourses)
    path = make([]bool, numCourses)
    hasCycle = false
    
    for i := 0; i < numCourses; i++ {
        traverse(graph, i)
    }

    return !hasCycle
}

func traverse(graph [][]int, curr int) {
    if path[curr] {
        hasCycle = true
    }
    if visited[curr] || hasCycle {
        return
    }
    
    visited[curr] = true
    
    path[curr] = true
    for _, next := range graph[curr] {
        traverse(graph, next)
    }
    path[curr] = false
}

// 构建图为邻接表
func buildGrapth(numCourses int, prerequisites [][]int) [][]int {
    graph := make([][]int, numCourses)
    for i := range graph {
        graph[i] = make([]int, 0)
    }

    for _, dependency := range prerequisites {
        from, to := dependency[0], dependency[1]
        graph[from] = append(graph[from], to)
    }

    return graph
}
```

### BFS 遍历

BFS 判断图中有无环，需要借助每个节点的**入度**信息（指向这个节点的边的数目）。

流程：
1. 构建邻接表
2. 构建 `indegree` 数组，记录每个节点的入度
3. 初始化 BFS 队列，将入度为 0 的节点进队
4. 执行 BFS 循环，不断弹出节点并将其邻接节点的入度 -1，将入度变为 0 的节点进队
5. 如果所有节点都被遍历过，则说明不存在环

当节点入度为 0 时，我们才去访问它的邻接节点。如果队列空了，而还有节点没访问完，说明这些节点的入度全不为 0，它们肯定存在环的关系。

```go
func canFinish(numCourses int, prerequisites [][]int) bool {
    graph := buildGrapth(numCourses, prerequisites)

    // 统计节点入度
    indegrees := make([]int, numCourses)
    for _, dependency := range prerequisites {
        indegrees[dependency[1]] += 1
    }

    // 将入度为 0 的节点进队
    queue := make([]int, 0)
    for i, indegree := range indegrees {
        if indegree == 0 {
            queue = append(queue, i)
        }
    }

    count := 0
    for len(queue) != 0 {
        sz := len(queue)
        for i := 0; i < sz; i++ {
            curr := queue[0]
            queue = queue[1:]

            count += 1

            // 相邻节点入度 -1，如果为 0 了就进队
            for _, next := range graph[curr] {
                indegrees[next] -= 1
                if indegrees[next] == 0 {
                    queue = append(queue, next)
                }
            }
        }
    }

    // 如果不是所有节点已被访问，则说明有环
    return count == numCourses
}

func buildGrapth(numCourses int, prerequisites [][]int) [][]int {
    graph := make([][]int, numCourses)
    for i := range graph {
        graph[i] = make([]int, 0)
    }

    for _, dependency := range prerequisites {
        from, to := dependency[0], dependency[1]
        graph[from] = append(graph[from], to)
    }

    return graph
}
```

## 课程表 II

https://leetcode-cn.com/problems/course-schedule-ii/

与上一题的区别是，此题不仅要判断能否完成，还需要找出正确的完成顺序。

### 拓扑排序

简单来说，拓扑排序就是将图的节点拉成一条直线，且所有箭头的方向都是一致的。

如果一幅图有环，则无法进行拓扑排序，否则一定可以进行拓扑排序。

完成拓扑排序，只需要在图节点的后序遍历位置加入自己即可，视建图方式来判断要不要将这个列表反转。

本题中，邻接表 `graph[i][j]` 定义为要选修 `i`，需要**先**修 `j`，因此按照后序遍历，子节点会在列表前面，即前置课程在前面，是正确的顺序。代码与上体几乎一样，先判断有无环，如果没有就输出拓扑顺序。

```go
var visited []bool
var path    []bool

var hasCycle bool

var postOrder []int  // 【新增】用后序遍历来完成拓扑排序

func findOrder(numCourses int, prerequisites [][]int) []int {
    visited = make([]bool, numCourses)
    path = make([]bool, numCourses)
    hasCycle = false

    postOrder = make([]int, 0)

    graph := buildGrapth(numCourses, prerequisites)
        for i := 0; i < numCourses; i++ {
        traverse(graph, i)
    }

    if hasCycle {
        return []int{}
    }

    return postOrder
}

func traverse(graph [][]int, curr int) {
    if path[curr] {
        hasCycle = true
    }
    if visited[curr] || hasCycle {
        return
    }
    
    visited[curr] = true
    
    path[curr] = true
    for _, next := range graph[curr] {
        traverse(graph, next)
    }
    postOrder = append(postOrder, curr)  // 【新增】在后序位置添加节点
    path[curr] = false
}

func buildGrapth(numCourses int, prerequisites [][]int) [][]int {
    graph := make([][]int, numCourses)
    for i := range graph {
        graph[i] = make([]int, 0)
    }

    for _, dependency := range prerequisites {
        from, to := dependency[0], dependency[1]
        graph[from] = append(graph[from], to)
    }

    return graph
}
```

### 拓扑排序 BFS 版本

容易看出，BFS 判断有无环的算法，直接输出拓扑排序的结果。由于图定义的关系，这里需要反转一下。

```go
func findOrder(numCourses int, prerequisites [][]int) []int {
    graph := buildGrapth(numCourses, prerequisites)

    result := make([]int, 0)  // 【新增】记录 BFS 访问顺序

    // 统计节点入度
    indegrees := make([]int, numCourses)
    for _, dependency := range prerequisites {
        indegrees[dependency[1]] += 1
    }

    // 将入度为 0 的节点进队
    queue := make([]int, 0)
    for i, indegree := range indegrees {
        if indegree == 0 {
            queue = append(queue, i)
        }
    }

    count := 0
    for len(queue) != 0 {
        sz := len(queue)
        for i := 0; i < sz; i++ {
            curr := queue[0]
            queue = queue[1:]

            count += 1
            result = append(result, curr)

            // 相邻节点入度 -1，如果为 0 了就进队
            for _, next := range graph[curr] {
                indegrees[next] -= 1
                if indegrees[next] == 0 {
                    queue = append(queue, next)
                }
            }
        }
    }

    if count != numCourses {
        return []int{}
    }
    reverse(result)
    return result
}

func buildGrapth(numCourses int, prerequisites [][]int) [][]int {
    graph := make([][]int, numCourses)
    for i := range graph {
        graph[i] = make([]int, 0)
    }

    for _, dependency := range prerequisites {
        from, to := dependency[0], dependency[1]
        graph[from] = append(graph[from], to)
    }

    return graph
}

func reverse(nums []int) {
    n := len(nums)
    for i := 0; i < n / 2; i++ {
        nums[i], nums[n-1-i] = nums[n-1-i], nums[i]
    }
}
```
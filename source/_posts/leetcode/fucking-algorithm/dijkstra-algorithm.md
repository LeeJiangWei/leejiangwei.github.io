---
title: DIJKSTRA 算法
categories:
  - LeetCode
  - Fucking
  - 图
tags:
  - Go
  - 图
index_img: /img/code.jpg
sticky: -10
date: 2022-03-22 15:50:00
---

# DIJKSTRA 算法

https://labuladong.gitee.io/algo/2/20/45/
j
## 算法模板

输入：
+ `graph [][]int` 图的邻接矩阵，矩阵的值代表边的权重，-1 表示不直接ji相连
+ `start int` 最短路径算法的起点

输出：
+ `minDistanceTo []int` 从起点到其他所有点的最短距离，值为 `MaxInt` 表示不可达

核心思想：
+ 从起点开始 BFS 遍历整幅图。遍历到某个点 `curr` 时，检查从 `start` 到 `curr` 的距离 `minDistanceTo[curr]` 加上到下一个点 `next` 的距离 `graph[curr][next]` 是否更短了。
  + 如果是，就说明这条路径比之前记录的更短，更新到 `next` 的最短路径，并且继续从 `next` 往后遍历；
  + 如果没有更短，就不用继续遍历了，因为这条路径已经不是最短的了。
+ 类似于动态规划的思想，`minDistanceTo[next] = max(minDistanceTo[next], minDistanceTo[curr] + graph[curr][next])`

注：用优先级队列是因为贪心策略，优先选更短的路径来尝试遍历。普通队列也能完成。

```go
import (
	"container/heap"
	"math"
)

/* DIJKSTRA 算法 */

func dijkstra(graph [][]int, start int) []int {
	v := len(graph)

	minDistanceTo := make([]int, v)
	for i := range minDistanceTo {
		minDistanceTo[i] = math.MaxInt
	}

	// 起点到自己的距离是 0
	minDistanceTo[start] = 0

	pq := PriorityQueue{}
	heap.Push(&pq, &State{start, 0})

	// 开始 BFS
	for pq.Len() != 0 {
		currState := heap.Pop(&pq).(*State)
		currPos, currDist := currState.id, currState.distanceFromStart

		// 已经有一条更短的到当前点的路径了
		if currDist > minDistanceTo[currPos] {
			continue
		}

		// 开始遍历相邻的点
		for nextPos, weight := range graph[currPos] {
			// 跳过不可达的点
			if weight == -1 {  
				continue
			}

			// 到下一个点的距离等于自己的距离加上边的权重
			distanceToNext := minDistanceTo[currPos] + weight

			// 如果当前的路径更短，就更新最短路径，并继续将下一个点加入队列遍历
			if minDistanceTo[nextPos] > distanceToNext {
				minDistanceTo[nextPos] = distanceToNext
				heap.Push(&pq, &State{nextPos, distanceToNext})
			}
		}
	}
	return minDistanceTo
}

/* 记录图节点的编号以及离起点的距离 */

type State struct {
	id                int
	distanceFromStart int
}

/* 为 State 结构体定义的优先级队列，按 distanceFromStart 升序排序 */ 

type PriorityQueue []*State

func (pq PriorityQueue) Len() int { return len(pq) }

func (pq PriorityQueue) Less(i, j int) bool {
	return pq[i].distanceFromStart < pq[j].distanceFromStart
}

func (pq PriorityQueue) Swap(i, j int) {
	pq[i], pq[j] = pq[j], pq[i]
}

func (pq *PriorityQueue) Push(x interface{}) {
	item := x.(*State)
	*pq = append(*pq, item)
}

func (pq *PriorityQueue) Pop() interface{} {
	old := *pq
	n := len(old)
	item := old[n-1]
	old[n-1] = nil // avoid memory leak
	*pq = old[0 : n-1]
	return item
}
```

由于 Golang 中实现优先级队列比较麻烦，放一个用一个普通队列实现的，代码更加简洁。

```go
/* DIJKSTRA 算法 */

func dijkstra(graph [][]int, start int) []int {
	v := len(graph)

	minDistanceTo := make([]int, v)
	for i := range minDistanceTo {
		minDistanceTo[i] = math.MaxInt
	}

	// 起点到自己的距离是 0
	minDistanceTo[start] = 0

	queue := make([]State, 0)
    queue = append(queue, State{start, 0})

	for len(queue) != 0 {
		currState := queue[0]
        queue = queue[1:]

		currPos, currDist := currState.id, currState.distanceFromStart

		// 已经有一条更短的到当前点的路径了
		if currDist > minDistanceTo[currPos] {
			continue
		}

		// 开始遍历相邻的点
		for nextPos, weight := range graph[currPos] {
			// 跳过不可达的点
            if weight == -1 {
				continue
			}

			// 到下一个点的距离等于自己的距离加上边的权重
			distanceToNext := minDistanceTo[currPos] + weight
		
			// 如果当前的路径更短，就更新最短路径，并继续将下一个点加入队列遍历
			if minDistanceTo[nextPos] > distanceToNext {
				minDistanceTo[nextPos] = distanceToNext
				queue = append(queue, State{nextPos, distanceToNext})
			}
		}
	}
	return minDistanceTo
}

type State struct {
	id                int
	distanceFromStart int
}
```

## 网络延迟时间

https://leetcode-cn.com/problems/network-delay-time/

这道题就是求从起点开始，到每一个点的最短路径中最长的那条，就是最大的网络延迟时间。构造图之后直接套模板即可。

```go
func networkDelayTime(times [][]int, n int, k int) int {
    graph := make([][]int, n)
    for i := range graph {
        graph[i] = make([]int, n)
        for j := range graph[i] {
            graph[i][j] = -1
        }
    }

    for _, time := range times {
        from, to, w := time[0] - 1, time[1] - 1, time[2]
        graph[from][to] = w
    }

    distTo := dijkstra(graph, k-1)

	// 寻找最长的最短路径
    res := 0
    for _, d := range distTo {
        if d == math.MaxInt {
            return -1
        }
        if d > res {
            res = d
        }
    }

    return res
}
```

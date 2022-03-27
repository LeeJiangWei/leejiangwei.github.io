---
title: 网易笔试题（3.27） 编程题 4. 沼泽地
categories:
  - LeetCode
  - 笔试编程题
tags:
  - Go
index_img: /img/code.jpg
sticky: -10
date: 2022-03-27 18:03:37
---

# 网易笔试题（3.27） 编程题 4. 沼泽地

## 题目描述

给一个二维数组，元素只会是 0 或 1，0 代表 平地，1 代表沼泽。小红从左上角出发，要走到右下角，可以向左、右、下走，一次走一格。同地形的路程耗费 1 点时间，而跨越地形（从 0 走到 1 或者从 1 走到 0）需要时间 2 点，因为要更换装备。

求走到终点的最短耗时。

## DIJKSTRA 最短路径

很明显是个最短路径题，做的时候没想起来。

```go
package main

import (
	"container/heap"
	"fmt"
	"math"
)

func main() {
	graph := [][]int{
		{1, 1, 0},
		{1, 0, 1},
		{0, 0, 0},
		{0, 0, 1},
	}
	n, m := len(graph), len(graph[0])

	fmt.Println(dijkstra(graph)[n-1][m-1])
}

type State struct {
	x                 int
	y                 int
	distanceFromStart int
}

func dijkstra(graph [][]int) [][]int {
	n, m := len(graph), len(graph[0])
	minDistanceTo := make([][]int, n)

	for i := range minDistanceTo {
		minDistanceTo[i] = make([]int, m)
		for j := range minDistanceTo[i] {
			minDistanceTo[i][j] = math.MaxInt
		}
	}
	minDistanceTo[0][0] = 0

	pq := PriorityQueue{}
	heap.Push(&pq, &State{0, 0, 0})

	// 3 个能走的方向
	directions := [][]int{
		{1, 0},
		{0, 1},
		{0, -1},
	}

	for pq.Len() > 0 {
		currState := heap.Pop(&pq).(*State)
		currX, currY, currDist := currState.x, currState.y, currState.distanceFromStart

		if minDistanceTo[currX][currY] < currDist {
			continue
		}

		for _, d := range directions {
			nextX, nextY := currX+d[0], currY+d[1]
			
			// 走出边界
			if nextX >= n || nextY < 0 || nextY >= m {
				continue
			}

			weight := 1
			if graph[nextX][nextY] != graph[currX][currY] {
				weight += 1
			}

			distanceToNext := minDistanceTo[currX][currY] + weight

			if distanceToNext < minDistanceTo[nextX][nextY] {
				minDistanceTo[nextX][nextY] = distanceToNext
				heap.Push(&pq, &State{nextX, nextY, distanceToNext})
			}
		}
	}

	return minDistanceTo
}

type PriorityQueue []*State

func (pq PriorityQueue) Len() int {
	return len(pq)
}

func (pq PriorityQueue) Less(i, j int) bool {
	return pq[i].distanceFromStart < pq[j].distanceFromStart
}

func (pq PriorityQueue) Swap(i, j int) {
	pq[i], pq[j] = pq[j], pq[i]
}

func (pq *PriorityQueue) Push(x interface{}) {
	*pq = append(*pq, x.(*State))
}

func (pq *PriorityQueue) Pop() interface{} {
	old := *pq
	n := len(old)
	item := old[n-1]
	old[n-1] = nil
	*pq = old[:n-1]
	return item
}
```

---
title: 阿里笔试题（3.14） 编程题 2. 探照灯
categories:
  - LeetCode
  - 笔试编程题
tags:
  - Go
index_img: /img/code.jpg
sticky: -10
date: 2022-03-15 19:43:37
---

# 阿里笔试题（3.14） 编程题 2. 探照灯

## 题目描述

给定一个二维数组，元素只有 0 和 1 两种，0 代表探照灯，1 代表人。探照灯朝着 4 个方向照，照到一个人就能得一分（距离无限远），但是人会挡住光线。求这个二维数组的分数。

## 暴力法

按人统计 4 个方向上的灯数量，复杂度 O(n^3)。（有一半会超时）

## 改进

分别考虑 4 个照射方向。以从左向右照为例，从最左边开始，统计有几盏灯在朝着右边照，遇到人就将这几盏灯作为得分加进结果中，并清空灯的计数。为每一行重复此操作即可。4 次二维数组遍历，渐进复杂度为 O(n^2)。

```go
package main

import "fmt"

func main() {
	matrix := [][]int{
		{0, 1, 0, 0},
		{1, 0, 1, 0},
	}

	m, n := len(matrix), len(matrix[0])
	totalScore := 0

	// 从左往右照的得分
	for i := 0; i < m; i++ {
		score := 0
		lightCount := 0
		for j := 0; j < n; j++ {
			if matrix[i][j] == 0 {
				lightCount++
			} else {
				score += lightCount
				lightCount = 0
			}
		}
		totalScore += score
	}

	// 从右往左照
	for i := 0; i < m; i++ {
		score := 0
		lightCount := 0
		for j := n - 1; j >= 0; j-- {
			if matrix[i][j] == 0 {
				lightCount++
			} else {
				score += lightCount
				lightCount = 0
			}
		}
		totalScore += score
	}

	// 从上往下照
	for j := 0; j < n; j++ {
		score := 0
		lightCount := 0
		for i := 0; i < m; i++ {
			if matrix[i][j] == 0 {
				lightCount++
			} else {
				score += lightCount
				lightCount = 0
			}
		}
		totalScore += score
	}

	// 从下往上照
	for j := 0; j < n; j++ {
		score := 0
		lightCount := 0
		for i := m - 1; i >= 0; i-- {
			if matrix[i][j] == 0 {
				lightCount++
			} else {
				score += lightCount
				lightCount = 0
			}
		}
		totalScore += score
	}

	fmt.Println(totalScore)
}
```

---
title: 单词搜索
categories:
  - LeetCode
  - HOT 100
tags:
  - Go
  - 回溯法
index_img: /img/code.jpg
sticky: -10
date: 2022-07-19 20:00:00
---

# 单词搜索

https://leetcode.cn/problems/word-search/

## 回溯法

```go
var directions = [][]int{
    {1, 0},
    {0, 1},
    {-1, 0},
    {0, -1},
}

func exist(board [][]byte, word string) bool {
    m, n := len(board), len(board[0])
    visited := make([][]bool, m)
    for i := range visited {
        visited[i] = make([]bool, n)
    }

    // 回溯函数定义
    var backtrack func(i, j, curr int) bool
    backtrack = func(i, j, curr int) bool {
        if board[i][j] != word[curr] {
            return false
        }
        if curr == len(word)-1 {
            return true
        }

        // 设置当前位置为已访问，并在函数返回之前回溯状态
        visited[i][j] = true
        defer func(){
            visited[i][j] = false
        }()

        // 访问四个方向的下个位置
        for _, d := range directions {
            nextI, nextJ := i+d[0], j+d[1]
            if nextI >= 0 && nextI < m && nextJ >= 0 && nextJ < n && !visited[nextI][nextJ] {
                if backtrack(nextI, nextJ, curr+1) {
                    return true
                }
            }
        }

        return false
    }

    // 以棋盘每个格子为起点都试一次
    for i := range visited {
        for j := range visited[i] {
            if backtrack(i, j, 0) {
                return true
            }
        }
    }

    return false
}
```
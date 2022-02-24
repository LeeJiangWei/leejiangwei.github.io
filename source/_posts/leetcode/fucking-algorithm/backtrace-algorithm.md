---
title: 回溯法
categories:
  - LeetCode
  - Fucking
  - 暴力搜索算法
tags:
  - Go
index_img: /img/code.jpg
sticky: -10
date: 2022-02-24 13:54:44
---

# 回溯法

回溯法基本框架：

```python
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    
    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```

## 全排列问题

https://leetcode-cn.com/problems/permutations/

```go
var result [][]int

func permute(nums []int) [][]int {
    result = make([][]int, 0)
    backtrace(nums, nil)
    return result
}

func backtrace(nums []int, path []int) {
    // 回溯终点：排列的数字已经用完
    if len(path) == len(nums) {
        result = append(result, path)
        return
    }

    for _, n := range nums {
        // 检查数字是否已经用过了
        exist := false
        for _, m := range path {
            if m == n {
                exist = true
                break
            }
        }
        if exist {
            continue
        }

        backtrace(nums, append(path, n))  // 这里创建了新的切片递归下去，所以不用撤销
    }
}
```

## N 皇后

https://leetcode-cn.com/problems/n-queens/

需要有个函数来判断当前位置是否能放下皇后。由于是一行一行从上往下放的，只用判断当前位置的上方、左上、右上是否存在其他皇后即可。

```go
var res [][]string

func solveNQueens(n int) [][]string {
    res = make([][]string, 0)
    board := make([][]string, n)
    for i := range board {
        board[i] = make([]string, n)
        for j := range board[i] {
            board[i][j] = "."
        }
    }
    backtrace(0, board)
    return res
}

func backtrace(row int, board [][]string) {
    n := len(board)
    if row == n {
        b := make([]string, n)
        sb := &strings.Builder{}
        for i := 0; i < n; i++ {
            sb.Reset()
            for j := 0; j < n; j++ {
                sb.WriteString(board[i][j])
            }
            b[i] = sb.String()
        }
        res = append(res, b)
        return
    }

    for col := 0; col < n; col++ {
        if !isValid(row, col, board) {
            continue
        }
        board[row][col] = "Q"      // 做选择
        backtrace(row + 1, board)
        board[row][col] = "."      // 撤销选择
    } 
}

// 检查是否能在当前位置[row][col]放置皇后
func isValid(row, col int, board [][]string) bool {
    n := len(board)

    // 当前皇后【上方】是否存在其他皇后
    for i := 0; i < n; i++ {
        if board[i][col] == "Q" {
            return false
        }
    }

    // 当前皇后【左上方】是否存在其他皇后
    for i, j := row - 1, col - 1; i >= 0 && j >= 0; i, j = i-1, j-1 {
        if board[i][j] == "Q" {
            return false
        }
    }

    // 当前皇后【右上方】是否存在其他皇后
    for i, j := row - 1, col + 1; i >= 0 && j < n; i, j = i-1, j+1 {
        if board[i][j] == "Q" {
            return false
        }
    }

    return true
}
```
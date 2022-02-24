---
title: 解数独
categories:
  - LeetCode
  - Fucking
  - 暴力搜索算法
tags:
  - Go
index_img: /img/code.jpg
sticky: -10
date: 2022-02-24 19:25:44
---

# 解数独

https://leetcode-cn.com/problems/sudoku-solver/

回溯算法可以通过返回布尔值，在得到一个正确的解法后就终止回溯。

```go
func solveSudoku(board [][]byte)  {
    backtrack(board, 0, 0)
}

func backtrack(board [][]byte, i, j int) bool {
    // 列越界，递归到下一行继续填
    if j == 9 {
        return backtrack(board, i + 1, 0)
    }
    // 行越界，说明已经填完了，返回
    if i == 9 {
        return true
    }
    
    // 已经存在预设数字
    if board[i][j] != '.' {
        return backtrack(board, i, j + 1)
    }

    for n := '1'; n <= '9'; n++ {
        if !isValid(board, i, j, byte(n)) {
            continue
        }
        
        // 做选择
        board[i][j] = byte(n)
        // 如果找到一个可行解，立即结束
        if backtrack(board, i, j + 1) {
            return true
        }
        // 撤销选择
        board[i][j] = '.'
    }

    return false
}

// 判断 board[row][col] 是否可以填入 val
func isValid(board [][]byte, row, col int, val byte) bool {
    for i := 0; i < 9; i++ {
        // 判断行是否存在重复
        if board[row][i] == val {
            return false
        }
        // 判断列是否存在重复
        if board[i][col] == val {
            return false
        }
        // 判断 3 x 3 小方框内是否存在重复
        if board[(row/3)*3 + i/3][(col/3)*3 + i%3] == val {
            return false
        }
    }
    return true
}
```

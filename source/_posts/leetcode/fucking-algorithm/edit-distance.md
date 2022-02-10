---
title: 编辑距离
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
  - 字符串
index_img: /img/code.jpg
sticky: -10
date: 2022-02-10 19:37:44
---

# 编辑距离

https://leetcode-cn.com/problems/edit-distance/

## 带备忘录的递归

核心思想：如果字符串不匹配，3 种编辑操作都试一遍，然后找出最小距离的

DP 函数定义：

```go
func dp(i int, j int) int {
    // 返回 s1[0..i] 和 s2[0..j] 的最小编辑距离
}
```

```go
func minDistance(word1 string, word2 string) int {
    memo := make(map[[2]int]int) // 建立备忘录，以 array 为 key
    return dp(memo, word1, word2, len(word1)-1, len(word2)-1)
}  

// DP 函数定义：两个字符串在 i 和 j 之前的编辑距离
func dp(memo map[[2]int]int, word1 string, word2 string, i int, j int) int {
    // base case: 已经处理完了其中一个字符，剩余编辑距离就是另一个字符未处理的长度
    if i == -1 {
        return j + 1
    }
    if j == -1 {
        return i + 1
    }

    // 查备忘录
    if val, ok := memo[[2]int{i, j}]; ok {
        return val
    }

    if word1[i] == word2[j] { 
        // 字符相等，两个指针都往前走，编辑距离不变
        v := dp(memo, word1, word2, i-1, j-1)
        memo[[2]int{i, j}] = v

        return v
    } else {
        // 字符不相等，则进行一次操作使得它们相等，编辑距离+1

        v1 := dp(memo, word1, word2, i, j-1) + 1   // 在 i 后插入字符，前移 j 
        v2 := dp(memo, word1, word2, i-1, j) + 1   // 删除 i 处字符，并前移 i
        v3 := dp(memo, word1, word2, i-1, j-1) + 1 // 替换 i 处字符，前移两个指针

        v := min(v1, min(v2, v3))
        memo[[2]int{i, j}] = v
        
        return v
    }
}

func min(a int, b int) int {
    if a < b {
        return a
    }
    return b
}
```

## 动态规划

构建 DP 表：`dp[i-1][j-1]` 存储 `s1[0..i]` 和 `s2[0..j]` 的最小编辑距离

dp 函数的 base case 是 i,j 等于 -1，而数组索引至少是 0，所以 dp 数组的 index 会偏移一位，即 `dp[0][0]` 表示 s1 和 s2 都已经处理完的情况（i = -1, j = -1)，即剩余最小编辑距离为 0。

![DP 表](https://github.com/labuladong/fucking-algorithm/raw/master/pictures/editDistance/dp.jpg)

```go
func minDistance(word1 string, word2 string) int {
    // 初始化 DP 表为 2D 切片
    dp := make([][]int, len(word1) + 1)
    for i := range dp {
        dp[i] = make([]int, len(word2) + 1)
    }

    // base case
    for i := 1; i <= len(word1); i++ {
        dp[i][0] = i
    }
    for j := 1; j <= len(word2); j++ {
        dp[0][j] = j
    }

    // 构建 DP 表
    for i := 1; i <= len(word1); i++ {
        for j := 1; j <= len(word2); j++ {
            if word1[i-1] == word2[j-1] {  // 注意这里对字符串的索引要减1
                dp[i][j] = dp[i-1][j-1]
            } else {
                dp[i][j] = min(dp[i-1][j] + 1, min(dp[i][j-1] + 1, dp[i-1][j-1] + 1))
            }
        }
    }

    return dp[len(word1)][len(word2)]
}  

func min(a int, b int) int {
    if a < b {
        return a
    }
    return b
}
```

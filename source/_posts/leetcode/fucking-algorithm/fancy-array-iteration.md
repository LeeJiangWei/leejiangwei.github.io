---
title: 二维数组的花式遍历技巧
categories:
  - LeetCode
  - Fucking
  - 数组
tags:
  - Go
  - 数组
index_img: /img/code.jpg
sticky: -10
date: 2022-02-21 15:53:00
---

# 二维数组的花式遍历技巧

## 旋转图像

https://leetcode-cn.com/problems/rotate-image/

```go
func rotate(matrix [][]int)  {
    n := len(matrix)
    // 对角线反转矩阵
    for i := 0; i < n; i++ {
        for j := i + 1; j < n; j++ {
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
        }
    }
    // 反转每一行
    for i := 0; i < n; i++ {
        reverse(matrix[i])
    }
}

func reverse(list []int) {
    i, j := 0, len(list) - 1
    for i < j {
        list[i], list[j] = list[j], list[i]
        i++
        j--
    }
}
```
## 螺旋矩阵

https://leetcode-cn.com/problems/spiral-matrix/

![](https://labuladong.gitee.io/algo/images/%e8%8a%b1%e5%bc%8f%e9%81%8d%e5%8e%86/6.png)

定义好这几个 bound，然后在它们的范围内螺旋遍历即可。

```go
func spiralOrder(matrix [][]int) []int {
    m, n := len(matrix), len(matrix[0])
    upperBound, rightBound, lowerBound, leftBound := 0, n-1, m-1, 0
    result := make([]int, m * n)
    count := 0
    for count < m * n {
        if upperBound <= lowerBound {
            for i := leftBound; i <= rightBound; i++ {
                result[count] = matrix[upperBound][i]
                count++
            }
            upperBound++
        }
        if rightBound >= leftBound {
            for i := upperBound; i <= lowerBound; i++ {
                result[count] = matrix[i][rightBound]
                count++
            }
            rightBound--
        }
        if lowerBound >= upperBound {
            for i := rightBound; i >= leftBound; i-- {
                result[count] = matrix[lowerBound][i]
                count++
            }
            lowerBound--
        }
        if leftBound <= rightBound {
            for i := lowerBound; i >= upperBound; i-- {
                result[count] = matrix[i][leftBound]
                count++
            }
            leftBound++
        }
    }
    return result
}
```

## 螺旋矩阵 II

就是刚刚那道题的反过程。

```go
func generateMatrix(n int) [][]int {
    matrix := make([][]int, n)
    for i := range matrix {
        matrix[i] = make([]int, n)
    }

    upperBound, rightBound, lowerBound, leftBound := 0, n-1, n-1, 0
    count := 1
    for count <= n * n {
        if upperBound <= lowerBound {
            for i := leftBound; i <= rightBound; i++ {
                matrix[upperBound][i] = count
                count++
            }
            upperBound++
        }
        if rightBound >= leftBound {
            for i := upperBound; i <= lowerBound; i++ {
                matrix[i][rightBound] = count
                count++
            }
            rightBound--
        }
        if lowerBound >= upperBound {
            for i := rightBound; i >= leftBound; i-- {
                matrix[lowerBound][i] = count
                count++
            }
            lowerBound--
        }
        if leftBound <= rightBound {
            for i := lowerBound; i >= upperBound; i-- {
                matrix[i][leftBound] = count
                count++
            }
            leftBound++
        }
    }
    return matrix
}
```
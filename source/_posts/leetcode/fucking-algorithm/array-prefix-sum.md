---
title: 完全二叉树的节点个数
categories:
  - LeetCode
  - Fucking
  - 数组
tags:
  - Go
  - 数组
index_img: /img/code.jpg
sticky: -10
date: 2022-02-21 11:10:00
---

# 小而美的算法技巧：前缀和数组

## 一维数组中的前缀和

https://leetcode-cn.com/problems/range-sum-query-immutable/

前缀和技巧：提前计算数组从 0 开始的累加和并储存，在需要获取一个区间内的和时，直接在累加和数组中相减即可得到，不需要每次去重新遍历数组。

![](https://labuladong.gitee.io/algo/images/%e5%b7%ae%e5%88%86%e6%95%b0%e7%bb%84/1.jpeg)

```go
type NumArray struct {
    prefixSum []int
}


func Constructor(nums []int) NumArray {
    a := new(NumArray)
    prefixSum := make([]int, len(nums) + 1)
    // 计算前缀和
    for i := 1; i <= len(nums); i++ {
        prefixSum[i] = prefixSum[i - 1] + nums[i - 1]
    }
    a.prefixSum = prefixSum
    return *a
}


func (this *NumArray) SumRange(left int, right int) int {
    return this.prefixSum[right + 1] - this.prefixSum[left]
}
```

## 二维矩阵中的前缀和

https://leetcode-cn.com/problems/range-sum-query-2d-immutable/

类似于上一题，不过是二维的。对于二维情况稍微复杂一点，但原理还是一样的：通过几个子矩阵前缀和的加减运算得出任意区域矩阵的和。

![](https://labuladong.gitee.io/algo/images/%e5%89%8d%e7%bc%80%e5%92%8c/5.png)

```go
type NumMatrix struct {
    prefixMatrix [][]int
}


func Constructor(matrix [][]int) NumMatrix {
    m, n := len(matrix), len(matrix[0])
    prefix := make([][]int, m + 1)
    for i := range prefix {
        prefix[i] = make([]int, n + 1)
    }

    for i := 1; i <= m; i++ {
        for j := 1; j <= n; j++ {
            prefix[i][j] = prefix[i-1][j] + prefix[i][j-1] - prefix[i-1][j-1] + matrix[i-1][j-1]
        }
    }
    return NumMatrix{prefix}
}


func (this *NumMatrix) SumRegion(row1 int, col1 int, row2 int, col2 int) int {
    return this.prefixMatrix[row2+1][col2+1] - this.prefixMatrix[row1][col2+1] - this.prefixMatrix[row2+1][col1] + this.prefixMatrix[row1][col1]
}
```

## 和为 k 的子数组

类似于两数之和，计算出前缀数组后不用再去遍历一遍，只需要寻找它减去 k 以后的前缀和是否已经在哈希表中。这样的前缀和有几个，就有几个能凑出和为 k 的子数组，直接在结果加上即可。

```go
func subarraySum(nums []int, k int) int {
    m := make(map[int]int)
    m[0] = 1  // base case
    
    res, sum:= 0, 0
    for i := 0; i < len(nums); i++ {
        sum += nums[i]
        start := sum - k
        if val, ok := m[start]; ok {
            res += val
        }
        m[sum] += 1
    }

    return res
}
```

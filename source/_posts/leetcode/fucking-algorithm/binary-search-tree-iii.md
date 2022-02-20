---
title: 二叉搜索树 3
categories:
  - LeetCode
  - Fucking
  - 树
tags:
  - Go
  - BST
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-02-20 20:16:00
---

# 二叉搜索树系列 3

## 不同的二叉搜索树

https://leetcode-cn.com/problems/unique-binary-search-trees/

这题实际上是一题动态规划问题，对于一个区间内的数字，穷举每一个数字作为根节点的情况，它的左右子树由相同的函数在不同的区间递归生成。

对于一个根节点，如果它的左子树有 m 种可能，右子树有 n 种可能，那它自己就是 m*n 种可能。最后加上备忘录消除重叠子问题。

```go
var memo [][]int

func numTrees(n int) int {
    memo = make([][]int, n+1)
    for i := range memo {
        memo[i] = make([]int, n+1)
    }
    return count(1, n)
}

func count(low, high int) int {
    // base case: 对应空树的情况，也是一种可能
    if low > high {
        return 1
    }

    if memo[low][high] != 0 {
        return memo[low][high]
    }

    res := 0
    for i := low; i <= high; i++ {
        res += count(low, i-1) * count(i+1, high)
    }
    memo[low][high] = res
    return res
}
```

## 不同的二叉搜索树 II

https://leetcode-cn.com/problems/unique-binary-search-trees-ii/

遇上一题一样，不过还要将树组合起来，也是用穷举的方法。

```go
func generateTrees(n int) []*TreeNode {
    return generate(1, n)
}

func generate(low, high int) []*TreeNode {
    // base case: 返回只包含空节点的列表（而不能返回空列表）
    if low > high {
        return []*TreeNode{nil}
    }

    res := make([]*TreeNode, 0)
    for i := low; i <= high; i++ {
        // 生成所有可能的左右子树
        leftTrees := generate(low, i-1)
        rightTrees := generate(i+1, high)

        // 穷举组合出所有可能的树
        for _, left := range leftTrees {
            for _, right := range rightTrees {
                res = append(res, &TreeNode{i, left, right})
            }
        }
    }

    return res
}
```

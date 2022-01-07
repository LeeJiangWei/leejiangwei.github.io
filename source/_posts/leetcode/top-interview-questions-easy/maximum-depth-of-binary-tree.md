---
title: 二叉树的最大深度
categories:
  - LeetCode
  - 初级算法
  - 树
tags:
  - Go
  - 二叉树
index_img: /img/code.jpg
sticky: -10
date: 2022-01-07 16:35:54
---

# 二叉树的最大深度

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xnd69e/

https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/solution/

## 深度优先（递归）

树的深度优先搜索算法一般用递归实现。

```go
func maxDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    return 1 + max(maxDepth(root.Left), maxDepth(root.Right))
}

func max(a int, b int) int {
    if a > b {
        return a
    } else {
        return b
    }
}
```

## 广度优先

一般用队列（数组）实现。

```go
func maxDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    queue := []*TreeNode{}
    queue = append(queue, root)
    ans := 0
    for len(queue) > 0 {
        sz := len(queue)
        for sz > 0 {
            node := queue[0]
            queue = queue[1:]
            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
            sz--
        }
        ans++
    }
    return ans
}
```
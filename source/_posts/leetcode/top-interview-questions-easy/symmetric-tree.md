---
title: 对称二叉树
categories:
  - LeetCode
tags:
  - Go
index_img: /img/code.jpg
sticky: -10
date: 2022-01-11 20:28:00
---

# 对称二叉树

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xn7ihv/

https://leetcode-cn.com/problems/symmetric-tree/solution/

## 递归

判断二叉树是否对称，就是判断根节点的左右子节点是否相同，且它们的后续子节点也是对称的。
即：`left.Left == right.Right && left.Right == right.Left`

```go
func isSymmetric(root *TreeNode) bool {
    if root == nil {
        return true
    }
    return helper(root.Left, root.Right)
}

func helper(left *TreeNode, right *TreeNode) bool {
    if left == nil && right == nil {
        return true
    } else if left == nil || right == nil {
        return false
    }
    if left.Val != right.Val {
        return false
    }
    return helper(left.Left, right.Right) && helper(left.Right, right.Left)
}
```

## 迭代

一样的思路，两个结点为一组进栈出栈

```go
func isSymmetric(root *TreeNode) bool {
    if root == nil {
        return true
    }
    
    stack := []*TreeNode{}

    stack = append(stack, root.Left)
    stack = append(stack, root.Right)

    for len(stack) != 0 {
        right := stack[len(stack)-1]
        left := stack[len(stack)-2]
        stack = stack[:len(stack)-2]

        if left == nil && right == nil {
            continue
        }
        if left == nil || right == nil || left.Val != right.Val {
            return false
        }
        
        stack = append(stack, left.Left)
        stack = append(stack, right.Right)

        stack = append(stack, right.Left)
        stack = append(stack, left.Right)
    }

    return true
}
```
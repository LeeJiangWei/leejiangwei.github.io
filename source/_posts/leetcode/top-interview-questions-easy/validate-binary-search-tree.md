---
title: 验证二叉搜索树
categories:
  - LeetCode
  - 初级算法
  - 树
tags:
  - Go
  - 二叉树
index_img: /img/code.jpg
sticky: -10
date: 2022-01-10 20:37:37
---

# 验证二叉搜索树

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xn08xg/

https://leetcode-cn.com/problems/validate-binary-search-tree/solution/

## 递归

注意，验证 BST 不能够只比较左右子节点与自己的值，而必须保证所有左侧的节点都要小于自己（右侧同理）。解决办法就是传入额外的上下界参数。

```go
func isValidBST(root *TreeNode) bool {
    return helper(root, math.MinInt64, math.MaxInt64)
}

func helper(root *TreeNode, lower, upper int) bool {
    if root == nil {
        return true
    }
    if root.Val <= lower || root.Val >= upper {
        return false
    }
    return helper(root.Left, lower, root.Val) && helper(root.Right, root.Val, upper)
}
```

## 遍历后判断

BST 中序遍历的结果是升序列表。

前序遍历、中序遍历、后续遍历都是**深度优先遍历**，可以使用栈来实现（广度优先是队列）。

中序遍历时，先不停地把左子节点压入栈，然后执行访问操作，操作完后出栈。

```go
func isValidBST(root *TreeNode) bool {
    stack := []*TreeNode{}
    inorder := math.MinInt64
    for len(stack) > 0 || root != nil {
        for root != nil {
            stack = append(stack, root)
            root = root.Left
        }
        root = stack[len(stack)-1]
        stack = stack[:len(stack)-1]
        if root.Val <= inorder {
            return false
        }
        inorder = root.Val
        root = root.Right
    }
    return true
}
```

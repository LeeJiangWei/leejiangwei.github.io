---
title: 二叉树的最近公共祖先
categories:
  - LeetCode
  - Fucking
  - 树
tags:
  - Go
  - 二叉树
index_img: /img/code.jpg
sticky: -10
date: 2022-02-20 21:30:09
---

# 二叉树的最近公共祖先

https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/

p 和 q 的公共祖先满足这样一个条件：它的左右子树分别包含了 p 跟 q；或者它就是 p 或 q（两个节点是父子关系）。

```go
func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {
    if root == nil {
        return nil
    }
    // base case: 如果当前节点等于其中一个要找的节点，直接返回
    if root == p || root == q {
        return root
    }

    left := lowestCommonAncestor(root.Left, p, q)
    right := lowestCommonAncestor(root.Right, p, q)

    // 左右子树分别包含了 p 和 q
    // 根据 base case 定义，如果返回值不为 nil，要么包含了 p 要么包含了 q
    if left != nil && right != nil {
        return root
    }
    
    // 左右子树都不包含 p 和 q
    if left == nil && right == nil {
        return nil
    }

    // 返回包含了其中一个（或两个）节点的那棵树
    if left == nil {
        return right
    } else {
        return left
    }
}
```

由于是后序遍历，能够保证第一个满足条件的 root 节点是最深的，也就是最近公共子节点。

**注意这个递归函数的功能跟它名字描述并不完全一致**，需要结合代码理解。

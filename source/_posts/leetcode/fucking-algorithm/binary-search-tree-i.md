---
title: 二叉搜索树 1
categories:
  - LeetCode
  - Fucking
  - 树
tags:
  - Go
  - BST
index_img: /img/code.jpg
sticky: -10
date: 2022-02-20 15:54:09
---

# 二叉搜索树系列 1

这两题主要利用 BST 的一个重要性质：**中序遍历的结果是升序的**。

## 二叉搜索树中第 K 小的元素

https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/

直接进行中序遍历，然后取第 K 个元素就是答案。

```go
func kthSmallest(root *TreeNode, k int) int {
    var a []int
    var traverse func(node *TreeNode)
    traverse = func(node *TreeNode) {
        if node == nil {
            return
        }
        traverse(node.Left)
        a = append(a, node.Val)
        traverse(node.Right)
    }
    traverse(root)
    return a[k-1]
}
```

## 把二叉搜索树转换为累加树

https://leetcode-cn.com/problems/convert-bst-to-greater-tree/

把每个节点更新为所有大于等于它节点的值，可以由 BST 中序遍历为有序列表的性质出发，按右根左的方式进行中序遍历就可以得到降序列表。外部维护一个累加值，遍历到一个节点用这个值更新节点即可。

```go
var sum int

func convertBST(root *TreeNode) *TreeNode {
    sum = 0
    traverse(root)
    return root
}

func traverse(root *TreeNode) {
    if root == nil {
        return
    }
    traverse(root.Right)

    // 记录累加值，并作为树的新值
    sum += root.Val
    root.Val = sum

    traverse(root.Left)
}
```
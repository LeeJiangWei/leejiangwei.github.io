---
title: 二叉树 2
categories:
  - LeetCode
  - Fucking
  - 树
tags:
  - Go
  - 树
index_img: /img/code.jpg
sticky: -10
date: 2022-02-20 09:28:00
---

# 二叉树系列 2

## 最大二叉树

https://leetcode-cn.com/problems/maximum-binary-tree/

```go
func constructMaximumBinaryTree(nums []int) *TreeNode {
    return build(nums, 0, len(nums) - 1)
}

func build(nums []int, low int, high int) *TreeNode {
    if low > high {
        return nil
    }
    index, val := -1, -1
    // 寻找列表中最大值作为根节点
    for i := low; i <= high; i++ {
        if nums[i] > val {
            val = nums[i]
            index = i
        }
    }
    return &TreeNode{val, build(nums, low, index-1), build(nums, index+1, high)}
}
```

## 从前序与中序遍历序列构造二叉树

https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/

一个节点要做的事就是从两个数组中找到根节点，然后递归地生成左右子树。要正确的生成子树，就要将子树对应的数组正确的传入函数中。

可以利用中序数组根节点的位置推算出左子树长度，从而在前序数组中确定位置。

![如何在前序数组中找到正确的子数组](https://labuladong.gitee.io/algo/images/%e4%ba%8c%e5%8f%89%e6%a0%91%e7%b3%bb%e5%88%972/4.jpeg)

```go
func buildTree(preorder []int, inorder []int) *TreeNode {
    return build(preorder, 0, len(preorder) - 1, inorder, 0, len(inorder) - 1)
}

func build(preorder []int, preLow, preHigh int, inorder []int, inLow, inHigh int) *TreeNode {
    if preLow > preHigh {
        return nil
    }

    // 前序数组第一个位置就是根节点
    rootVal := preorder[preLow]

    // 找到根节点再中序数组中的位置
    rootIdx := inLow
    for i := inLow; i <= inHigh; i++ {
        if inorder[i] == rootVal {
            rootIdx = i
        }
    }

    // 计算左子树的大小，以确定如何划分数组递归调用
    leftSize := rootIdx - inLow

    node := &TreeNode{rootVal, nil, nil}
    node.Left = build(preorder, preLow+1, preLow+leftSize, inorder, inLow, rootIdx-1)
    node.Right = build(preorder, preLow+leftSize+1, preHigh, inorder,  rootIdx+1, inHigh)

    return node
}
```

## 从中序与后序遍历序列构造二叉树

https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/

和上一题几乎是一样的思想。

```go
func buildTree(inorder []int, postorder []int) *TreeNode {
    return build(inorder, 0, len(inorder) - 1, postorder, 0, len(postorder) - 1)
}

func build(inorder []int, inLow, inHigh int, postorder []int, postLow, postHigh int) *TreeNode {
    if inLow > inHigh {
        return nil
    }

    // 后序数组最后一个位置就是根节点
    rootVal := postorder[postHigh]

    // 找到根节点在中序数组中的位置
    rootIdx := inLow
    for i := inLow; i <= inHigh; i++ {
        if inorder[i] == rootVal {
            rootIdx = i
        }
    }

    // 计算左子树的大小，以确定如何划分数组递归调用
    leftSize := rootIdx - inLow

    node := &TreeNode{rootVal, nil, nil}
    node.Left = build(inorder, inLow, rootIdx-1, postorder, postLow, postLow+leftSize-1)
    node.Right = build(inorder, rootIdx+1, inHigh, postorder, postLow+leftSize, postHigh-1)

    return node
}
```

## 根据前序和后序遍历构造二叉树

https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-postorder-traversal/

如何通过前后序位置确定某一棵子树的大小？前序数组的第 2 个位置就是左子树的根节点。

在后序数组中寻找这个元素的位置，它的 index 就指示了左子树的大小（在后序数组中，根节点的值位于它所有子树之后）

```go
func constructFromPrePost(preorder []int, postorder []int) *TreeNode {
    return build(preorder, 0, len(preorder)-1, postorder, 0, len(postorder)-1)
}

func build(preorder []int, preLow, preHigh int, postorder []int, postLow, postHigh int) *TreeNode {
    if preLow > preHigh {
        return nil
    }
    // 确保能取到左子树根节点
    if preLow == preHigh {
        return &TreeNode{preorder[preLow], nil, nil}
    }
    rootVal := preorder[preLow]

    leftRootVal := preorder[preLow + 1]
    // 找到左子树根节点在后序数组中的位置
    index := 0
    for i := postLow; i <= postHigh; i++ {
        if postorder[i] == leftRootVal {
            index = i
        }
    }

    // 计算左子树的大小，以确定如何划分数组递归调用
    leftSize := index - postLow + 1

    node := &TreeNode{rootVal, nil, nil}
    node.Left = build(preorder, preLow+1, preLow+leftSize, postorder, postLow, index)
    node.Right = build(preorder, preLow+leftSize+1, preHigh, postorder, index+1, postHigh-1)

    return node
}
```

为什么还原的二叉树可能不唯一？因为在这一句中：

```go
leftRootVal := preorder[preLow + 1]
```

我们假设了前序数组中根节点的后一个值就是左子树根节点，然而左子树如果为空，这个值实际上是右子树根节点，所以不能确定到底是哪一个。

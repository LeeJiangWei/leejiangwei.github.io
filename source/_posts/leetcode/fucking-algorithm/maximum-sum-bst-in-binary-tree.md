---
title: 二叉搜索子树的最大键值和
categories:
  - LeetCode
  - Fucking
  - 树
tags:
  - Go
  - BST
index_img: /img/code.jpg
sticky: -10
date: 2022-02-20 20:57:46
---

# 二叉搜索子树的最大键值和

https://leetcode-cn.com/problems/maximum-sum-bst-in-binary-tree/

## 后序遍历返回多个值

题目需要我们在每一颗子树上判断：是否是 BST，如果是 BST，计算整颗子树的大小。找出最大的 BST。

递归地说，首先需要一个节点判断自己是不是 BST，则需要：它的左右子树都是 BST，且它的值大于左子树的最大值、小于右子树的最小值。如果它是 BST 了，则需要得知左子树的和、右子树的和，才能计算整棵树的大小。

这道题的奥妙就在于这些信息都是可以用后序遍历得到的，因此只进行 1 次后序遍历，但是返回多个值来记录这些信息。

```go
var maxSum int

func maxSumBST(root *TreeNode) int {
    maxSum = 0
    traverse(root)
    return maxSum
}

// [4]int{isBST, min, max, sum}
func traverse(root *TreeNode) [4]int {
    if root == nil {
        // base case: 空节点是 BST，但它没有最小值，也就是说它的最小值需要取一个有效范围以外的值，使得任意值都比它小。最大值同理
        return [4]int{1, math.MaxInt32, math.MinInt32, 0}
    }

    left, right := traverse(root.Left), traverse(root.Right)

    res := [4]int{}
    // 有效 BST 判定
    if left[0] == 1 && right[0] == 1 && root.Val > left[2] && root.Val < right[1] {
        res[0] = 1                              // 这颗子树是 BST
        res[1] = min(left[1], root.Val)         // 这颗子树的最小值
        res[2] = max(right[2], root.Val)        // 这颗子树的最大值
        res[3] = left[3] + right[3] + root.Val  // 这颗子树之和

        maxSum = max(maxSum, res[3])            // 更新全局最大值记录
    } else {
        res[0] = 0  // 如果不是 BST，只需要记录这个值就够了，其他的用不上
    }
    return res
}

func min(a, b int) int {
    if a < b {
        return a
    }
    return b
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```
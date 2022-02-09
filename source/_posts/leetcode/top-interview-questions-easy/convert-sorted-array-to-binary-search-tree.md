---
title: 有序数组转换为平衡二叉树
categories:
  - LeetCode
  - 初级算法
  - 树
tags:
  - Go
index_img: /img/code.jpg
sticky: -10
date: 2022-01-12 20:40:41
---

# 有序数组转换为平衡二叉树

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xninbt/

## 递归

平衡的二叉树，对于「每个节点的左右两个子树的高度差的绝对值不超过 1 」，只要保证所有的内部节点的左右子树高度差在此范围内，就可以保证整棵树的平衡。

对于 BST 而言，每次取数组中间的值作为根节点，左右两边的值为左右子树的节点，然后递归地进行。

```go
func sortedArrayToBST(nums []int) *TreeNode {
    if len(nums) == 0 {
        return nil
    }
    mid := int(len(nums) / 2)
    return &TreeNode{nums[mid], sortedArrayToBST(nums[:mid]), sortedArrayToBST(nums[mid+1:])}
}
```
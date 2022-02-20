---
title: 二叉树 3
categories:
  - LeetCode
  - Fucking
  - 树
tags:
  - Go
  - 二叉树
index_img: /img/code.jpg
sticky: -10
date: 2022-02-20 15:33:36
---

# 寻找重复的子树

https://leetcode-cn.com/problems/find-duplicate-subtrees/

## 二叉树序列化 + 哈希表

遍历二叉树，序列化每一个子节点作为根的子树得到一个代表子树的字符串，如果字符串相同，则对应的子树也是相同的。最后用哈希表来记录这样的子树出现了多少次。

对于这题，子树在序列化时也是需要知道自己子节点的信息，因此还是以后序的框架来写（但是是前序序列化）。

```go
var memo map[string]int
var res []*TreeNode

func findDuplicateSubtrees(root *TreeNode) []*TreeNode {
    memo = make(map[string]int)
    res = make([]*TreeNode, 0)
    serialize(root)
    return res
}

func serialize(root *TreeNode) string {
    if root == nil {
        return "#,"
    }
    s := strconv.Itoa(root.Val) + "," + serialize(root.Left) + serialize(root.Right)  // 序列化
    
    if memo[s] == 1 {
        res = append(res, root)
    }
    memo[s] += 1

    return s
}
```
---
title: 二叉树 1
categories:
  - LeetCode
  - Fucking
  - 树
tags:
  - Go
  - 树
index_img: /img/code.jpg
sticky: -10
date: 2022-02-19 22:15:00
---

# 二叉树系列 1

递归代码，需要把题目的要求细化成**每个节点需要做的事情**。如果一个节点的信息不够完成任务，参数就多加一个需要的节点。

## 翻转二叉树

此题每个节点要完成的事就是交换它的两个子节点。

```go
func invertTree(root *TreeNode) *TreeNode {
    traverse(root)
    return root
}

func traverse(root *TreeNode) {
    if root == nil {
        return
    }
    root.Left, root.Right = root.Right, root.Left
    traverse(root.Left)
    traverse(root.Right)
}
```

## 填充每个节点的下一个右侧节点指针

https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/

### 层次遍历

层次遍历维护一个队列，每取空一次队列为一层

```go
func connect(root *Node) *Node {
    if root == nil {
        return root
    }
	queue := []*Node{root}

    for len(queue) > 0 {
        tmp := queue
        queue = nil
        // 取空队列为同一层
        for i := 0; i < len(tmp); i++ {
            if i < len(tmp) - 1 { 
                tmp[i].Next = tmp[i+1]
            }
            if tmp[i].Left != nil {
                queue = append(queue, tmp[i].Left)
            }
            if tmp[i].Right != nil {
                queue = append(queue, tmp[i].Right)
            }
        }
    }
    return root
}
```

### 递归

由于需要连接两个父节点不同的相邻节点，需要额外的信息来完成。

```go
func connect(root *Node) *Node {
    if root == nil {
        return root
    }
    connectNodes(root.Left, root.Right)
    return root
}

func connectNodes(left, right *Node) {
    if left == nil || right == nil {
        return 
    }
    left.Next = right
    // 连接相同父节点的两个子节点
    connectNodes(left.Left, left.Right)
    connectNodes(right.Left, right.Right)

    // 连接跨越父节点的两个子节点
    connectNodes(left.Right, right.Left)
}
```

## 二叉树展开为链表

https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/

### 遍历

显然链表结果是前序遍历结果，先遍历，然后连成链表即可。

```go
var nodes []*TreeNode

func flatten(root *TreeNode) {
    nodes = make([]*TreeNode, 0)
    traverse(root)
    for i := 0; i < len(nodes) - 1; i++ {
        nodes[i].Left = nil
        nodes[i].Right = nodes[i+1]
    }
}

func traverse(root *TreeNode) {
    if root == nil {
        return
    }
    nodes = append(nodes, root)
    traverse(root.Left)
    traverse(root.Right)
}
```

### 递归

拉成链表可以视为以下 3 步：

1. 将 `root` 的左子树和右子树拉平。
2. 将 `root` 的右子树接到左子树下方
3. 然后将整个左子树作为右子树。

![](https://labuladong.gitee.io/algo/images/%e4%ba%8c%e5%8f%89%e6%a0%91%e7%b3%bb%e5%88%97/2.jpeg)

```go
func flatten(root *TreeNode) {
    if root == nil {
        return
    }

    // 拉平左右子树
    flatten(root.Left)
    flatten(root.Right)

    left, right := root.Left, root.Right
    root.Left, root.Right = nil, left  // 将左子树作为右子树
    
    // 原先的右子树接到当前右子树的末端
    p := root
    for p.Right != nil {
        p = p.Right
    }
    p.Right = right
}
```
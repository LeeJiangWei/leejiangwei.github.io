---
title: 完全二叉树的节点个数
categories:
  - LeetCode
  - Fucking
  - 树
tags:
  - Go
  - 二叉树
index_img: /img/code.jpg
sticky: -10
date: 2022-02-21 10:44:09
---

# 完全二叉树的节点个数

https://leetcode-cn.com/problems/count-complete-tree-nodes/

完全二叉树有个重要的特点：**一棵完全二叉树的两棵子树，至少有一棵是满二叉树**

![](https://labuladong.gitee.io/algo/images/complete_tree/1.jpg)

计算满二叉树的节点个数并不需要遍历所有节点，只需要得知它的深度，直接计算即可。

这段代码先把左右子树都当作满树计算，如果不满足条件再递归到左右子树。

```go
func countNodes(root *TreeNode) int {
    l, r := root, root
    hl, hr := 0, 0
    for l != nil {
        l = l.Left
        hl += 1
    }
    for r != nil {
        r = r.Right
        hr += 1
    }
    if hl == hr {
        return int(math.Pow(2.0, float64(hr))) - 1
    }
    return 1 + countNodes(root.Left) + countNodes(root.Right)
}
```

算法的递归深度就是树的高度 O(logN)，每次递归所花费的时间就是 while 循环，需要 O(logN)，所以总体的时间复杂度是 O(logN*logN)。

如果是普通二叉树，遍历一遍的时间复杂度是 O(N)。

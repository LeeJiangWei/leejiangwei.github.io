---
title: 二叉树的层次遍历
categories:
  - LeetCode
  - 初级算法
  - 树
tags:
  - Go
index_img: /img/code.jpg
sticky: -10
date: 2022-01-12 20:09:04
---

# 二叉树的层次遍历

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xnldjj/

https://leetcode-cn.com/problems/binary-tree-level-order-traversal/solution/er-cha-shu-de-ceng-xu-bian-li-by-leetcode-solution/

## 广度优先搜索

显然应该用广度优先搜索。由于返回的节点需要按层次分组，因此需要知道当前是在哪一层。

**解决方法**：队列中存放一层的结点，每一次直接取一层的出来，并把下一层的放进去。这样实际上每一次都是把队列取空了，然后再填上。取之前先记录队列的元素个数，然后一个循环取出来就完事了。

```go
func levelOrder(root *TreeNode) [][]int {
    queue := []*TreeNode{root}
    result := [][]int{}

    for len(queue) != 0 || root != nil {
        level := len(queue)
        values := []int{}
        for i := 0; i < level; i++ {
            root = queue[0]
            queue = queue[1:]
            if root != nil {
                values = append(values, root.Val)
                queue = append(queue, root.Left)
                queue = append(queue, root.Right)
            }
        }
        if len(values) != 0 {
            result = append(result, values)
        }
    }
    
    return result
}
```

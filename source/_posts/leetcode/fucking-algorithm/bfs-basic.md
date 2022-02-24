---
title: BFS 算法
categories:
  - LeetCode
  - Fucking
  - 暴力搜索算法
tags:
  - Go
index_img: /img/code.jpg
sticky: -10
date: 2022-02-24 20:54:44
---
# BFS 算法

**BFS 找到的路径一定是最短的**, 问题的本质就是让你在一幅「图」中找到从起点 `start` 到终点 `target` 的最近距离。

## 二叉树的最小深度

https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func minDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    queue := make([]*TreeNode, 0)
    queue = append(queue, root)
    depth := 1

    for len(queue) != 0 {
        sz := len(queue)
        for i := 0; i < sz; i++ {
            curr := queue[0]
            queue = queue[1:]

            if curr.Left == nil && curr.Right == nil {
                return depth
            }
            if curr.Left != nil {
                queue = append(queue, curr.Left)
            }
            if curr.Right != nil {
                queue = append(queue, curr.Right)
            }
        }
        depth++
    }

    return depth
}
```

## 打开转盘锁

https://leetcode-cn.com/problems/open-the-lock/

如何穷举一个密码的所有组合？比如说从 `"0000"` 开始，转一次，可以穷举出 `"1000", "9000", "0100", "0900"...` 共 8 种密码。然后，再以这 8 种密码作为基础，对每个密码再转一下，穷举出其他

这就可以抽象成一幅图，每个节点有 8 个相邻的节点，知道起点终点，求最短距离，典型的 BFS 问题了。

```go
func openLock(deadends []string, target string) int {
    queue := make([]string, 0)
    visited := make(map[string]bool)
    // 将死亡密码也加入 visited 中，这样就不会遍历到
    for _, d := range deadends {
        if d == "0000" {
            return -1
        }
        visited[d] = true
    }

    queue = append(queue, "0000")
    visited["0000"] = true

    steps := 0

    for len(queue) != 0 {
        sz := len(queue)
        for i := 0; i < sz; i++ {
            curr := queue[0]
            queue = queue[1:]

            if curr == target {
                return steps
            }

            // 尝试拨动 4 个位置中的一个，作为当前节点的相邻节点
            for j := 0; j < 4; j++ {
                up, down := plusOne(curr, j), minusOne(curr, j)

                // 如果没访问过，就加入队列
                if _, ok := visited[up]; !ok {
                    queue = append(queue, up)
                    visited[up] = true
                }
                if _, ok := visited[down]; !ok {
                    queue = append(queue, down)
                    visited[down] = true
                }
            }
        }
        steps++
    }

    return -1
}

// 将第 j 位转盘加一
func plusOne(s string, j int) string {
    chars := []rune(s)
    if chars[j] == '9' {
        chars[j] = '0'
    } else {
        chars[j] += 1
    }
    return string(chars)
}

// 将第 j 位转盘减一
func minusOne(s string, j int) string {
    chars := []rune(s)
    if chars[j] == '0' {
        chars[j] = '9'
    } else {
        chars[j] -= 1
    }
    return string(chars)
}
```

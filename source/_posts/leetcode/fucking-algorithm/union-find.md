---
title: 并查集
categories:
  - LeetCode
  - Fucking
  - 图
tags:
  - Go
  - 图
index_img: /img/code.jpg
sticky: -10
date: 2022-03-21 18:48:00
---

# 并查集（union-find）

https://labuladong.gitee.io/algo/2/20/41/

## 基本实现

用一个类 UF 来实现并查集。初始化时，将每个节点的父节点设为自己。

`union` 操作将 p 的根节点接到 q 的根节点下面。

`find` 操作查找节点 x 的根节点，且在查找的路上将路径压缩，将 x 直接接到它的爷爷节点（父节点的父节点）下面，这样下来最终树的高度不会超过 3。

```go
type UF struct {
	count   int    // 连通分量数
	parents []int  // 记录每个节点的父节点
}

func NewUF(n int) UF {
	parents := make([]int, n)
	for i := 0; i < n; i++ {
		parents[i] = i
	}
	return UF{n, parents}
}

// Union 将节点 p 和 q 联通
func (u *UF) Union(p, q int) {
	rootP, rootQ := u.Find(p), u.Find(q)
	if rootP == rootQ {
		return
	}

	u.parents[rootP] = rootQ
	u.count -= 1
}

// Find 查找节点 x 的根节点
func (u *UF) Find(x int) int {
	for u.parents[x] != x {
		u.parents[x] = u.parents[u.parents[x]] // 路径压缩
		x = u.parents[x]
	}
	return x
}

// Connected 判断两个节点是否连通
func (u *UF) Connected(p, q int) bool {
	rootP, rootQ := u.Find(p), u.Find(q)
	return rootP == rootQ
}
```

## 被围绕的区域

https://leetcode-cn.com/problems/surrounded-regions/

这个问题有个重要的性质：和**边界上的 0 **连通的 0 不会被替换，而其他的 0 要被替换。

因此，利用并查集，我们将边界上的所有 0 和图一个 `dummy` 根节点相连，然后遍历棋盘内部，将所有的 0 来跟周围的 0 连接。最后判断一个 0 是否要被替换，就看它的根节点是否为 `dummy`，如果是就不用替换。

实现要点：

1. 为 `dummy` 根节点预留位置
2. 将二维数组转为一维的技巧：`(x, y) => x*n + y` （n 为列数）
3. 为了不破坏上面这个映射结构，`dummy` 的位置为数组最末尾
4. 用 `directions` 数组技巧来遍历上下左右四个位置

```go
func solve(board [][]byte)  {
    m, n := len(board), len(board[0])
    uf := NewUF(m * n + 1)

    dummy := m * n

    for row := 0; row < m; row++ {
        if board[row][0] == 'O' {
            uf.Union(row * n, dummy)
        }
        if board[row][n-1] == 'O' {
            uf.Union(row * n + n - 1, dummy)
        }
    }

    for col := 0; col < n; col++ {
        if board[0][col] == 'O' {
            uf.Union(col, dummy)
        }
        if board[m-1][col] == 'O' {
            uf.Union((m-1) * n + col, dummy)
        }
    }

    directions := [][]int{
        {1, 0},
        {0, 1},
        {0, -1},
        {-1, 0},
    }
    
    for i := 1; i < m-1; i++ {
        for j := 1; j < n-1; j++ {
            if board[i][j] == 'O' {
                for _, d := range directions {
                    x := i + d[0]
                    y := j + d[1]
                    if board[x][y] == 'O' {
                        uf.Union(i * n + j, x * n + y)
                    }
                }
            }
        }
    }

    for i := 1; i < m-1; i++ {
        for j := 1; j < n-1; j++ {
            if !uf.Connected(dummy, i * n + j) {
                board[i][j] = 'X'
            }
        }
    }
}

// UF 实现见上文
```

注：这题更好的解法是遍历边界上的 0，用 DFS 或 BFS 将与它们连接的 0 替换成别的一个特殊字符 #，然后替换掉其他所有的 0，再将 # 替换回来。

## 等式方程的可满足性

https://leetcode-cn.com/problems/satisfiability-of-equality-equations/

用 `==` 连接的变量，就是互相连通的关系，可以自然地用并查集解决。具体来说，先处理 `==` 连接的变量，构造连通图，然后处理 `!=` 的变量，检查是否破坏了连通关系。

```go
func equationsPossible(equations []string) bool {
    uf := NewUF(26)
    for _, eq := range equations {
        if eq[1] == '=' {
            uf.Union(int(eq[0] - 'a'), int(eq[3] - 'a'))
        }
    }

    for _, eq := range equations {
        if eq[1] == '!' {
            // 检查是否违反了已有的连通关系
            if uf.Connected(int(eq[0] - 'a'), int(eq[3] - 'a')) {
                return false
            }
        }
    }

    return true
}

// UF 实现见上文
```

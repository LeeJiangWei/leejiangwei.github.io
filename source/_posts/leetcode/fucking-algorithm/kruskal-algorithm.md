---
title: KRUSKAL 最小生成树算法
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

# KRUSKAL 最小生成树算法

https://labuladong.gitee.io/algo/2/20/42/

最小生成树算法：在图中找一棵**包含图中的所有节点**的**树**（无环连通图），且使得边的权重最小。

KRUSKAL 最小生成树算法：
1. 将边按权重从小到大排序。
2. 然后从最小权重的边开始，如果连通它的两个端点不会产生环（用并查集实现，如果它们已经是连通状态了，则连通它们会产生环），则这条边就是最小生成树的一部分；否则不使用这条边。
3. 最终查看并查集的连通分量是否为 1 来判断是不是所有的节点都已被连通。

## 连接所有点的最小费用

https://leetcode-cn.com/problems/min-cost-to-connect-all-points/

先构造出所有可能的边以及权重（曼哈顿距离），然后按权重排序后，直接利用并查集完成。

```go
func minCostConnectPoints(points [][]int) int {
    n := len(points)

    edges := make([][3]int, 0)
    for i := 0; i < n; i++ {
        for j := i + 1; j < n; j++ {
            dist := abs(points[i][0] - points[j][0]) + abs(points[i][1] - points[j][1])
            edges = append(edges, [3]int{i, j, dist})
        }
    } 
    
    sort.Slice(edges, func(i, j int) bool {
        return edges[i][2] < edges[j][2]
    })

    cost := 0
    uf := NewUF(n)

    for _, edge := range edges {
        // 如果两个端点没连通，就连接它们并加入最小生成树中
        if !uf.Connected(edge[0], edge[1]) {
            uf.Union(edge[0], edge[1])
            cost += edge[2]
        }
    }

    return cost
}

func abs(x int) int {
    if x < 0 {
        return -x
    }
    return x
}
```

## 复杂度

对于一副节点个数为 `V`，边数为 `E` 的图：

空间复杂度：装载 `E` 条边和并查集装载的 `V` 个节点的空间复杂度为 `O(E + V)`。

时间复杂度：主要是对 `E` 条边排序的复杂度，为 `O(E * logE)`；并查集的合并和查找操作都是 `O(1)`，操作 `E` 次为 `O(E)`。总体复杂度为 `O(E * logE)`。

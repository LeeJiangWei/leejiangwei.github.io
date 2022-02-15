---
title: 加油站
categories:
  - LeetCode
  - Fucking
  - 动态规划
tags:
  - Go
  - 动态规划
  - 贪心算法
index_img: /img/code.jpg
sticky: -10
date: 2022-02-15 15:06:00
---

# 加油站

https://leetcode-cn.com/problems/gas-station/

## 画图法

我们需要判断这个环形数组中是否能够找到一个起点 start，使得从这个起点开始，`gas` 和 `cost` 的累加和一直大于等于 0。

![假设把 0 作为起点](https://labuladong.gitee.io/algo/images/%e8%80%81%e5%8f%b8%e6%9c%ba/3.jpeg)

可以看出其中有一段路 `sum` 小于 0 了，是不行的。如果把图像的最低点作为起点，就能让图像整体平移上去，保证了一直 大于等于 0。

![](https://labuladong.gitee.io/algo/images/%e8%80%81%e5%8f%b8%e6%9c%ba/4.jpeg)

```go
func canCompleteCircuit(gas []int, cost []int) int {
    n := len(gas)
    sum := 0
    minSum := 0
    start := 0

    for i := 0; i < n; i++ {
        sum += gas[i] - cost[i]
        if sum < minSum {
            start = i + 1
            minSum = sum
        }
    }

    if sum < 0 {
        return -1
    }
    
    if start == n {
        return 0
    }
    return start
}
```

## 贪心法

基于以下结论：**如果选择站点 i 作为起点「恰好」无法走到站点 j，那么 i 和 j 中间的任意站点 k 都不可能作为起点。**

证明：因为如果恰好无法走到站点 j，则对于途中任意 `i < k < j`，油箱量 `tank[k]` 都要 > 0，而如果将 `k` 作为起点，则 `tank[k] == 0`，无法满足条件。

根据这个条件，我们可以一次遍历起点，从 0 开始，如果发现哪里走不动了，说明这个点以前的都不可能是起点。

```go
func canCompleteCircuit(gas []int, cost []int) int {
    n := len(gas)
    sum, tank, start:= 0, 0, 0

    for i := 0; i < n; i++ {
        sum += gas[i] - cost[i]
        tank += gas[i] - cost[i]
        if tank < 0 {
            // 重设下一个点为起点
            tank = 0
            start = i + 1
        }
    }

    if sum < 0 {
        return -1
    }

    if start == n {
        return 0
    }
    return start
}
```
---
title: BFS 解决滑动谜题
categories:
  - LeetCode
  - Fucking
  - 暴力搜索算法
tags:
  - Go
index_img: /img/code.jpg
sticky: -10
date: 2022-02-25 09:54:44
---
# BFS 解决滑动谜题

https://leetcode-cn.com/problems/sliding-puzzle/

对于这种计算最小步数的问题，我们就要敏感地想到 BFS 算法。**如何穷举出 `board` 当前局面下可能衍生出的所有局面** ？这就简单了，看数字 0 的位置呗，和上下左右的数字进行交换就行了：

![](https://labuladong.gitee.io/algo/images/sliding_puzzle/3.jpeg)

每次先找到数字 0，然后和周围的数字进行交换，形成新的局面加入队列…… 当第一次到达 `target` 时，就得到了赢得游戏的最少步数。由于棋盘只是 2 X 3 的小尺寸，可以将棋盘压缩为一个一维的字符串，这样方便传递参数和存入哈希表（记录已访问节点）。

为了能正确地移动数字 0，需要将一维字符串中对于二维的邻居手动写出来。

![](https://labuladong.gitee.io/algo/images/sliding_puzzle/4.jpeg)

```go
func slidingPuzzle(board [][]int) int {
    target := "123450"
    neighbor := [][]int{
        {1, 3},
        {0, 4, 2},
        {1, 5},
        {0, 4},
        {3, 1, 5},
        {4, 2},
    }

    sb := strings.Builder{}
    for _, i := range board {
        for _, v := range i {
            sb.WriteString(strconv.Itoa(v))
        }
    }
    start := sb.String()

    /******* BFS 算法框架开始 *******/
    queue := make([]string, 0)
    visited := make(map[string]bool)

    queue = append(queue, start)
    visited[start] = true

    steps := 0
    for len(queue) != 0 {
        sz := len(queue)
        for i := 0; i < sz; i++ {
            currState := queue[0]
            queue = queue[1:]

            if currState == target {
                return steps
            }

            zeroIdx := 0
            for i, c := range currState {
                if c == '0' {
                    zeroIdx = i
                    break
                }
            }

            // 尝试将 0 交换到每一个邻居节点上，形成新的棋盘状态
            for _, n := range neighbor[zeroIdx] {
                newState := moveZero(currState, zeroIdx, n)
                if _, ok := visited[newState]; !ok {
                    queue = append(queue, newState)
                    visited[newState] = true
                }
            }
        }
        steps++
    }
    return -1
}

func moveZero(state string, from, to int) string {
    chars := []rune(state)
    chars[from], chars[to] = chars[to], chars[from]
    return string(chars)
}
```

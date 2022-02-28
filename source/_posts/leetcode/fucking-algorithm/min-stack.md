---
title: 最小栈
categories:
  - LeetCode
  - Fucking
  - 数据结构设计
tags:
  - Go
  - 栈
index_img: /img/code.jpg
sticky: -10
date: 2022-02-28 17:30:00
---
# 最小栈

https://leetcode-cn.com/problems/min-stack/

## 需求分析

1. 需要完成栈的功能（压入、弹出），因此底层数据结构是栈
2. 需要知道最小的元素；如果最小元素被弹出，需要知道第二小的元素

需求 2 是难点，由于需要知道第二小的元素，不能简单地用一个变量来记录最小值。

## 实现

使用 2 个栈。栈 A 是一般的栈，储存所有元素。使用一个辅助栈 B，存储栈 A 中所有 **非严格降序** 的元素，这样栈 B 顶就是最小的元素。

新元素 `x` 入栈时，如果 `x <= B.top()` (x 小于 B 的栈顶），将 x 也压入栈 B，这样就保证了最小元素的更新。

要弹出栈 A 的元素时，检查这个元素是否是跟栈 B 顶的 元素相等。如果相等，栈 B 也需要弹出，这样栈 B 的栈顶就是第二小的元素了（有可能大小不变）。

由于 B 储存的是 **非严格降序** 的元素，即使 A 和 B 都压入了多个一样大小的最小值，也能保证弹出栈顶后维持正确的最小元素。

```go
type MinStack struct {
    stack []int
    descendingStack []int
}


/** initialize your data structure here. */
func Constructor() MinStack {
    return MinStack{make([]int, 0), make([]int, 0)}
}


func (this *MinStack) Push(x int)  {
    this.stack = append(this.stack, x)
  
    m := len(this.descendingStack)
    if m == 0 || x <= this.descendingStack[m-1] {
        this.descendingStack = append(this.descendingStack, x)
    }
}


func (this *MinStack) Pop()  {
    n, m := len(this.stack), len(this.descendingStack)
    if n == 0 {
        return
    }

    x := this.stack[n-1]
    this.stack = this.stack[:n-1]

    if x == this.descendingStack[m-1] {
        this.descendingStack = this.descendingStack[:m-1]
    }
}


func (this *MinStack) Top() int {
    return this.stack[len(this.stack)-1]
}


func (this *MinStack) GetMin() int {
    return this.descendingStack[len(this.descendingStack)-1]
}
```

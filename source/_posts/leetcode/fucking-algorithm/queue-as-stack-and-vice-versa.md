---
title: 队列实现栈以及栈实现队列
categories:
  - LeetCode
  - Fucking
  - 数据结构设计
tags:
  - Go
  - 栈
  - 队列
index_img: /img/code.jpg
sticky: -10
date: 2022-02-25 20:48:08
---

# 队列实现栈以及栈实现队列

## 用栈实现队列

https://leetcode-cn.com/problems/implement-queue-using-stacks/

两个栈来回倒，其中一个的栈顶就是最后进入的，push 的时候压进这个栈；另一个栈的栈顶是最先进入的，pop 和 peek 的时候弹出这个栈顶。

```go
type MyQueue struct {
    s1, s2 []int
}


func Constructor() MyQueue {
    return MyQueue{make([]int, 0), make([]int, 0)}
}


func (this *MyQueue) Push(x int)  {
    // 弹出所有 s2 元素并压入 s1
    for len(this.s2) != 0 {
        top := this.s2[len(this.s2)-1]
        this.s2 = this.s2[:len(this.s2)-1]
        this.s1 = append(this.s1, top)
    }

    // 压入 x
    this.s1 = append(this.s1, x)
}


func (this *MyQueue) Pop() int {
    // 弹出所有 s1 元素并压入 s2
    for len(this.s1) != 0 {
        top := this.s1[len(this.s1)-1]
        this.s1 = this.s1[:len(this.s1)-1]
        this.s2 = append(this.s2, top)
    }

    // s2 此时已是先进先出顺序。弹出 s2 栈顶元素
    top := this.s2[len(this.s2)-1]
    this.s2 = this.s2[:len(this.s2)-1]
    return top
}


func (this *MyQueue) Peek() int {
    // 弹出所有 s1 元素并压入 s2
    for len(this.s1) != 0 {
        top := this.s1[len(this.s1)-1]
        this.s1 = this.s1[:len(this.s1)-1]
        this.s2 = append(this.s2, top)
    }

    // s2 此时已是先进先出顺序。返回 s2 栈顶元素
    return this.s2[len(this.s2)-1]
}


func (this *MyQueue) Empty() bool {
    return len(this.s1) + len(this.s2) == 0
}
```

## 用队列实现栈

https://leetcode-cn.com/problems/implement-stack-using-queues/

只用一个队列即可。栈顶元素就是队尾，入队时记下来。弹出栈顶元素时，将其他元素全部出队并重新入队，注意记住下一个栈顶元素。

```go
type MyStack struct {
    top int
    q []int
}


func Constructor() MyStack {
    return MyStack{}
}


func (this *MyStack) Push(x int)  {
    this.q = append(this.q, x)
    this.top = x  // 顺便记录栈顶
}


func (this *MyStack) Pop() int {
    sz := len(this.q) - 2
    for sz > 0 {
        // 无关元素重新重新排队
        front := this.q[0]
        this.q = this.q[1:]
        this.q = append(this.q, front)
        sz--
    }
    // 当前栈顶的前一个元素是下一个栈顶，单独拎出来记住
    nextTop := this.q[0]
    this.q = this.q[1:]
    this.q = append(this.q, nextTop)

    this.top = nextTop

    // 当前栈顶元素出队并返回
    top := this.q[0]
    this.q = this.q[1:]
    return top
}


func (this *MyStack) Top() int {
    return this.top
}


func (this *MyStack) Empty() bool {
    return len(this.q) == 0
}
```

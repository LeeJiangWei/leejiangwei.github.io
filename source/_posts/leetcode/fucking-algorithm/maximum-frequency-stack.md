---
title: 最大频率栈
categories:
  - LeetCode
  - Fucking
  - 数据结构设计
tags:
  - Go
  - 栈
  - 哈希表
index_img: /img/code.jpg
sticky: -10
date: 2022-02-24 10:00:00
---

# 最大频率栈

https://leetcode-cn.com/problems/maximum-frequency-stack/

需求分析：

1. 每次 `pop` 时，必须要知道频率最高的元素是什么：使用一个变量 `maxFreq` 记录最大频率
2. 每次 `push` 时，要知道对应元素的频率：使用哈希表 `valToFreq` 记录频率
3. 如果频率最高的元素有多个，还得知道哪个是最近 `push` 进来的元素是哪个：使用哈希表+栈 `freqToVal` 记录相同频率的元素，且栈储存了时间信息

![](https://labuladong.gitee.io/algo/images/%e9%ab%98%e9%a2%91%e6%a0%88/1.gif)

```go
type FreqStack struct {
    maxFreq   int
    valToFreq map[int]int
    freqToVal map[int][]int
}


func Constructor() FreqStack {
    VF := make(map[int]int)
    FV := make(map[int][]int)
    return FreqStack{0, VF, FV}
}


func (this *FreqStack) Push(val int)  {
    this.valToFreq[val] += 1  // val 对应频次加一（如果没有的话默认是 0）
    f := this.valToFreq[val]
    this.freqToVal[f] = append(this.freqToVal[f], val)  // 在更新后频次的表中加入
    this.maxFreq = max(this.maxFreq, f)  // 更新最大频次
}


func (this *FreqStack) Pop() int {
    // 最大频次的表中最后一个元素就是最近加入的，将它弹出
    p := this.freqToVal[this.maxFreq][len(this.freqToVal[this.maxFreq])-1]
    this.freqToVal[this.maxFreq] = this.freqToVal[this.maxFreq][:len(this.freqToVal[this.maxFreq])-1]  //出栈
    
    // 如果这个频次表变成空的了，就删除它，且最大频率减一
    if len(this.freqToVal[this.maxFreq]) == 0 {
        delete(this.freqToVal, this.maxFreq)
        this.maxFreq--
    }

    this.valToFreq[p]--  // 更新被弹出节点的频率

    return p
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```
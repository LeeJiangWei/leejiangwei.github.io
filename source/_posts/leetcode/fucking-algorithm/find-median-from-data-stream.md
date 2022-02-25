---
title: 数据流的中位数
categories:
  - LeetCode
  - Fucking
  - 数据结构设计
tags:
  - Go
  - 优先级队列
index_img: /img/code.jpg
sticky: -10
date: 2022-02-25 16:08:00
---
# 数据流的中位数

https://leetcode-cn.com/problems/find-median-from-data-stream/

用两个优先级队列实现，一个 `MinHeap` 存放比中位数小的（最大堆），另一个 `MaxHeap` 存放比中位数大的（最小堆），这样两个堆的堆顶就是最靠近中位数的两个数。

只要能保证两个优先级队列**大小最多不相差超过 1**，中位数就位于数量更多的那堆顶、或是两个堆顶的平均数。

插入时，需要维持 `MinHeap` 的所有元素都比 `MaxHeap` 的小，因此不能简单比较待插入元素和两个堆顶元素大小就插入。

如果想插入 `MaxHeap`，要先插入到`MinHeap` 中，然后从 `MinHeap` 中弹出堆顶（最大的）元素，再插入到 `MaxHeap` 中。

## 实现

这里的 Golang 实现中，直接使用 `sort.IntSlice` 作为底层的队列，这个类型已经实现好了排序的接口，且是按从小到大排（因此它是一个最小堆）。

为了减少代码量，最大堆  `MinHeap` 也复用这个类型，让元素在进出 `MinHeap` 的时候都取反，它就相当于变成了个最大堆（里面存的都是负数，堆顶是最小的负数，取反后变成最大的）。

```go

import "sort"

type MedianFinder struct {
    MinHeap, MaxHeap *Heap
}


func Constructor() MedianFinder {
    return MedianFinder{ &Heap{}, &Heap{} }
}


func (this *MedianFinder) AddNum(num int)  {
    if this.MinHeap.Len() >= this.MaxHeap.Len() {
        heap.Push(this.MinHeap, -num)
        heap.Push(this.MaxHeap, -heap.Pop(this.MinHeap).(int))
    } else {
        heap.Push(this.MaxHeap, num)
        heap.Push(this.MinHeap, -heap.Pop(this.MaxHeap).(int))
    }
}


func (this *MedianFinder) FindMedian() float64 {
    if this.MinHeap.Len() < this.MaxHeap.Len() {
        return float64(this.MaxHeap.IntSlice[0])
    }
    if this.MinHeap.Len() > this.MaxHeap.Len() {
        return float64(-this.MinHeap.IntSlice[0])
    }

    return float64(this.MaxHeap.IntSlice[0] - this.MinHeap.IntSlice[0]) / 2
}


/**
 * Your MedianFinder object will be instantiated and called as such:
 * obj := Constructor();
 * obj.AddNum(num);
 * param_2 := obj.FindMedian();
 */

// sort.IntSlice 已经给 []int 实现了 sort 相关的那三个接口
type Heap struct { sort.IntSlice }  // 直接作为匿名字段嵌入

func (h *Heap) Push(x interface{}) {
    h.IntSlice = append(h.IntSlice, x.(int))
}

func (h *Heap) Pop() interface{} {
    old := h.IntSlice
    n := len(old)
    x := old[n-1]
    h.IntSlice = old[:n-1]
    return x
}
```

## 参考

https://pkg.go.dev/sort#IntSlice

https://pkg.go.dev/container/heap

---
title: 前 K 个高频元素
categories:
  - LeetCode
  - Fucking
  - 数组
tags:
  - Go
  - 数组
index_img: /img/code.jpg
sticky: -10
date: 2022-04-09 23:10:00
---

# 前 K 个高频元素

https://leetcode-cn.com/problems/top-k-frequent-elements/

## 快速排序

先统计元素出现频率，然后对数组进行排序。快速排序平均时间复杂度为 O(nlogn)。

```go
func topKFrequent(nums []int, k int) []int {
    result := make([]int, 0)
    valToFreq := make(map[int]int)

    for _, n := range nums {
        valToFreq[n]++
    }

    for val := range valToFreq {
        result = append(result, val)
    }

    sort.Slice(result, func(i, j int) bool {
        return valToFreq[result[i]] > valToFreq[result[j]]
    })

    return result[:k]
}
```

## 堆排序：最小堆

维护一个频率的最小堆，并保持里面的元素最多只有 k 个。如果插入元素超过 k 个了，就弹出堆顶，最终留下来的就是 k 个频率最大的。由于保持了堆中元素最多为 k 个，插入和弹出的复杂度都是 O(logk)，建堆时插入 n 次，总体复杂度为 O(nlogk)。

```go
var valToFreq map[int]int

func topKFrequent(nums []int, k int) []int {
    result := make([]int, k)
    valToFreq = make(map[int]int)

    for _, n := range nums {
        valToFreq[n]++
    }

    h := MaxHeap{}

    for val := range valToFreq {
        heap.Push(&h, val)
        if h.Len() > k {
            heap.Pop(&h)
        }
    }

    for i := 0; i < k; i++ {
        x := heap.Pop(&h).(int)
        result[k-1-i] = x
    }

    return result
}


type MaxHeap []int

func (h MaxHeap) Len() int {
    return len(h)
}

func (h MaxHeap) Less(i, j int) bool {
    return valToFreq[h[i]] < valToFreq[h[j]]  // 注意：是最小堆
}

func (h MaxHeap) Swap(i, j int) {
    h[i], h[j] = h[j], h[i]
}

func (h *MaxHeap) Push(x interface{}) {
    *h = append(*h, x.(int))
}

func (h *MaxHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}
```

## 堆排序：最大堆

直接调包在已有元素上建立出现频率的最大堆（而**不是**一个一个插入），则建堆的时间复杂度只有 O(n)*，然后再弹出 k 个元素即可。每次弹出的复杂度为 O(logn)，则总体复杂度为 O(klogn)。

**注：使用 Floyd 建堆算法。*

```go
var valToFreq map[int]int

func topKFrequent(nums []int, k int) []int {
    result := make([]int, k)
    valToFreq = make(map[int]int)

    for _, n := range nums {
        valToFreq[n]++
    }

    h := MaxHeap{}

    for val := range valToFreq {
        h = append(h, val)
    }

    heap.Init(&h)  // 建堆

    for i := 0; i < k; i++ {
        x := heap.Pop(&h).(int)
        result[i] = x
    }

    return result
}


type MaxHeap []int

func (h MaxHeap) Len() int {
    return len(h)
}

func (h MaxHeap) Less(i, j int) bool {
    return valToFreq[h[i]] > valToFreq[h[j]]  // 注意：是最大堆
}

func (h MaxHeap) Swap(i, j int) {
    h[i], h[j] = h[j], h[i]
}

func (h *MaxHeap) Push(x interface{}) {
    *h = append(*h, x.(int))
}

func (h *MaxHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}
```

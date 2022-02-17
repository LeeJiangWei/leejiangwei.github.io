---
title: 合并K个升序链表
categories:
  - LeetCode
  - Fucking
  - 链表
tags:
  - Go
  - 链表
index_img: /img/code.jpg
sticky: -10
date: 2022-02-17 15:32:00
---

# 合并K个升序链表

https://leetcode-cn.com/problems/merge-k-sorted-lists/

## 最小堆

把所有链表的头节点放入最小堆，每次从中取出最小的那个。

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func mergeKLists(lists []*ListNode) *ListNode {
    h := new(minHeap)
    for i := 0; i < len(lists); i++ {
        if lists[i] != nil {
            heap.Push(h, lists[i])
        }
    }

    head := new(ListNode)
    curr := head

    for h.Len() > 0 {
        temp := heap.Pop(h).(*ListNode)
        if temp.Next != nil {
            heap.Push(h, temp.Next)
        }
        curr.Next = temp
        curr = curr.Next
    }

    return head.Next
}

// 最小堆实现
type minHeap []*ListNode

func (h minHeap) Len() int           { return len(h) }
func (h minHeap) Less(i, j int) bool { return h[i].Val < h[j].Val }
func (h minHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }

func (h *minHeap) Push(x interface{}) {
    *h = append(*h, x.(*ListNode))
}

func (h *minHeap) Pop() interface{} {
	old := *h
	n := len(old)
	x := old[n-1]
	*h = old[0 : n-1]
	return x
}
```

最小堆的实现参考：
+ https://pkg.go.dev/container/heap
+ https://books.studygolang.com/The-Golang-Standard-Library-by-Example/chapter03/03.3.html
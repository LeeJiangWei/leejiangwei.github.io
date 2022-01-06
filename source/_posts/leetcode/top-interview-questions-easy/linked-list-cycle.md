---
title: 环形链表
categories:
  - LeetCode
  - 初级算法
  - 链表
tags:
  - Go
  - 链表
  - 哈希表
index_img: /img/code.jpg
sticky: -10
date: 2022-01-06 17:29:44
---

# 环形链表

## 哈希表

使用哈希数据类型来记录节点是否存在过，key 可以是节点的地址。

```go
func hasCycle(head *ListNode) bool {
    var m = make(map[*ListNode]bool)

    curr := head

    for curr != nil {
        if m[curr] == true {
            return true
        }
        m[curr] = true
        curr = curr.Next
    }
    return false
}
```

+ `map` 类型要用 make 来初始化，如果不初始化它的值就是 `nil`
+ 函数参数中，`a &int` 是按引用传递，`a *int` 是传递了一个指针，这个 `a` 的类型就是 `*int` （int 指针），指针类型可以用作 `map` 的 key。
+ 因此在这个代码中，key 是 `*ListNode` 类型的，直接用变量 `curr` 就可以索引这个 map。

## 快慢指针

如果链表有环，快慢指针最终会相遇。

```go
func hasCycle(head *ListNode) bool {
    fast, slow := head, head
    for fast != nil && fast.Next != nil {
        fast = fast.Next.Next
        slow = slow.Next
        if fast == slow {
           return true 
        }
    }
    return false
}
```
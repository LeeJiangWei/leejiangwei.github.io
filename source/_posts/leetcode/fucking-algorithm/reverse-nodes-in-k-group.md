---
title: K 个一组翻转链表
categories:
  - LeetCode
  - Fucking
  - 链表
tags:
  - Go
  - 链表
index_img: /img/code.jpg
sticky: -10
date: 2022-02-17 21:08:00
---

# K 个一组翻转链表

https://leetcode-cn.com/problems/reverse-nodes-in-k-group/

## 递归

先反转前面 k 个，然后反转接着的 k 个，容易看出完全独立的子问题关系。先反转完前 K 个后，调用一样的函数反转后续的链表。base case 是链表元素不足 K 个，不进行反转。

![反转前两个后，如何将链表正确地拼在一起](https://labuladong.gitee.io/algo/images/kgroup/6.jpg)

```go
func reverseKGroup(head *ListNode, k int) *ListNode {
    a , b := head, head
    // 数链表元素个数是否足够反转
    for i := 0; i < k; i++ {
        if b == nil {
            return a
        }
        b = b.Next
    }
    reversed := reverseInterval(a, b)
    a.Next = reverseKGroup(b, k)  // 将 a 接到反转后的链表头上
    return reversed
}

func reverseInterval(a, b *ListNode) *ListNode {
    var prev *ListNode
    curr := a

    for curr != b {
        next := curr.Next
        curr.Next = prev
        prev = curr
        curr = next
    }
    return prev
}
```

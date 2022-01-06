---
title: 反转链表
categories:
  - LeetCode
  - 初级算法
  - 链表
tags:
  - Go
  - 链表
index_img: /img/code.jpg
sticky: -10
date: 2022-01-06 14:26:14
---

# 反转链表

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xnnhm6/

https://leetcode-cn.com/problems/reverse-linked-list/solution/

## 迭代

迭代进行中需要储存好前一个节点、当前节点、下一节点的指针，以便操作。

```go
func reverseList(head *ListNode) *ListNode {
    var prev *ListNode
    curr := head

    for curr != nil {
        next := curr.Next
        curr.Next = prev
        prev = curr
        curr = next

        // 或者利用平行赋值简写为一行
        // curr.Next, prev, curr = prev, curr, curr.Next
    }

    return prev
}
```

## 递归

主要在于如何返回反转后的头节点，答案就是记住它（变量 `p`），然后层层直接返回。

其中递归所需的空间复杂度是 O(n)。

```go
func reverseList(head *ListNode) *ListNode {
    if head == nil || head.Next == nil {
        return head
    }
    p := reverseList(head.Next)

    head.Next.Next = head
    head.Next = nil

    return p
}
```
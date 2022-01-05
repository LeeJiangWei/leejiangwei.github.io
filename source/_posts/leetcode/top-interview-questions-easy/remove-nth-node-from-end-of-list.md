---
title: 删除链表的倒数第N个节点
categories:
  - LeetCode
  - 初级算法
  - 链表
tags:
  - Go
  - 链表
  - 双指针
  - 栈
index_img: /img/code.jpg
sticky: -10
date: 2022-01-05 20:04:48
---

# 删除链表的倒数第N个节点

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xn2925/

https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/solution/

## 获取链表长度

第一次遍历走到链表结尾，算出链表长度。第二次遍历到删除的节点。

```go
func removeNthFromEnd(head *ListNode, n int) *ListNode {
    // 计算链表长度
    counter := 0
    curr := head
    for curr != nil {
        counter ++
        curr = curr.Next
    }

    // 移除链表头的情况
    if counter == n {
        return head.Next
    }

    curr = head
    for i := 0; i < counter - n - 1; i++ {
        curr = curr.Next
    }
    curr.Next = curr.Next.Next

    return head
}
```

实际上给链表加一个假的头可以避免处理删除节点为头节点的情况。

```go
func removeNthFromEnd(head *ListNode, n int) *ListNode {
    dummyHead := &ListNode{0, head} // 加入假的头

    // 计算链表长度
    counter := 0
    curr := dummyHead
    for curr != nil {
        counter ++
        curr = curr.Next
    }

    curr = dummyHead
    for i := 0; i < counter - n - 1; i++ {
        curr = curr.Next
    }
    curr.Next = curr.Next.Next

    return dummyHead.Next
}
```

## 双指针

由于我们需要找到倒数第 n 个节点，因此我们可以使用两个指针 first 和 second 同时对链表进行遍历，并且 first 比 second 超前 n 个节点。当 first 遍历到链表的末尾时，second 就恰好处于倒数第 n 个节点。

```go
func removeNthFromEnd(head *ListNode, n int) *ListNode {
    dummyHead := &ListNode{0, head}

    first := dummyHead
    // 移动 first 指针，使得它超前 second 指针 n 个节点
    for i := 0; i < n; i++ {
        first = first.Next
    }
    second := dummyHead

    // 同时移动两个指针，直至 first 指针到最后一个节点，此时 second 指针就是要删除节点的前驱节点
    for first.Next != nil {
        first = first.Next
        second = second.Next
    }

    second.Next = second.Next.Next // 执行删除

    return dummyHead.Next
}
```


## 栈

所有节点依次入栈，然后弹出第 n 个节点就是要删除的节点，且此时栈顶正好是它前一个节点。

```go
func removeNthFromEnd(head *ListNode, n int) *ListNode {
    dummy := &ListNode{0, head}
    first, second := head, dummy
    for i := 0; i < n; i++ {
        first = first.Next
    }
    for ; first != nil; first = first.Next {
        second = second.Next
    }
    second.Next = second.Next.Next
    return dummy.Next
}
```

在这个实现中，本质上是用的数组作为栈来储存遍历过的节点，第二次就无需再从头遍历。属于是用空间换时间。

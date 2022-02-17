---
title: 反转链表
categories:
  - LeetCode
  - Fucking
  - 链表
tags:
  - Go
  - 链表
index_img: /img/code.jpg
sticky: -10
date: 2022-02-17 19:20:00
---

# 环形链表

https://leetcode-cn.com/problems/reverse-linked-list/

https://leetcode-cn.com/problems/reverse-linked-list-ii/

## 反转链表

迭代方法：记住当前处理节点的上一个节点和下一个节点，完成反转操作

```go
func reverseList(head *ListNode) *ListNode {
    var prev *ListNode
    curr := head

    for curr != nil {
        next := curr.Next

        // 反转当前链表的指针
        curr.Next = prev

        // 前进指针，进行下一次处理
        prev = curr
        curr = next
    }

    return prev
}
```

递归方法

```go
func reverseList(head *ListNode) *ListNode {
    if head == nil || head.Next == nil {
        return head
    }

    reversed := reverseList(head.Next)

    head.Next.Next = head  // 反转链表的尾巴设为 head
    head.Next = nil        // head 变成了尾巴，它的 next 应该是 nil

    return reversed        // 反转后链表的头部
}
```

## 反转链表的前 N 个元素

![](https://labuladong.gitee.io/algo/images/%e5%8f%8d%e8%bd%ac%e9%93%be%e8%a1%a8/6.jpg)

需要注意的是，反转前 N 个元素后，要将反转后的链表尾巴接到未反转的部分上（如果是全反转，设为 nil 即可），因此需要记录下未反转的部分是哪里。

显然，N = 1 时，它的下一个节点就是未反转的部分，因此在此 base case 上记录下未反转的头部，然后反转后将反转后的尾巴接上去。

```go
var successor *ListNode

func reverseN(head *ListNode, n int) *ListNode {
    if n == 1 {
        successor = head.Next  // 记录未反转的部分
        return head
    }
    
    reversed := reverseN(head.Next, n - 1)

    head.Next.Next = head
    head.Next = successor   // 反转后的尾巴接上未反转的部分
    return reversed
}
```

## 反转链表的中间的几个元素

反转从 left 到 right 的中间几个元素，相当于从 left 开始，反转前 N 个元素。因此只要加一个判断，如果当前没到 left，就递归到下一个节点，直到当前从 left 开始，然后执行反转前 N 个元素的操作即可。

```go
var successor *ListNode

func reverseBetween(head *ListNode, left int, right int) *ListNode {
    if left == 1 {
        return reverseN(head, right)
    }
    head.Next = reverseBetween(head.Next, left-1, right-1)
    return head
}

func reverseN(head *ListNode, n int) *ListNode {
    if n == 1 {
        successor = head.Next
        return head
    }
    
    reversed := reverseN(head.Next, n - 1)

    head.Next.Next = head
    head.Next = successor
    return reversed
}
```
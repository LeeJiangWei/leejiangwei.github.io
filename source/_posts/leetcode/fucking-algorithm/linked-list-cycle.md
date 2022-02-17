---
title: 环形链表
categories:
  - LeetCode
  - Fucking
  - 链表
tags:
  - Go
  - 链表
index_img: /img/code.jpg
sticky: -10
date: 2022-02-17 16:09:00
---

# 环形链表

https://leetcode-cn.com/problems/linked-list-cycle/

https://leetcode-cn.com/problems/linked-list-cycle-ii/

## 检测是否存在环形链表

快慢指针

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

## 环形链表的起点

快慢指针相遇后，将其中一个指回开头，以同样的速度前进，再次相遇的地方就是环的起点

![](https://labuladong.gitee.io/algo/images/%e5%8f%8c%e6%8c%87%e9%92%88/2.jpeg)

```go
func detectCycle(head *ListNode) *ListNode {
    fast, slow := head, head
    for fast != nil && fast.Next != nil {
        fast = fast.Next.Next
        slow = slow.Next
        if fast == slow {
            // 开始寻找环起点
            fast = head 
            for fast != slow {
                fast = fast.Next
                slow = slow.Next
            }
            return fast
        }
    }
    return nil
}
```
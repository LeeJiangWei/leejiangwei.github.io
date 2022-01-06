---
title: 合并两个有序链表
categories:
  - LeetCode
  - 初级算法
  - 链表
tags:
  - Go
  - 链表
index_img: /img/code.jpg
sticky: -10
date: 2022-01-06 15:31:16
---

# 合并两个有序链表

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xnnbp2/

https://leetcode-cn.com/problems/merge-two-sorted-lists/solution/

## 迭代

哪个表的表头更小，就连上那个节点，并把对应的表头向后移动，直到有一方变成空的位置，连上另一个非空的表，完事。

dummy head 在这里很好用，这种需要返回链表头的，记得保存 head 变量用于返回。

```go
func mergeTwoLists(list1 *ListNode, list2 *ListNode) *ListNode {
    head := &ListNode{-999, nil}
    curr := head
    
    for list1 != nil && list2 != nil {
        if list1.Val < list2.Val {
            curr.Next = list1
            list1 = list1.Next
        } else {
            curr.Next = list2
            list2 = list2.Next
        }
        curr = curr.Next
    }
    if list1 == nil {
        curr.Next = list2
    } else {
        curr.Next = list1
    }

    return head.Next
}
```

## 递归

关键难点是如何递归，返回什么，如何使用返回值。

+ 如何递归：小的那个节点，它的下一个节点是已经整理好的序列的头
+ 返回什么：因为要返回整理好序列的头，这个小的节点就是，返回即可

```go
func mergeTwoLists(list1 *ListNode, list2 *ListNode) *ListNode {
    if list1 == nil {
        return list2
    }
    if list2 == nil {
        return list1
    }
    if list1.Val < list2.Val {
        list1.Next = mergeTwoLists(list1.Next, list2)
        return list1
    } else {
        list2.Next = mergeTwoLists(list1, list2.Next)
        return list2
    }
}
```
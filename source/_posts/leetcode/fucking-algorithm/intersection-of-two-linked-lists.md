---
title: 相交链表
categories:
  - LeetCode
  - Fucking
  - 链表
tags:
  - Go
  - 链表
index_img: /img/code.jpg
sticky: -10
date: 2022-02-17 16:28:00
---

# 相交链表

https://leetcode-cn.com/problems/intersection-of-two-linked-lists/

## 让指针分别遍历两个链表

指针 `p1` 遍历完 `l1` 后开始遍历 `l2`，`p2` 同理，每次两个指针只会走一步。这样它们最终会在相交处相遇。

![](https://labuladong.gitee.io/algo/images/%e9%93%be%e8%a1%a8%e6%8a%80%e5%b7%a7/6.jpeg)

```go
    p1, p2 := headA, headB
    for p1 != p2 {
        if p1 == nil {
            p1 = headB
        } else { 
            p1 = p1.Next
        }
        if p2 == nil {
            p2 = headA
        } else { 
            p2 = p2.Next
        }
    }
    return p1
```

## 转换成环形链表问题

让链表首尾相连，变成寻找环形链表起点问题。（但是会修改链表结构）

## 计算链表长度

计算两个链表的长度，作差，使其中一个指针先前进这个差值，使得它们到终点的距离相同，然后一起前进，判断是否相等。

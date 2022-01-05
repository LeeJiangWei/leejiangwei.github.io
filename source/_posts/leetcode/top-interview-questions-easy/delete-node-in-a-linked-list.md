---
title: 删除链表中的节点
categories:
  - LeetCode
  - 初级算法
  - 链表
tags:
  - Go
  - 链表
index_img: /img/code.jpg
sticky: -10
date: 2022-01-05 19:30:23
---

# 删除链表中的节点

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xnarn7/

https://leetcode-cn.com/problems/delete-node-in-a-linked-list/solution/

## 狸猫换太子

无法获取链表的前一个节点，直接将自己复制成下一个节点，然后删除下一个节点，就相当于删除了自己

```go
func deleteNode(node *ListNode) {
    node.Val = node.Next.Val
    node.Next = node.Next.Next
}
```

目标：杀掉A

> 正常杀手需要找到 A 的把柄才可以杀掉 A <br>
可现在找到 A 本人后竟然没有可以获取 A 把柄的途径 <br>
A 得知我们要杀他，心生一计，可助你完成任务 <br>
A 说我有 B 的把柄，你杀了 B，我改头换面，以 B 的身份活着 <br>
GC 也会自动清理掉 B 的尸体，没人会知道的

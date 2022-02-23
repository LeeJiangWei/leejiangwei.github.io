---
title: 实现 LRU 缓存算法
categories:
  - LeetCode
  - Fucking
  - 数据结构设计
tags:
  - Go
  - 链表
  - 哈希表
index_img: /img/code.jpg
sticky: -10
date: 2022-02-23 13:41:00
---

# 实现 LRU 缓存算法

https://leetcode-cn.com/problems/lru-cache/

链表有时序，插入删除快；哈希表查找快。结合这两种数据结构实现哈希链表。

+ 双链表负责按时序储存，可以做到从头部删除节点和从尾部加入节点，这样尾部的节点是最近使用过的，头部的是最长时间未使用的
+ 哈希表负责查找特定节点，以便于查找、提升、删除链表任意一个元素

```go
/* 首先实现双链表和链表节点 */

// 链表节点类
type Node struct {
    Key, Value int
    Next, Prev *Node
}

// 双链表类：新元素从尾部加入，表头元素是最久未被使用的
type LinkedList struct {
    Head, Tail *Node
    Len int
}

// 双链表类构造方法
func NewList() LinkedList {
    head := new(Node)
    tail := new(Node)
    head.Next = tail
    tail.Prev = head
    return LinkedList{head, tail, 0}
}

// 链表尾部添加节点
func (list *LinkedList) AddLast(x *Node) {
    x.Prev = list.Tail.Prev
    x.Next = list.Tail

    list.Tail.Prev.Next = x
    list.Tail.Prev = x

    list.Len++
}

// 删除链表的节点 x
func (list *LinkedList) Remove(x *Node) {
    x.Prev.Next = x.Next
    x.Next.Prev = x.Prev

    list.Len--
}

// 删除链表第一个节点并返回（返回的节点用于获取 key value）
func (list *LinkedList) RemoveFirst() *Node {
    if list.Head.Next == list.Tail {
        return nil
    }
    first := list.Head.Next
    list.Remove(first)
    return first
}


type LRUCache struct {
    m map[int]*Node
    cache LinkedList
    cap int
}

/* 定义几个 API 方法，屏蔽 map 和链表的操作细节 */

// 将某个 key 提升为最近使用的
func (this *LRUCache) makeRecently(key int) {
    x := this.m[key]       // 找到 key 对应的节点
    this.cache.Remove(x)   // 删除它
    this.cache.AddLast(x)  // 再把它加到最后
}

// 添加一个 key
func (this *LRUCache) addRecently(key, val int) {
    x := Node{key, val, nil, nil}
    this.cache.AddLast(&x)
    this.m[key] = &x
}

// 删除 key
func (this *LRUCache) deleteKey(key int) {
    x := this.m[key]
    this.cache.Remove(x)
    delete(this.m, key)
}

// 删除最久未使用的元素
func (this *LRUCache) removeLeastRecently() {
    deletedNode := this.cache.RemoveFirst()
    k := deletedNode.Key
    delete(this.m, k)
}

/* LRU 方法 */
func Constructor(capacity int) LRUCache {
    return LRUCache{make(map[int]*Node), NewList(), capacity}
}


func (this *LRUCache) Get(key int) int {
    if _, ok := this.m[key]; !ok {
        return -1
    }
    this.makeRecently(key)
    return this.m[key].Value
}


func (this *LRUCache) Put(key int, value int)  {
    // 如果 key 存在，则更新 key 值并提升（相当于删了再加）
    if _, ok := this.m[key]; ok {
        this.deleteKey(key)
        this.addRecently(key, value)
        return
    }
    // 如果容量满了，删除最久未使用的
    if this.cap == this.cache.Len {
        this.removeLeastRecently()
    }
    this.addRecently(key, value)
}
```

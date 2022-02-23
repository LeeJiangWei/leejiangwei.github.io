---
title: 实现 LFU 缓存算法
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
date: 2022-02-23 14:00:00
---

# 实现 LFU 缓存算法

https://leetcode-cn.com/problems/lfu-cache/

https://mp.weixin.qq.com/s/oXv03m1J8TwtHwMJEZ1ApQ

## 需求分析

上一个 LRU 只需要替换最长时间未使用的，因此只需要一个哈希链表维护时序信息就够了。

而 LFU 需要维护频次信息，删除时如果有多个频次相同的，还要删除最长时间未使用的。

+ 调用 `get(key)` 方法时，要返回该 `key` 对应的 `val`：使用哈希表 `keyToVal`
+ 只要用 `get` 或者 `put` 方法访问一次某个 `key`，该 `key` 的 `freq` 就要加一：使用哈希表 `keyToFreq` 快速操作对应的频率
+ 要将 `freq` 最小的 `key` 删除：
  1. 要得知最小的 `freq` 是多少：用一个变量 `minFreq` 储存
  2. 要得知哪些 `key` 的频次是 `freq`：用哈希表 `freqToKeys` 储存 `freq` 对应的**所有** `key`
  3. 如果最小 `freq` 有多个 `key`，删除最长时间未使用的：类似 LRU，用哈希链表储存 `key`
  4. 要快速删除 `key` 列表中的任意一个，因为它频次更新为 `freq + 1`后，应该从 `freqToKeys[freq]` 中删除，并加入到 `freqToKeys[freq + 1]` 中：还是类似 LRU，用哈希链表储存 `key`

至此，可以写出 LFU 所需要的所有数据结构：

```go
type LFUCache struct {
    keyToVal  map[int]int             // key 映射为 val，用于记录 key 的值
    keyToFreq map[int]int             // key 映射为 freq，用于记录 key 的频率
    freqToKeys map[int]*LinkedHashSet  // freq 映射为 key 的哈希链表，用于找到最小频率且包含时序信息的 key

    minFreq int  // 记录最小频次
    cap     int  // 记录最大容量
}
```

## 组件实现

### 哈希链表实现

Golang 没有内置的哈希链表实现，按照 LRU 中的写法再实现一次

实现双链表和链表节点：

```go
/* 链表节点类 */
type Node struct {
    Key, Value int
    Next, Prev *Node
}

/* 双链表类 */
type LinkedList struct {
    Head, Tail *Node
    Len int
}

/* 双链表类构造方法 */
func NewList() LinkedList {
    head := new(Node)
    tail := new(Node)
    head.Next = tail
    tail.Prev = head
    return LinkedList{head, tail, 0}
}

/* 向双链表尾部添加节点 */
func (this *LinkedList) AddLast(x *Node) {
    x.Prev = this.Tail.Prev
    x.Next = this.Tail

    this.Tail.Prev.Next = x
    this.Tail.Prev = x

    this.Len++
}

/* 删除双链表特定节点 */
func (this *LinkedList) Remove(x *Node) {
    x.Prev.Next = x.Next
    x.Next.Prev = x.Prev
    this.Len--
}

/* 删除双链表头部节点并返回 */
func (this *LinkedList) RemoveFirst() *Node {
    if this.Head.Next == this.Tail {
        return nil
    }

    x := this.Head.Next
    this.Remove(x)

    return x
}
```

根据双链表，实现哈希链表：

```go
/* 哈希链表类 */
type LinkedHashSet struct {
    keyToNode map[int]*Node
    cache LinkedList
}

/* 哈希链表类构造方法 */
func NewLinkedHashSet() *LinkedHashSet {
    return &LinkedHashSet{make(map[int]*Node), NewList()}
}

/* 向哈希链表添加元素 */
func (this *LinkedHashSet) AddRecently(key, val int) {
    x := &Node{key, val, nil, nil}
    this.cache.AddLast(x)
    this.keyToNode[key] = x
}

/* 删除哈希链表指定元素 */
func (this *LinkedHashSet) DeleteKey(key int) {
    x := this.keyToNode[key]
    this.cache.Remove(x)
    delete(this.keyToNode, key)
}

/* 删除哈希链表头部元素 */
func (this *LinkedHashSet) DeleteLeastRecently() *Node {
    detetedNode := this.cache.RemoveFirst()
    delete(this.keyToNode, detetedNode.Key)
    return detetedNode
}

/* 哈希链表长度 */
func (this *LinkedHashSet) Len() int {
    return this.cache.Len
}
```

### 实现 LFU 

实现 LFU 时，遇到跟频次有关的操作，先抽象为一个函数，后面再去实现函数具体逻辑：

```go
type LFUCache struct {
    keyToVal  map[int]int             // key 映射为 val，用于记录 key 的值
    keyToFreq map[int]int             // key 映射为 freq，用于记录 key 的频率
    freqToKeys map[int]*LinkedHashSet  // freq 映射为 key 的哈希列表，用于找到最小频率且包含时序信息的 key

    minFreq int  // 记录最小频次
    cap     int  // 记录最大容量
}


func Constructor(capacity int) LFUCache {
    keyToVal := make(map[int]int)
    keyToFreq := make(map[int]int)
    freqToKeys := make(map[int]*LinkedHashSet)

    return LFUCache{keyToVal, keyToFreq, freqToKeys, 0, capacity}
}


func (this *LFUCache) Get(key int) int {
    if _, ok := this.keyToVal[key]; !ok {
        return -1
    }
    this.increaseFreq(key)  // 增加 key 对应的频率
    return this.keyToVal[key]
}


func (this *LFUCache) Put(key int, value int)  {
    // 注：测试用例有传入 0 容量的情况
    if this.cap <= 0 {
        return
    }

    // 如果 key 存在，只用更新它的 value，且增加对应频率
    if _, ok := this.keyToVal[key]; ok {
        this.keyToVal[key] = value
        this.increaseFreq(key)
        return
    }

    // 如果超出容量，移除最小频率的 key
    if this.cap <= len(this.keyToVal) {
        this.removeMinFreqKey()
    }

    // 插入 key 和 val
    this.keyToVal[key] = value
    this.keyToFreq[key] = 1
    if _, ok := this.freqToKeys[1]; !ok {
        this.freqToKeys[1] = NewLinkedHashSet()
    }
    this.freqToKeys[1].AddRecently(key, value)
    this.minFreq = 1
}
```

将跟新频率、移除最小频率元素的两个方法抽象了出来：

```go
/* 两个频次相关的实用函数 */
func (this *LFUCache) removeMinFreqKey() {
    keyList := this.freqToKeys[this.minFreq]

    deletedNode := keyList.DeleteLeastRecently()
    if keyList.Len() == 0 {
        delete(this.freqToKeys, this.minFreq)
        // 由于此方法只会再插入新元素时调用，而插入后 minFreq 一定会更新为 1，因此这里不需要更新 minFreq 的值
    }

    delete(this.keyToVal, deletedNode.Key)
    delete(this.keyToFreq, deletedNode.Key)
}

func (this *LFUCache) increaseFreq(key int) {
    freq := this.keyToFreq[key]
    this.keyToFreq[key] = freq + 1  // 更新对应的频率

    // 在新的频率链表中加上它
    if _, ok := this.freqToKeys[freq + 1]; !ok {
        this.freqToKeys[freq + 1] = NewLinkedHashSet()
    }
    this.freqToKeys[freq + 1].AddRecently(key, this.keyToVal[key])

    // 在旧的频率链表中删除它
    this.freqToKeys[freq].DeleteKey(key)
    if this.freqToKeys[freq].Len() == 0 {
        delete(this.freqToKeys, freq)
        if freq == this.minFreq {
            this.minFreq++
        }
    }
}
```

## 完整代码

```go
/* 链表节点类 */
type Node struct {
    Key, Value int
    Next, Prev *Node
}

/* 双链表类 */
type LinkedList struct {
    Head, Tail *Node
    Len int
}

/* 双链表类构造方法 */
func NewList() LinkedList {
    head := new(Node)
    tail := new(Node)
    head.Next = tail
    tail.Prev = head
    return LinkedList{head, tail, 0}
}

/* 向双链表尾部添加节点 */
func (this *LinkedList) AddLast(x *Node) {
    x.Prev = this.Tail.Prev
    x.Next = this.Tail

    this.Tail.Prev.Next = x
    this.Tail.Prev = x

    this.Len++
}

/* 删除双链表特定节点 */
func (this *LinkedList) Remove(x *Node) {
    x.Prev.Next = x.Next
    x.Next.Prev = x.Prev
    this.Len--
}

/* 删除双链表头部节点并返回 */
func (this *LinkedList) RemoveFirst() *Node {
    if this.Head.Next == this.Tail {
        return nil
    }

    x := this.Head.Next
    this.Remove(x)

    return x
}

/* 哈希链表类 */
type LinkedHashSet struct {
    keyToNode map[int]*Node
    cache LinkedList
}

/* 哈希链表类构造方法 */
func NewLinkedHashSet() *LinkedHashSet {
    return &LinkedHashSet{make(map[int]*Node), NewList()}
}

/* 向哈希链表添加元素 */
func (this *LinkedHashSet) AddRecently(key, val int) {
    x := &Node{key, val, nil, nil}
    this.cache.AddLast(x)
    this.keyToNode[key] = x
}

/* 删除哈希链表指定元素 */
func (this *LinkedHashSet) DeleteKey(key int) {
    x := this.keyToNode[key]
    this.cache.Remove(x)
    delete(this.keyToNode, key)
}

/* 删除哈希链表头部元素 */
func (this *LinkedHashSet) DeleteLeastRecently() *Node {
    detetedNode := this.cache.RemoveFirst()
    delete(this.keyToNode, detetedNode.Key)
    return detetedNode
}

/* 哈希链表长度 */
func (this *LinkedHashSet) Len() int {
    return this.cache.Len
}

type LFUCache struct {
    keyToVal  map[int]int             // key 映射为 val，用于记录 key 的值
    keyToFreq map[int]int             // key 映射为 freq，用于记录 key 的频率
    freqToKeys map[int]*LinkedHashSet  // freq 映射为 key 的哈希列表，用于找到最小频率且包含时序信息的 key

    minFreq int  // 记录最小频次
    cap     int  // 记录最大容量
}


func Constructor(capacity int) LFUCache {
    keyToVal := make(map[int]int)
    keyToFreq := make(map[int]int)
    freqToKeys := make(map[int]*LinkedHashSet)

    return LFUCache{keyToVal, keyToFreq, freqToKeys, 0, capacity}
}


func (this *LFUCache) Get(key int) int {
    if _, ok := this.keyToVal[key]; !ok {
        return -1
    }
    this.increaseFreq(key)  // 增加 key 对应的频率
    return this.keyToVal[key]
}


func (this *LFUCache) Put(key int, value int)  {
    // 注：测试用例有传入 0 容量的情况
    if this.cap <= 0 {
        return
    }

    // 如果 key 存在，只用更新它的 value，且增加对应频率
    if _, ok := this.keyToVal[key]; ok {
        this.keyToVal[key] = value
        this.increaseFreq(key)
        return
    }

    // 如果超出容量，移除最小频率的 key
    if this.cap <= len(this.keyToVal) {
        this.removeMinFreqKey()
    }

    // 插入 key 和 val
    this.keyToVal[key] = value
    this.keyToFreq[key] = 1
    if _, ok := this.freqToKeys[1]; !ok {
        this.freqToKeys[1] = NewLinkedHashSet()
    }
    this.freqToKeys[1].AddRecently(key, value)
    this.minFreq = 1
}

/* 两个频次相关的实用函数 */
func (this *LFUCache) removeMinFreqKey() {
    keyList := this.freqToKeys[this.minFreq]

    deletedNode := keyList.DeleteLeastRecently()
    if keyList.Len() == 0 {
        delete(this.freqToKeys, this.minFreq)
        // 由于此方法只会再插入新元素时调用，而插入后 minFreq 一定会更新为 1，因此这里不需要更新 minFreq 的值
    }

    delete(this.keyToVal, deletedNode.Key)
    delete(this.keyToFreq, deletedNode.Key)
}

func (this *LFUCache) increaseFreq(key int) {
    freq := this.keyToFreq[key]
    this.keyToFreq[key] = freq + 1  // 更新对应的频率

    // 在新的频率链表中加上它
    if _, ok := this.freqToKeys[freq + 1]; !ok {
        this.freqToKeys[freq + 1] = NewLinkedHashSet()
    }
    this.freqToKeys[freq + 1].AddRecently(key, this.keyToVal[key])

    // 在旧的频率链表中删除它
    this.freqToKeys[freq].DeleteKey(key)
    if this.freqToKeys[freq].Len() == 0 {
        delete(this.freqToKeys, freq)
        if freq == this.minFreq {
            this.minFreq++
        }
    }
}


/**
 * Your LFUCache object will be instantiated and called as such:
 * obj := Constructor(capacity);
 * param_1 := obj.Get(key);
 * obj.Put(key,value);
 */
```
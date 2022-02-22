---
title: O(1) 时间删除查找数组中的任意元素
categories:
  - LeetCode
  - Fucking
  - 数组
tags:
  - Go
  - 数组
index_img: /img/code.jpg
sticky: -10
date: 2022-02-22 19:56:00
---

# O(1) 时间删除查找数组中的任意元素

https://leetcode-cn.com/problems/split-array-largest-sum/

底层用数组实现，用哈希表记录一个元素在数组的索引。

+ 插入：检查哈希表，将元素插入数组尾部，更新哈希表

+ 删除：通过哈希表寻到索引，和数组尾部元素交换，删除尾部元素，更新哈希表

+ 获得随机元素：生成数组大小的随机数

```go
type RandomizedSet struct {
    nums []int
    valToIndex map[int]int
}


func Constructor() RandomizedSet {
    return RandomizedSet{make([]int, 0), make(map[int]int)}
}


func (this *RandomizedSet) Insert(val int) bool {
    if _, ok := this.valToIndex[val]; ok {
        return false
    }
    this.valToIndex[val] = len(this.nums)
    this.nums = append(this.nums, val)
    return true
}


func (this *RandomizedSet) Remove(val int) bool {
    if index, ok := this.valToIndex[val]; ok {
        last := len(this.nums) - 1
        // 交换尾部元素并更新哈希表
        this.nums[index] = this.nums[last]
        this.valToIndex[this.nums[index]] = index
        // 删除元素以及哈希表的索引
        this.nums = this.nums[:last]
        delete(this.valToIndex, val)
        return true
    }
    return false
}


func (this *RandomizedSet) GetRandom() int {
    r := rand.Intn(len(this.nums))
    return this.nums[r]
}
```
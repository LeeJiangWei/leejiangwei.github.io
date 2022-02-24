---
title: 集合划分问题
categories:
  - LeetCode
  - Fucking
  - 暴力搜索算法
tags:
  - Go
index_img: /img/code.jpg
sticky: -10
date: 2022-02-24 14:50:00
---

# 集合划分问题

https://leetcode-cn.com/problems/partition-to-k-equal-sum-subsets/

## 以数字的视角

遍历所有数字，穷举每个数字可能装进的桶。当所有数字都装完后检查桶是否达到目标。

```go
var bucket []int
var target int

func canPartitionKSubsets(nums []int, k int) bool {
    bucket = make([]int, k)
    sum := 0
    for _, v := range nums {
        sum += v
    }
    
    if sum % k != 0 {
        return false 
    }

    target = sum / k

    // 将数组降序排序，以更快地命中剪枝条件
    sort.Slice(nums, func(i, j int) bool {
        return nums[i] > nums[j]
    })

    return backtrace(nums, 0)
}

func backtrace(nums []int, index int) bool {
    // 所有数字已用完，检查桶是否都达到目标
    if index == len(nums) {
        for b := 0; b < len(bucket); b++ {
            if bucket[b] != target {
                return false
            }
        }
        return true
    }
    
    // 遍历所有桶，决定自己要不要装进去
    for b := 0; b < len(bucket); b++ {
        if bucket[b] + nums[index] > target {
            continue
        }

        bucket[b] += nums[index]
        if backtrace(nums, index + 1) {
            return true
        }
        bucket[b] -= nums[index]
    }
    return false
}
```

## 以桶的视角

每个桶需要遍历 `nums` 中的所有数字，决定是否把当前数字装进桶中；当装满一个桶之后，还要装下一个桶，直到所有桶都装满为止。

+ 需要记录数字是否已经被使用。使用位图的技巧，用一个整数 `used` + 位运算来记录数字是否被使用
+ 由于算法会遍历所有的桶，而当有其中一个桶失败，相当于其他桶在当前状态下也是会失败的，应该将这个状态保存下来，其他桶再遇到这个状态就能直接返回结果，因此把 used 用哈希表记录。

```go
var target int
var used   int
var memo   map[int]bool

func canPartitionKSubsets(nums []int, k int) bool {
    sum := 0
    for _, v := range nums {
        sum += v
    }
    
    if sum % k != 0 {
        return false 
    }

    target = sum / k
    used = 0                   // 使用位运算技巧，第 i 位地 1/0 表示第 i 个数字是否已被使用
    memo = make(map[int]bool)  // 记录数字被使用的状态，避免重复运算

    return backtrace(nums, 0, k, 0)
}

func backtrace(nums []int, index int, k int, bucket int) bool {
    // 所有桶已装满
    if k == 0 {
        return true
    }

    // 当前桶已装满
    if bucket == target {
        res := backtrace(nums, 0, k-1, 0)
        memo[used] = res
        return res
    }

    if v, ok := memo[used]; ok {
        return v
    }

    for i := index; i < len(nums); i++ {
        // 第 i 个数组已经被装入其他桶中
        if ((used >> i) & 1) == 1 {
            continue
        }

        // 当前桶装不下
        if bucket + nums[i] > target {
            continue
        }

        // 做选择
        used |= 1 << i  // 将第 i 位设为 1
        bucket += nums[i]

        // 递归穷举下一个数字是否装入当前桶
        if backtrace(nums, i + 1, k, bucket) {
            return true
        }

        // 撤销选择
        used ^= 1 << i  // 使用异或运算将第 i 位恢复 0
        bucket -= nums[i]
    }
    return false
}
```
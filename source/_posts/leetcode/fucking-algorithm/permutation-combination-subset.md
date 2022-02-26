---
title: 回溯法解决排列、组合、子集
categories:
  - LeetCode
  - Fucking
  - 暴力搜索算法
tags:
  - Go
  - 回溯法
index_img: /img/code.jpg
sticky: -10
date: 2022-02-24 16:40:00
---

# 回溯法解决排列、组合、子集

回溯法基本框架：

```python
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    
    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```

## 子集

https://leetcode-cn.com/problems/subsets/

```go
var res [][]int

func subsets(nums []int) [][]int {
    res = make([][]int, 0)
    backtrack(nums, 0, []int{})
    return res
}

func backtrack(nums []int, start int, track []int) {
    // 直接将当前子集加入结果
    t := make([]int, len(track))
    copy(t, track)
    res = append(res, t)

    for i := start; i < len(nums); i++ {
        track = append(track, nums[i])
        backtrack(nums, i + 1, track)
        track = track[:len(track)-1]
    }
}
```

## 组合

https://leetcode-cn.com/problems/combinations/

```go
var res [][]int

func combine(n int, k int) [][]int {
    res = make([][]int, 0)
    backtrack(n, k, 1, []int{})
    return res
}

func backtrack(n, k, start int, track []int) {
    // 组合出足够的长度才要加到结果中
    if len(track) == k {
        t := make([]int, len(track))
        copy(t, track)
        res = append(res, t)
    }

    for i := start; i <= n; i++ {
        track = append(track, i)
        backtrack(n, k, i + 1, track)
        track = track[:len(track)-1]
    }
}
```

## 全排列

https://leetcode-cn.com/problems/permutations/

```go
var res [][]int

func permute(nums []int) [][]int {
    res = make([][]int, 0)
    backtrack(nums, nil)
    return res
}

func backtrack(nums []int, track []int) {
    // 排列出足够的长度才要加到结果中
    if len(track) == len(nums) {
        t := make([]int, len(track))
        copy(t, track)
        res = append(res, t)
        return
    }

    for _, n := range nums {
        exist := false
        for _, m := range track {
            if m == n {
                exist = true
                break
            }
        }
        if exist {
            continue
        }

        track = append(track, n)
        backtrack(nums, track)
        track = track[:len(track)-1]
    }
}
```

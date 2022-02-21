---
title: 二分查找
categories:
  - LeetCode
  - Fucking
  - 数组
tags:
  - Go
  - 数组
index_img: /img/code.jpg
sticky: -10
date: 2022-02-21 21:40:00
---

# 二分查找

https://labuladong.gitee.io/algo/2/21/61/

## 基本二分查找：查找一个元素

https://leetcode-cn.com/problems/binary-search/

```go
func search(nums []int, target int) int {
    left, right := 0, len(nums) - 1       // 搜索区间两头都包含

    for left <= right {                   // while 循环中取等于号
        mid := left + (right - left) / 2  // 防止 left+right 溢出
        if nums[mid] == target {
            return mid
        } else if nums[mid] < target {    
            left = mid + 1                // 搜索区间两头都包含了，mid可以 +1
        } else {
            right = mid - 1               // 搜索区间两头都包含了，mid可以 -1
        }
    }
    return -1
}
```

## 查找左右侧边界

+ 统一所有的二分查找为查找两头闭区间，左右指针初始化为 `0, len(nums) - 1`
+ while 循环条件全都是  `left <= right`
+ 所有的区间更新都是 `left = mid + 1` 或 `right = mid - 1`
+ 找到目标后不马上返回，而是收缩另一侧的边界（想要得到哪一侧，就收缩另一侧）
+ while 循环结束后返回想要的边界指针，需要判断有没有溢出、以及目标有没有找到。

## 实战：在排序数组中查找元素的第一个和最后一个位置

https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/

分别以寻找左侧和右侧边界作为例子。注意，两段代码只有在有注释的地方不同，其余都是一样的。

这寻找左侧边界：

```go
func searchRange(nums []int, target int) []int {
    left, right := 0, len(nums) - 1

    for left <= right {
        mid := left + (right - left) / 2
        if nums[mid] < target {
            left = mid + 1
        } else if nums[mid] > target {
            right = mid - 1
        } else {
            // 不马上返回，锁定左侧边界
            right = mid - 1
        }
    }

    // 边界条件判断、是否找到判断
    if left >= len(nums) || nums[left] != target {
        return []int{-1, -1}
    }

    // 找到左侧边界后，顺序寻找到右侧即可
    for i := left; i < len(nums); i++ {
        if nums[i] != target {
            break
        }
        right = i
    }

    return []int{left, right}
}
```

如果改成寻找右边界：

```go
func searchRange(nums []int, target int) []int {
    left, right := 0, len(nums) - 1

    for left <= right {
        mid := left + (right - left) / 2
        
        if nums[mid] < target {
            left = mid + 1
        } else if nums[mid] > target {
            right = mid - 1
        } else {
            // 不马上返回，锁定右侧边界
            left = mid + 1
        }
    }
    
    // 边界条件判断、是否找到判断
    if right < 0 || nums[right] != target {
        return []int{-1, -1}
    }

    // 找到右侧边界后，顺序寻找到左侧即可
    for i := right; i >= 0; i-- {
        if nums[i] != target {
            break
        }
        left = i
    }

    return []int{left, right}
}
```
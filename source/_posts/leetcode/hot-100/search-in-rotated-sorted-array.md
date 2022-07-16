---
title: 搜索旋转排序数组
categories:
  - LeetCode
  - HOT 100
tags:
  - Go
  - 数组
index_img: /img/code.jpg
sticky: -10
date: 2022-07-16 14:49:00
---

# 搜索旋转排序数组

https://leetcode.cn/problems/search-in-rotated-sorted-array/

## 二分查找

对于一个旋转后的数组，取它的中点，则一定有一边是有序的。对于有序的那一边，如果 target 在那个范围内，则只需要搜索那一边即可，否则搜索另一边。

```go
func search(nums []int, target int) int {
    left, right := 0, len(nums) - 1

    for left <= right {
        mid := left + (right - left) / 2

        if nums[mid] == target {
            return mid
        } else if nums[left] <= nums[mid] { // 左半边有序
            if nums[left] <= target && target <= nums[mid] {
                right = mid -1
            } else {
                left = mid + 1
            }
        } else { // 右半边有序
            if nums[mid] <= target && target <= nums[right] {
                left = mid + 1
            } else {
                right = mid - 1
            }
        }
    }
    
    return -1
}
```
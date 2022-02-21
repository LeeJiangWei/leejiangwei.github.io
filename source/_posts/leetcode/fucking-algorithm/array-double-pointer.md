---
title: 双指针基础
categories:
  - LeetCode
  - Fucking
  - 数组
tags:
  - Go
  - 数组
  - 双指针
index_img: /img/code.jpg
sticky: -10
date: 2022-02-21 17:10:00
---

# 双指针基础

## 两数之和 II - 输入有序数组

https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/

只要数组有序，就应该想到双指针技巧，一左一右不断向目标值逼近。

```go
func twoSum(numbers []int, target int) []int {
    left, right := 0, len(numbers) - 1
    for left < right {
        if numbers[left] + numbers[right] == target {
            return []int{left+1, right+1}
        }
        if numbers[left] + numbers[right] < target {
            left++
        } else {
            right--
        }
    }
    return []int{0, 0}
}
```
## 反转字符串

```go
func reverseString(s []byte)  {
    left, right := 0, len(s) - 1
    for left < right {
        s[left], s[right] = s[right], s[left]
        left++
        right--
    }
}
```
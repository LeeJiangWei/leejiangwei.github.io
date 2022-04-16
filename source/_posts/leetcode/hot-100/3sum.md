---
title: 三数之和
categories:
  - LeetCode
  - HOT 100
tags:
  - Go
  - 数组
index_img: /img/code.jpg
sticky: -10
date: 2022-04-16 19:47:00
---

# 三数之和

https://leetcode-cn.com/problems/3sum/

## 排序 + 双指针

首先要求 a b c 都不能重复，为了方便处理，可以先将数组排序，然后从小到大开始选 a b c，这样只要在选的时候不选重复的值，就能保证最终的序列也不重复。

假定先选定一个 a，此时目标变为选择一个 b 跟 c 使得 a b c 加起来是 0。由于 a 已经固定了，只要选择一个 b，c 的值也相应地确定了，且随着 b 增大，c 的取值只可能会减小，因此可以使用双指针一左一右来找出，减少一层循环。

```go
func threeSum(nums []int) [][]int {
    // 先对数组排序
    sort.Ints(nums)
    n := len(nums)
    res := make([][]int, 0)

    for first := 0; first < n - 2; first++ {
        // 跳过重复的 a
        if first > 0 && nums[first] == nums[first-1] {
            continue
        }

        // c 作为右指针
        third := n - 1

        // b 作为左指针
        for second := first + 1; second < n - 1; second++ {
            // 跳过重复的 b
            if second - 1 > first && nums[second] == nums[second-1] {
                continue
            }

            // 收缩右指针
            for second < third && nums[first] + nums[second] + nums[third] > 0 {
                third--
            }

            // 排除左右指针相遇的情况
            if second == third {
                break
            }

            if nums[first] + nums[second] + nums[third] == 0 {
                res = append(res, []int{nums[first], nums[second], nums[third]})
            }
        }
    }

    return res
}
```
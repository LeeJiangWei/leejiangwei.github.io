---
title: 快速排序和快速选择
categories:
  - LeetCode
  - Fucking
  - 数组
tags:
  - Go
  - 数组
index_img: /img/code.jpg
sticky: -10
date: 2022-04-19 22:17:00
---

# 快速排序和快速选择

https://leetcode-cn.com/problems/kth-largest-element-in-an-array/

https://mp.weixin.qq.com/s/8ZTMhvHJK_He48PpSt_AmQ

## 快速排序 v1

```go
func quicksort(arr []int, left, right int) {
    if left >= right {
        return
    }

    i, j := left, right
    
    for i < j {
        for i < j && arr[j] >= arr[left] {
            j--
        }
        for i < j && arr[i] <= arr[left] {
            i++
        }
        arr[i], arr[j] = arr[j], arr[i]
    }
    arr[j], arr[left] = arr[left], arr[j]

    quicksort(arr, left, j - 1)
    quicksort(arr, j + 1, right)
}
```

## 快速排序 v2

抽象一个 `partition` 函数，作用：选择一个分界点 `p`，交换元素使得 `nums[left..p-1]` 都**小于等于** `nums[p]`，且 `nums[p+1..right]` 都**大于** `nums[p]`。

```go
func quicksort(arr []int, left, right int) {
    if left >= right {
        return
    }

    p := partition(arr, left, right)

    quicksort(arr, left, p - 1)
    quicksort(arr, p + 1, right)
}

func partition(arr []int, left, right int) int {
    i, j := left, right
    
    for i < j {
        for i < j && arr[j] >= arr[left] {
            j--
        }
        for i < j && arr[i] <= arr[left] {
            i++
        }
        arr[i], arr[j] = arr[j], arr[i]
    }
    arr[j], arr[left] = arr[left], arr[j]

    return j
}
```

## 快速选择

执行 `partition` 后，`nums[p]` 左边的元素都以及比它小，而右边都比它大。将 `p` 和 `k` 比较，如果 `p < k`，说明第 `k` 大的元素在 `p` 的右边，只需要对 `nums[p+1..right]` 执行 `partition`，反之亦然。

注意：`partition` 函数返回的是 `j`。当传入 `partition` 函数的 `left > right` 时，返回 `i` 会导致越界（如果传入正常的 `left < right` 值，在 for 循环退出时 `i` 总是等于 `j`）。所以在快速排序中，由于 `quicksort` 函数进入后会判断 `left` 和 `right` 的大小，所以返回 `i` 和 `j` 都是一样的。

```go
func findKthLargest(nums []int, k int) int {
    return quickselect(nums, len(nums)-k)
}

func quickselect(arr []int, k int) int {
    left, right := 0, len(arr)-1

    for left <= right {
        p := partition(arr, left, right)

        if p < k {
            left = p + 1
        } else if p > k {
            right = p - 1
        } else {
            return arr[p]
        }
    }

    return -1
}

func partition(arr []int, left, right int) int {
    i, j := left, right
    
    for i < j {
        for i < j && arr[j] >= arr[left] {
            j--
        }
        for i < j && arr[i] <= arr[left] {
            i++
        }
        arr[i], arr[j] = arr[j], arr[i]
    }
    arr[j], arr[left] = arr[left], arr[j]

    return j
}
```

复杂度：O(N)

最好情况下，每次都将数组二分，且二分后只会继续二分其中的一半。

```python
# 等差数列
N + N/2 + N/4 + N/8 + ... + 1 = 2N = O(N)
```

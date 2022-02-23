---
title: 删除有序数组的重复元素
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

# 删除有序数组的重复元素

此类题目都可以用快慢指针，快指针一直走，遇到重复的元素就跳过，否则赋值给慢指针且慢指针走一步，这样慢指针走过的路全都是不重复的了。

## 删除有序数组中的重复项

https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/

```go
func removeDuplicates(nums []int) int {
    if len(nums) == 0 {
        return 0
    }

    slow, fast := 0, 0
    for fast < len(nums) {
        if nums[fast] != nums[slow] {
            slow++
            nums[slow] = nums[fast]
        }
        fast++
    }
    return slow + 1
}
```

## 删除排序链表中的重复元素

https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/

与上一题十分泪湿，只不过将数组换成了链表。末尾记得要将慢指针所指的节点以后的元素都删掉（断开链接）。

```go
func deleteDuplicates(head *ListNode) *ListNode {
    if head == nil {
        return head
    }
    slow, fast := head, head
    for fast != nil {
        if fast.Val != slow.Val {
            slow = slow.Next
            slow.Val = fast.Val
        }
        fast = fast.Next
    }
    slow.Next = nil  // 断开不重复部分和后面的链接
    return head
}
```

## 移除元素

https://leetcode-cn.com/problems/remove-element/

快指针遇到要移除的元素就跳过，否则告诉慢指针，一样的道理。

```go
func removeElement(nums []int, val int) int {
    slow, fast := 0, 0
    for fast < len(nums) {
        if nums[fast] != val {
            nums[slow] = nums[fast]
            slow++
        }
        fast++
    }
    return slow
}
```

## 移动零

https://leetcode-cn.com/problems/move-zeroes/

快指针遇到 0 就跳过，否则告诉慢指针。完成后将慢指针以后的元素都变成 0 即可。

```go
func moveZeroes(nums []int)  {
    slow, fast := 0, 0
    for fast < len(nums) {
        if nums[fast] != 0 {
            nums[slow] = nums[fast]
            slow++
        }
        fast++
    }
    for slow < len(nums) {
        nums[slow] = 0
        slow++
    }
}
``` 
---
title: 回文链表
categories:
  - LeetCode
  - 初级算法
  - 链表
tags:
  - Go
  - 链表
index_img: /img/code.jpg
sticky: -10
date: 2022-01-06 16:18:53
---

# 回文链表

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xnv1oc/

https://leetcode-cn.com/problems/palindrome-linked-list/solution/

## 栈

把所有值存到栈（数组）中，然后前后指针来检查是否为回文。

```go
func isPalindrome(head *ListNode) bool {
    values := []int{}

    for head != nil {
        values = append(values, head.Val)
        head = head.Next
    }

    n := len(values)
    for i, v := range values[:n/2] {
        if v != values[n-1-i] {
            return false
        }
    }

    return true
}
```

## 快慢指针

使用快慢指针，慢指针走 1 步时快指针走 2 步，这样在快指针到达末尾时，慢指针恰好在链表的中间。

反转后半部分链表，然后检查是否是回文，这样时间复杂度还是 O(n)，空间复杂度只有 O(1)了。

```go
func isPalindrome(head *ListNode) bool {
    if head.Next == nil {
        return true
    }

    fast, slow := head, head

    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
    }

    if fast != nil { // 奇数个节点
        slow = slow.Next // 跳过节点中点，只逆转后半截
    }

    revHead := reverse(slow)

    for revHead != nil {
        if head.Val != revHead.Val {
            return false
        }
        head = head.Next
        revHead = revHead.Next
    }
    return true
}

func reverse(head *ListNode) *ListNode {
    var prev *ListNode
    curr := head

    for curr != nil {
        next := curr.Next
        curr.Next = prev
        prev = curr
        curr = next
    }

    return prev
}
```

## 递归

类似于二叉树的后序遍历，单链表也能从后往前遍历，只要在**调用递归函数之后**进行访问操作就可以了，例如：（JAVA）

```java
private void printListNode(ListNode head) {
    if (head == null)
        return;
    printListNode(head.next);
    System.out.println(head.val);
}
```

因此也可以在递归中从后往前走的时候，用一个**全局变量**来储存从前往后走的指针，递归每走一步，这个指针也往前走一步，这样就能实现两头的访问。

```go
var temp *ListNode

func isPalindrome(head *ListNode) bool {
    temp = head
    return check(head)
}

func check(node *ListNode) bool {
    if node == nil {
        return true
    }
    res := check(node.Next) && node.Val == temp.Val
    temp = temp.Next
    
    return res
}
```

可以把递归视作函数调用栈，因此空间复杂度依然是 O(n)。
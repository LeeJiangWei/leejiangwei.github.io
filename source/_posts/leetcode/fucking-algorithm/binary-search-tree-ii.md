---
title: 二叉搜索树 2
categories:
  - LeetCode
  - Fucking
  - 树
tags:
  - Go
  - BST
index_img: /img/code.jpg
sticky: -10
date: 2022-02-20 15:54:09
---

# 二叉搜索树系列 2

## 验证二叉搜索树

https://leetcode-cn.com/problems/validate-binary-search-tree/

注意：这题有坑，左右子树都是 BST 的一棵树，它自己不一定是 BST（还需要左右子树的值都小于或大于自己）。因此不能简单的递归交给子树解决，而要将根节点的限制也作为函数参数传入。

```go
func isValidBST(root *TreeNode) bool {
    return isValid(root, nil, nil)
}

func isValid(root, min, max *TreeNode) bool {
    if root == nil {
        return true
    }
    if min != nil && root.Val <= min.Val {
        return false
    }
    if max != nil && root.Val >= max.Val {
        return false
    }

    return isValid(root.Left, min, root) && isValid(root.Right, root, max)
}
```

或者走一遍中序遍历，判断是否有元素比上一个元素小。

```go
func isValidBST(root *TreeNode) bool {
    stack := []*TreeNode{}
    inorder := math.MinInt64
    for len(stack) > 0 || root != nil {
        for root != nil {
            stack = append(stack, root)
            root = root.Left
        }
        root = stack[len(stack)-1]
        stack = stack[:len(stack)-1]
        if root.Val <= inorder {
            return false
        }
        inorder = root.Val
        root = root.Right
    }
    return true
}
```

## 二叉搜索树中的搜索

https://leetcode-cn.com/problems/search-in-a-binary-search-tree/

递归和迭代两种方法，最坏情况下时间复杂度都是 O(N) （因为不是`平衡` BST，最坏情况下退化成一个链表）。

空间复杂度，最坏情况下递归还是栈空间的 O(N)，而迭代是 O(1)，没使用额外空间。

### 递归

```go
func searchBST(root *TreeNode, val int) *TreeNode {
    if root == nil || root.Val == val {
        return root
    }
    if val < root.Val {
        return searchBST(root.Left, val)
    } else {
        return searchBST(root.Right, val)
    }
}
```

### 迭代

```go
func searchBST(root *TreeNode, val int) *TreeNode {
    for root != nil && root.Val != val {
        if val < root.Val {
            root = root.Left
        } else {
            root = root.Right
        }
    }
    return root
}
```

## 二叉搜索树中的插入

https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/

类似于搜索，只不过需要对返回值赋值。**一旦涉及「改」，函数就要返回 `TreeNode` 类型，并且对递归调用的返回值进行接收。**

```go
func insertIntoBST(root *TreeNode, val int) *TreeNode {
    if root == nil {
        return &TreeNode{val, nil, nil}
    }
    if val < root.Val {
        root.Left = insertIntoBST(root.Left, val)
    } else {
        root.Right = insertIntoBST(root.Right, val)
    }
    return root
}
```

## 删除二叉搜索树中的节点

https://leetcode-cn.com/problems/delete-node-in-a-bst/

记住接受函数的返回值。删除一个节点，如果它左右子树都不为空，则最好的方法是找一个替代它，使得新的节点还能满足 BST。右子树中的最小节点和左子树中的最大节点都能满足这个要求，只要用它们替换掉待删除的节点即可。

```go
func deleteNode(root *TreeNode, key int) *TreeNode {
    if root == nil {
        return root
    }
    if key < root.Val {
        root.Left = deleteNode(root.Left, key)
        return root
    } else if key > root.Val {
        root.Right = deleteNode(root.Right, key)
        return root
    } else {
        if root.Right == nil {
            return root.Left
        }
        if root.Left == nil {
            return root.Right
        }
        minNode := findMin(root.Right)  // 用待删除节点的右子树的最小节点来替换

        root.Right = deleteNode(root.Right, minNode.Val)  // 先从原来的树中切掉

        minNode.Right = root.Right  
        minNode.Left = root.Left
        root = minNode  // 然后用它替代要删除的节点
        return root
    }
}

func findMin(root *TreeNode) *TreeNode {
    for root.Left != nil {
        root = root.Left
    }
    return root
}
```

注意这里我们不改变节点的值，只操作它的指针。

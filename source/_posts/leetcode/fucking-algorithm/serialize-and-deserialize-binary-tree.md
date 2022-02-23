---
title: 二叉树的序列化与反序列化
categories:
  - LeetCode
  - Fucking
  - 树
tags:
  - Go
  - 二叉树
index_img: /img/code.jpg
sticky: -10
date: 2022-02-20 15:12:43
---

# 二叉树的序列化与反序列化

https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/

## 前序遍历

按任意一种方式遍历然后用相同的方式还原其实都可以，这里选择前序遍历。

```go
import "strconv"

type Codec struct {
    
}

func Constructor() (_ Codec) {
    return
}

// Serializes a tree to a single string.
func (this *Codec) serialize(root *TreeNode) string {
    sb := &strings.Builder{}
    serialize(root, sb)
    return sb.String()
}

func serialize(root *TreeNode, sb *strings.Builder) {
    if root == nil {
        sb.WriteString("null,")
        return
    }
    
    sb.WriteString(strconv.Itoa(root.Val))
    sb.WriteByte(',')
    serialize(root.Left, sb)
    serialize(root.Right, sb)
}

// Deserializes your encoded data to tree.
func (this *Codec) deserialize(data string) *TreeNode {    
    stringSlice := strings.Split(data, ",")

    // 使用了函数闭包的技巧，省去传参的麻烦
    var deserialize func() *TreeNode
    deserialize = func() *TreeNode {
        
        if stringSlice[0] == "null" {
            stringSlice = stringSlice[1:]
            return nil
        }

        val, _ := strconv.Atoi(stringSlice[0])
        stringSlice = stringSlice[1:]

        return &TreeNode{val, deserialize(), deserialize()}
    }
    return deserialize()
}
```
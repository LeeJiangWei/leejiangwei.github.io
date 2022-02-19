---
title: 二叉树 0
categories:
  - LeetCode
  - Fucking
  - 树
tags:
  - Go
  - 树
index_img: /img/code.jpg
sticky: -10
date: 2022-02-19 22:05:00
---

# 二叉树系列 0

+ 前中后序是遍历二叉树过程中处理每一个节点的三个特殊时间点

+ 对节点的访问顺序其实是一样的，但是输出顺序不一样

+ 如果能遍历一遍得到结果的，就可以用遍历法。而如果涉及子树结果的（可分解为子问题），就可以用递归的方法。

+ 后序位置才能**通过返回值获取子树的信息**。一旦发现题目和子树有关，那大概率要给函数设置合理的定义和返回值，在**后序位置**写代码了。

## 二叉树的最大深度

https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/

### 递归

```go
func maxDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    return 1 + max(maxDepth(root.Left), maxDepth(root.Right))
}

func max(a, b int) int {
    if a > b {
        return a 
    }
    return b
}
```

### 遍历

```c++
// 记录最大深度
int res = 0;
// 记录遍历到的节点的深度
int depth = 0;

// 主函数
int maxDepth(TreeNode root) {
	traverse(root);
	return res;
}

// 二叉树遍历框架
void traverse(TreeNode root) {
	if (root == null) {
		// 到达叶子节点，更新最大深度
		res = Math.max(res, depth);
		return;
	}
	// 前序位置
	depth++;
	traverse(root.left);
	traverse(root.right);
	// 后序位置
	depth--;
}
```

## 二叉树的直径

https://leetcode-cn.com/problems/diameter-of-binary-tree/

```go
var maxDiameter int

func diameterOfBinaryTree(root *TreeNode) int {
    maxDiameter = 0
    maxDepth(root)
    return maxDiameter
}

func maxDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    maxLeft, maxRight := maxDepth(root.Left), maxDepth(root.Right)
    myDiameter := maxLeft + maxRight
    maxDiameter = max(maxDiameter, myDiameter)
    return 1 + max(maxLeft, maxRight)
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```

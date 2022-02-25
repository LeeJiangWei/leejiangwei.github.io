---
title: 常用的位操作
categories:
  - LeetCode
  - Fucking
  - 数学运算技巧
tags:
  - Go
index_img: /img/code.jpg
sticky: -10
date: 2022-02-25 09:54:44
---
# 常用的位操作

https://labuladong.gitee.io/algo/4/30/118/

## 常用操作

### 将某个二进制数的第 i 位置为 0 或 1

在用位图技巧记录一连串 bool 值的时候非常有用。

```go
n = n | 1 << i    // 用或操作置 1

n = n & ~(1 << i) // 用与操作置 0

n = n ^ 1 << i    // 用异或操作翻转这一位
```

解释：将 1 左移 `i` 位，得到的数只有第 `i` 位为 1，其余为 0。

+ 用 `1 << i` 对 `n` 进行 OR 操作，其他位置都会维持不变，第 `i` 位会变成 1。
+ 用 `~(1 << i)` 对 `n` 进行 AND 操作，只有第 `i` 位会变成 0。
+ 用 `1 << i` 对 `n` 进行 XOR 操作，由于 `1 XOR 0 = 1`，`1 XOR 1 = 0`，如果这一位原来是 1，异或后会变成 0；如果这一位原来是 0，异或后会变成 1，实现了翻转。因为 `1 << i` 的其他位置都是 0，而 `1 XOR 0 = 1`，`0 XOR 0 = 0`，`n` 的其他位置不会变。

### 利用或操作 `|` 和空格将英文字符转换为小写

```go
('a' | ' ') = 'a'
('A' | ' ') = 'a'
```

### 利用与操作 `&` 和下划线将英文字符转换为大写

```go
('b' & '_') = 'B'
('B' & '_') = 'B'
```

### 利用异或操作 ^ 和空格进行英文字符大小写互换

```go
('d' ^ ' ') = 'D'
('D' ^ ' ') = 'd'
```

### 判断两个数是否异号

利用补码编码的符号位来判断，可以省去 if else 分支。

```go
x, y := -1, 2
f := ((x ^ y) < 0) // true

x, y := 3, 2
f := ((x ^ y) < 0) // false
```

## 算法常用操作

`n & (n - 1)` 技巧：作用是消除数字 `n` 的二进制表示中的最后一个 1。

其核心逻辑是：`n - 1` 一定可以消除最后一个 1，同时把其后的 0 都变成 1，这样再和 `n` 做一次 `&` 运算，就可以仅仅把最后一个 1 变成 0 了。

![](https://labuladong.gitee.io/algo/images/%e4%bd%8d%e6%93%8d%e4%bd%9c/1.png)

### 计算汉明权重（Hamming Weight

https://leetcode-cn.com/problems/number-of-1-bits/

因为 `n & (n - 1)` 可以消除最后一个 1，所以可以用一个循环不停地消除 1 同时计数，直到 `n` 变成 0 为止。

```go
func hammingWeight(n uint32) int {
    res := 0
    for n != 0 {
        n = n & (n - 1)
        res++
    }
    return res
}
```

### 判断一个数是不是 2 的指数

https://leetcode-cn.com/problems/power-of-two/

一个数如果是 2 的指数，那么它的二进制表示一定只含有一个 1，用这个技巧消除 1 一次，如果变成 0 了就是 2 的幂。

```go
func isPowerOfTwo(n int) bool {
    if n <= 0 {
        return false
    }
    return (n & (n - 1)) == 0
}
```

### 查找只出现一次的元素

https://leetcode-cn.com/problems/single-number/

一个数和它本身做异或运算结果为 0，即 `a ^ a = 0`；一个数和 0 做异或运算的结果为它本身，即 `a ^ 0 = a`。

对于这道题目，我们只要把所有数字进行异或，成对儿的数字就会变成 0，落单的数字和 0 做异或还是它本身，所以最后异或的结果就是只出现一次的元素：

```go
func singleNumber(nums []int) int {
    res := 0
    for i := 0; i < len(nums); i++ {
        res ^= nums[i]
    }
    return res
}
```

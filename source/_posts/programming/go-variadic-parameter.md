---
title: Golang 可变参数（...）
date: 2022-02-11 22:20:04
categories:
  - 编程语言
  - Go
tags:
  - Go
index_img: /img/go.png
sticky: -5
---

# Golang 可变参数（点点点）

可变参数： variadic parameter

## 在函数签名中使用

只能用在最后一个参数的类型前，表示这个类型的参数可以有任意个。实际传入函数中的是一个切片:

```go
func sum(vals ...int) int {
    total := 0
    for _, val := range vals {
        total += val
    }
    return total
}
```

## 传入函数参数时使用

如果需要传入这个函数的原始参数已经是切片了，则传入函数时在它的后面加上 `...`

```go
values := []int{1, 2, 3, 4}
fmt.Println(sum(values...)) // "10"
```

## 参考
+ https://docs.hundan.org/gopl-zh/ch5/ch5-07.html

+ https://stackoverflow.com/questions/23669720/meaning-of-interface-dot-dot-dot-interface/23669857
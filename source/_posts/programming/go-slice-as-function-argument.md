---
title: Golang 切片作为函数参数的“陷阱”
date: 2022-02-20 14:45:00
categories:
  - 编程语言
  - Go
tags:
  - Go
index_img: /img/go.png
sticky: -5
---

# Golang 切片作为函数参数的“陷阱”

我们知道 Go 中的切片类型是引用类型，直觉来讲在作为函数参数时，如果函数内部修改了切片的值，函数外的也会被修改，但这有个前提：只能修改切片的**内容**，而不能修改切片它**本身**。

如果用切片重组或者 append 方法修改了切片的长度或容量，得到的切片就不是原来那个了，因此变化不会反映到函数外。例如：

```go
func main() {
	a := [5]int{1, 2, 3, 4, 5}
	s := a[:]
	changeSlice(s)
	fmt.Println(s)
}

func changeSlice(s []int) {
    // 以下改动不会导致函数外部变化
	s = s[:2]
    s = append(s, 98)
}

// out: [1 2 3 4 5]
```

而如果修改了切片的内容，例如其中元素的值，切片的长度跟容量都没有变，会导致函数外部的切片也一起改变

```go
func main() {
	a := [5]int{1, 2, 3, 4, 5}
	s := a[:]
	changeSlice(s)
	fmt.Println(s)
}

func changeSlice(s []int) {
    // 以下改动会导致函数外部变化
	s[0] = 4396
}

// out: [4396 2 3 4 5]
```

## 传递切片的指针

如果实在想修改切片，可以传递切片的指针，解引用后进行操作

```go
func main() {
	a := [5]int{1, 2, 3, 4, 5}
	s := a[:]
	changeSlice(&s)
	fmt.Println(s)
}

func changeSlice(s *[]int) {
    *s = (*s)[:2]
	*s = append(*s, 4396)
}

// out: [1 2 4396]
```

## 复制切片

如果需要保证切片（或它的关联数组）一定不会被修改，可以将原始切片用内置函数 `copy()` 复制后操作

```go
func main() {
	a := [5]int{1, 2, 3, 4, 5}
	s := a[:]
	changeSlice(s)
	fmt.Println(s)
}

func changeSlice(s []int) {
	sCopied := make([]int, len(s))
	copy(sCopied, s)
	sCopied[0] = 4396
	sCopied = append(sCopied, 7777)
}

// out: [1 2 3 4 5]
```

## 参考

+ https://coolrc.me/2021/08/03/202108031930/

+ https://studygolang.com/articles/9876

+ https://blog.go-zh.org/go-slices-usage-and-internals

+ https://stackoverflow.com/questions/49428716/pass-slice-as-function-argument-and-modify-the-original-slice
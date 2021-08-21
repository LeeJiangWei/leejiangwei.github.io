---
title: Go build 命令用法
date: 2021-08-21 22:20:04
categories:
  - 编程语言
  - Go
tags:
  - Go
index_img: /img/go.png
sticky: -5
---

# Go build 命令用法

## 按文件打包

```bash
go build main.go utils.go
```

## 按包名打包

```bash
go build go-message-pusher
```

## 打包到不同操作系统

设置两个环境变量：

+ `GOARCH` = amd64
+ `GOOS` = linux | windows

## 其他附加参数

| 参数  | 备注                                        |
| ----- | ------------------------------------------- |
| -o    | 指定编译输出的名称                          |
| -i    | 安装作为目标依赖的包                        |
| -v    | 编译时显示包名                              |
| -p n  | 开启并发编译，默认情况下该值为 CPU 逻辑核数 |
| -a    | 强制重新构建                                |
| -x    | 打印编译时会用到的所有命令                  |
| -n    | 打印编译时会用到的所有命令，但不真正执行    |
| -race | 开启竞态检测                                |

## GoLand 中的设置示例

![](https://i.loli.net/2021/08/21/peVkNE19O4GwClj.png)

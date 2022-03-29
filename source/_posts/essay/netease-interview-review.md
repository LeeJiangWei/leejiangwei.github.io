---
title: 网易互娱暑期实习后端面试记录
date: 2022-03-28 18:18:11
categories:
- 随笔
sticky: -1
---

# 网易互娱暑期实习后端面试记录

## 项目

+ 项目相关
+ 印象最深的是什么
+ JWT 用的什么签名算法
+ JWT 有什么缺点
+ ORM 是什么，Gorm 怎么将数据表字段映射到结构体属性
+ 如果要将 Sqlite 换成 MySQL 放到线上去用，揣测这样做的意图
+ 如果将 access token 独立成服务，且在更新的时候每个用到它的其他服务都要通知要怎么做
+ 与数据库通信用的什么协议

## 计算机网络

+ 什么是 close wait 和 time wait 状态
+ 服务端退出 close wait 状态会调用什么函数
+ DDos 攻击是什么
+ TCP 半连接 DDos 如何使服务器不能服务（消耗了服务器什么资源）
    + 服务器能维持的半连接队列是有限的，一旦满了就无法处理正常的请求了，消耗了服务器 CPU 和内存等资源
+ IPv6 跟 IPv4 的区别
+ 家里电脑配置上网时，IP地址、网关地址、DNS 服务器、子网掩码哪些是在同一网段的

## Go

+ 读写一个 nil 的 channel 会发送什么
+ new 和 make 的区别
+ select 语句和 switch 语句有什么区别
+ 如果在 select 语句中套一个 for 循环，在里面 break，请问 break 的是循环还是 select
+ 有用过 Go 的锁吗
+ 有用过 defer 语句吗
+ 有用过 defer recover 吗

## Python

+ 列表生成式生成列表和 for 循环一个一个 append 有什么区别
+ pass 语句有什么作用
+ yield 关键字有什么用

## Linux

+ 用过那些 Linux 命令
+ 平时学校服务器出问题怎么维护的
+ 平时是怎么指定谁用哪几张显卡的

## 其他

+ OOM killer 听过吗
+ 讲两个排序方法
+ sleep 排序有什么问题
+ 如果让你随意调度休眠的线程，怎么保证输出是对的
+ 如何看待代码里硬编码路径
+ 使用 git push --force 会怎么样

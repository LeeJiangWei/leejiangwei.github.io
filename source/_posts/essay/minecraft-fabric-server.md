---
title: 玩 MineCraft 的 Fabric 服务器
date: 2021-08-04 22:14:00
excerpt: 也就是一般说的 mod 服，可以加各种 mod
categories:
    - 随笔
tags:
    - MineCraft
index_img: /img/mc.jpg
banner_img: /img/mc.png
---

# 玩 MineCraft 的 Fabric 服务器

MineCraft Java 版有两种常见的非官方服务器，Forge 和 Fabric，相当于是提供了官方接口之上再次的封装，让 mod 开发者更加方便编程。

网上有说法称 Fabric 服务器更小巧，且 Forge 已经比较老了，故最终选择了 Fabric。

## 服务端

### 安装 Fabric 服务器

可以完全参照 Fabric 官网的文档：[Installing a Fabric Server without a GUI](https://fabricmc.net/wiki/player:tutorials:install_server)，按部就班即可。

注意，在按照文档的示例一通命名改名操作后，启动命令是`java -jar server.jar`，而非`java -jar vanilla.jar`，后者相当于原版服务器，mod 不会生效。

可以使用与官方服务器相同的参数来指定使用的内存大小。例如：`java -Xms1024M -Xmx1024M -jar server.jar`

### 安装 Mod

一些 mod 是要求在服务器上安装，另一些则是需要在本地客户端上安装，还有一些是两者都要。安装 mod 时需要看清 mod 本身的说明。

服务端安装 mod，将 mod 的 jar 包直接放入游戏根目录的 `mods` 目录下，重启服务器即可。mod 本身可能会有设置，对应的配置文件在 `config` 目录中。

![mods 和 config 目录示意图](https://i.loli.net/2021/08/04/Gh38n2fHJq6d9OK.png)

目前我在服务端安装的 mod：（1.17.1 版本可用）

+ Fabric API
+ 修改苦力怕爆炸的行为（environmentalcreepers）
+ 一键砍树（FallingTree）
+ 连锁挖矿（diggusmaximus）（客户端服务器都要装）

## 客户端

### 安装 Fabric 客户端

在这里下载 Fabric Installer：[Installation for: Minecraft Launcher](https://fabricmc.net/use/)

对于我用的 Windows 系统，jar 包和 .exe 文件都可以，运行后按提示安装即可。最好使用默认目录，防止自己忘记。

### 安装 Mod

直接将jar包放到这个目录中：`C:\Users\你的用户名\AppData\Roaming\.minecraft\mods`

目前我在客户端安装的 mod：（1.17.1 版本可用）

+ Fabric API
+ 钓鱼自动收杆（Autofish）
+ 连锁挖矿（diggusmaximus）（客户端服务器都要装）
+ 小地图（voxel map）（非常好用！！）
+ mod 管理菜单（mod menu）

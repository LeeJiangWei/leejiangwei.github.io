---
title: 部署自己的 MineCraft 服务器
date: 2021-07-19 14:04:57
categories:
    - 随笔
tags:
    - MineCraft
index_img: /img/mc.jpg
banner_img: /img/mc.png
---

# 部署自己的 MineCraft 服务器

放假在家突然想玩新版MC，买了个Java版和朋友联机玩。记录一下折腾过程。

## 服务器的配置要求

原本在朋友1核2G的服务器上玩，遇到了第三个玩家加入就会被踢出去的问题，而服务器内存和CPU并未过载，推测是带宽不足（为128KB）。可能出现的错误信息有：

+ 登入失败：身份验证目前正在停机维护
+ lost connection：timed out

![错误信息](https://i.loli.net/2021/07/19/NsQHjSEZBkLTnOz.png)

为了能愉快地玩耍，在阿里云上买了个所谓的轻量应用服务器，2核2G内存，关键是峰值带宽为5M。搞定以后解决了所有问题，3个人玩的很流畅。虽然每月限流1000GB，但还是完全够用。一年99，并且再续费一年似乎也是99，感觉挺不错的。

![我买的服务器的配置](https://i.loli.net/2021/07/19/LaSM8ypwVfiJmAI.png)

## 开放服务器端口

在阿里云的服务器控制台中设置防火墙规则，开放MC要用的`25565`端口。

![不知道用的是TCP还是UDP，那就全部开放](https://i.loli.net/2021/07/19/5x8Vbvz1nkrqmPf.png)

## 安装相关依赖

### 安装Java

```bash
apt install openjdk-16-jre-headless
```

### 安装screen

```bash
apt install screen
```

### 下载MC服务器程序

下载地址见：[DOWNLOAD THE MINECRAFT: JAVA EDITION SERVER](https://www.minecraft.net/en-us/download/server)

```bash
wget https://launcher.mojang.com/v1/objects/a16d67e5807f57fc4e550299cf20226194497dc2/server.jar
```

就是一个jar包，用Java运行这个包就启动了服务器。

## 启动游戏服务器

### 创建会话

创建了名为mc的会话。

```bash
screen -S mc
```

### 运行游戏服务器

两个参数`-Xmx`和`-Xms`分别指定了最大内存和最小内存。（均为1024M）

```bash
java -Xmx1024M -Xms1024M -jar minecraft_server.1.17.1.jar nogui
```

成功启动后就能看到各种提示信息，此时可以输入各种服务器命令了。

![启动成功](https://i.loli.net/2021/07/19/MOFGxlQfTH5X3gd.png)

### 保留Screen并退出

`ctrl + A + D`

### 再次进入

```bash
screen -r mc
```

### 强行杀死

```bash
screen -S mc -X quit
```

更多screen的用法详见：[linux中screen命令的用法](https://www.huaweicloud.com/articles/6d91932d9a65d3cb45101886ac6fe53a.html)

## MineCraft服务器属性

详见：[Minecraft服务器属性](https://minecraft.fandom.com/zh/wiki/Server.properties?variant=zh#Java.E7.89.88_3)

编辑`server.properties`文件。

## MineCraft后台命令

详见：[命令列表及其概述](https://minecraft.fandom.com/zh/wiki/%E5%91%BD%E4%BB%A4?variant=zh#.E5.91.BD.E4.BB.A4.E5.88.97.E8.A1.A8.E5.8F.8A.E5.85.B6.E6.A6.82.E8.BF.B0)

启动服务器后直接输入命令即可，无需以`/`开头。


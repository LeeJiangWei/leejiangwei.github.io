---
title: 个人域名绑定 Github pages
date: 2021-07-19 22:30:03
excerpt: 无需再用 github.io 啦
categories:
  - 博客建设
index_img: /img/tag.png
banner_img: /img/tag.png
---

# 个人域名绑定 Github pages

## 购买域名

我是在腾讯云上买的域名，按照指示按部就班就好，各种实名认证都搞好，审核速度还是挺快的。

## 添加 DNS 解析记录

来到腾讯云的控制台，添加类型为 CNAME 的记录，指向自己原来的 GitHub Page 名`xxx.github.io`。

这里我添加的是 blog 子域名，记录值如下图所示。

![这里我只用了 blog 子域名而没有直接解析主域名(@)](https://i.loli.net/2021/07/19/NXI9p5ymYvCxhBf.png)

## GitHub pages相关设置

### 添加 CNAME 文件

**在博客源分支的`source`目录下创建名为 CNAME 的文件**，内容为刚才添加了解析规则的域名。例如我的是：blog.gentlecomet.com。

由于我的博客使用的是 Hexo 以及 GitHub action 的自动部署，如果直接放在部署分支中，会在 push 源分支的时候被自动删掉，必须放在源分支的`source`目录下才行，这样部署的时候这个文件也会被正确地放到部署的分支中。

### 设置 pages

在仓库的 Settings 里找到 Pages 选项卡，设置 custom domain 为 CNAME 中的域名，save 即可。最后把强制 HTTPS 打开就好了。

![](https://i.loli.net/2021/07/19/RAjPHGbsDiNFzE2.png)

注意这一步实际上会自动添加一个 CNAME 文件在部署分支里。

## 关于我的域名

有选择困难症，想域名的时候想了好久。最后选了这个 gentle comet，neta 了我很喜欢的 YOASOBI 的一首歌 **優しい彗星** 的英文翻译，意为”温柔的彗星“。


---
title: 将Hexo部署到Github上
excerpt: 自己搞了有一小会儿，写一下过程防止以后忘了
date: 2021-06-08 17:51:59
categories:
- 博客建设
index_img: /img/default.jpg
banner_img: /img/default.jpg
---

## 主要参考
+ [利用 GitHub Actions 自动部署 Hexo 博客](https://hexo.fluid-dev.com/posts/actions-deploy/)
+ [编写自己的 GitHub Action，体验自动化部署](https://zkqiang.cn/posts/e8ed6836/)

## 步骤
#### 博客目录创建git仓库，推送至source分支
```ps
git init
git add .
git commit
git branch -M source
git push origin source
```

#### 更改`.github/workflows/pages.yml`
```yaml
name: Pages

on:
  push:
    branches:
      - source  # 监听push事件的分支，是博客源文件的分支

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          ref: source

      - name: Setup Node
        uses: actions/setup-node@v1
        with:
          node-version: '12.x'

      - name: Hexo Generate
        run: |
          rm -f .yarnclean
          yarn --frozen-lockfile --ignore-engines --ignore-optional --non-interactive --silent --ignore-scripts --production=false
          rm -rf ./public
          yarn run hexo clean
          yarn run hexo generate

      - name: Hexo Deploy
        env:
          SSH_PRIVATE: ${{ secrets.SSH_PRIVATE }}  # 仓库的私有环境变量
          GIT_NAME: LeeJiangWei                    # 自己的git用户名名字
          GIT_EMAIL: ljwskrskr@outlook.com         # 自己的邮箱
        run: |
          mkdir -p ~/.ssh/
          echo "$SSH_PRIVATE" | tr -d '\r' > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts
          git config --global user.name "$GIT_NAME"
          git config --global user.email "$GIT_EMAIL"
          yarn run hexo deploy
```
主要注意注释的部分，相应值要改为自己的，其他的照抄就好了（我也不懂）。

#### 更改项目私有环境变量
在项目的`Settings`页的`Secrets`标签里新建一个，名字是`SSH_PRIVATE`，值是SSH私钥（为啥呢）。

#### 更改项目Github Pages的源目录
在项目的`Settings`页的`Pages`标签里，将源目录改为master，保存就好了。
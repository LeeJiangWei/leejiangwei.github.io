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
+ [GitHub Pages](https://hexo.io/docs/github-pages)

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
  pages:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Use Node.js 12.x
        uses: actions/setup-node@v1
        with:
          node-version: '12.x'
      - name: Cache NPM dependencies
        uses: actions/cache@v2
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
          publish_branch: master  # 部署的分支，是Github Pages对应的目录
```
注意一下分支名字即可。

#### 更改项目Github Pages的源目录
在项目的`Settings`页的`Pages`标签里，将源目录改为`master`，保存就好了。

## 其他参考
这两个是Fluid主题给的教程，但是不太看得懂到底部署到哪儿去了，最后没有用。

+ [利用 GitHub Actions 自动部署 Hexo 博客](https://hexo.fluid-dev.com/posts/actions-deploy/)
+ [编写自己的 GitHub Action，体验自动化部署](https://zkqiang.cn/posts/e8ed6836/)

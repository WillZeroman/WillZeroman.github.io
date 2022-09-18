---
title: Getting Start For Hexo And Github Page
date: 2022-06-19 20:24:18
categories:
- 基础
tags: 
- 基础
- Hexo
excerpt: 从零开始搭建个人技术博客，用于记录与分享。
---

从零开始搭建个人技术博客，用于记录与分享。

## 准备工作

所需技能：

* [node.js](http://nodejs.cn)：目前的版本数 `v16`，本次用于安装hexo。

* [git](https://git-scm.com)：版本管理工具，本次用于上传代码到github。


有了以上两个工具，再加上掌握markdown写作语法，就可以开始搭建并写作了。


## Hexo 第一篇文章

### 安装 Hexo
1. 使用 npm 命令安装 Hexo

```bash
npm install hexo-cli -g
```

2. 安装完后，使用 hexo 命令建立博客，可以指定文件夹。

```bash
hexo init <folder>
cd <folder>
npm install
```
3. 执行以下命令，本地部署，

```bash
hexo server
```
启动后访问 `http://localhost:4000`，效果如下：
![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2f801a1c96d84a9cac03b7d301f68a1d~tplv-k3u1fbpfcp-watermark.image?)

### 新建文章
新建文章之前，修改 `_config.xml`中的配置，修改创建文章的名称，增加年月日标识。

```bash
new_post_name: :year-:month-:day-:title.md
```
新建文章

```bash
hexo new "Getting Start For Hexo And Github Page"
```
会在`source/_posts`中新创建一个名为 "2022-06-19-Getting-Start-For-Hexo-And-Github-Page.md"的文件，编辑即可写文章。

## Gitbub Pages 部署
使用Github Action 做自动化部署。

1. 在Github中创建一个公开仓库，根据自己的 **username** 命名，格式为 ***username*.github.io**
2. 将上一个段落创建的 hexo 目录推送到仓库中，分支为默认分支 **main**

```bash
git init
git remote add origin xxx
git checkout main
git add .
git commit -am "init hexo"
git push -u origin main
```
3. 在目录下创建文件 `.github/workflows/pages.yml`，然后填入以下内容

```yml
name: Pages

on:
  push:
    branches:
      - main  # default branch

jobs:
  pages:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true        # 拉取submodule模块
      - name: Use Node.js 16.x
        uses: actions/setup-node@v2
        with:
          node-version: '16'
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

```
保存，将以上内容推送到仓库中，当部署完成后，生成的页面会在`gh-pages`分支中。

4. 修改Github Pages配置，在 Github 仓库的 **Settings** > **Pages** > **Source**中，修改分支为`gh-pages`
5. 在 **Actions** 中检查是否部署成功
![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8ea533fc39e2481e9a4b7908cbfee5bb~tplv-k3u1fbpfcp-watermark.image?)
部署成功后，访问网页 *username*.github.io

## 踩到的坑
### Github中的持续集成，没有使用 Travis CI，使用Github Action
由于[Travis CI](https://travis-ci.com/)目前已全面收费，新用户只能免费使用30天，如果使用Travis CI需要认证用户信息，并购买服务后才能使用。
Github Actions是Github的免费持续集成工具，符合当前建站要求。

因此最新版本的hexo建站，可以使用 Github Action。

### 部署成功后，访问页面内容为空
**现象：**
1. 页面访问成功，但是页面为空。不显示文章。
2. 查看Github Actions的运用的 `build` 任务时，出现WARN日志。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fa5268773cb64f3ea9556ddd95834856~tplv-k3u1fbpfcp-watermark.image?)


**原因：**
在持续集成的时候，拉取仓库代码后，没有拉去到对应的 submodule 的代码，导致 theme 主题下的目录为空。在后面执行`hexo generate`的时候，生成的 index.html 页面文件都是空的。

**解决办法**：
在 `pages.yml` 中，第一步 `checkout`增加配置 `submodules: true ` 用于拉去submodule的源代码。

## Reference

* hexo [中文](https://hexo.io/zh-cn/docs/)、[英文](https://hexo.io/docs/index.html)： 推荐看英文，中文翻译比较老旧。
* Github Actions [中文](https://docs.github.com/cn/actions/learn-github-actions/understanding-github-actions)：对 Actions 有个初步的了解。
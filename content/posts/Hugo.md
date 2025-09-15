---
tags:
  - "#MJJ"
  - "#blog"
  - "#hugo"
  - MJJ
  - blog
  - hugo
created: 2025-09-15T11:31:00
title: Hugo
date: 2025-09-15
slug: 17:06
share: true
description: 学习使用hugo搭建个人博客
series: Hugo
lastmod: 2025-09-15T19:37:00
author: hansel
dir: posts
cover:
  image: ""
---
# 准备

## 安装软件

```bash
sudo pacman -Sy --needed hugo git
```

## **注册 GitHub 并创建仓库**

-  [Github 注册](https://github.com)
- 创建一个**与你用户名相同的仓库**，比如:

```bash
<Github用户名>.github.io
```

# Hugo 博客项目

## 创建源代码

在本地找个位置存储 **源代码**

```bash
# 创建文件夹
mkdir blog

# 打开文件夹
cd blog

# 使用 hugo 创建文件夹(此处是您的代码仓库)
hugo new site <你的文件夹名>

#打开你的文件夹
cd <你的文件夹名>
```

## 添加主题

> (此处使用 **主题** - **PaperMod** 作为例子)

- 在当前 “根目录” 文件夹目录下

```bash
# git初始化本地仓库
git init
# 将 PaperMod下载到本地
git submodule add \\
    <https://github.com/adityatelange/hugo-PaperMod.git> \\
    themes/PaperMod  
```

- 如果 **git submodule add** 不行，则可以网页浏览到 [PaperMod主页](https://github.com/adityatelange/hugo-PaperMod)点击 **<>Code** 下载 zip 文件，压缩文件将文件名改为 **PaperMod** 并复制粘贴到_**当前themes目录**_下即可。

## **配置 hugo.toml**

- 将下面代码复制粘贴到hugo.toml下。

```bash
baseURL = 'https://<username>.github.io/'
languageCode = 'zh-cn'
title = '我的博客'
theme = 'PaperMod'

[params]
  defaultTheme = "auto"
  # 这里 homeInfoParams 下注意不要换行，否则 hugo server可能打开不了（复制时候删除这行注释）
[params.homeInfoParams]
    Title = "你好，我是 <username> "
    Content = "欢迎来到我的博客主页：个人 / 博客 / 教程 "

[[menu.main]]
  name = "个人"
  url = "/posts"
  weight = 1

[[menu.main]]
  name = "教程"
  url = "/docs"
  weight = 2

[[menu.main]]
  name = "学习"
  url = "/study"
  weight = 3
```

- **baseURL**: 访问你个人网页的链接;
- **url**: 链接的是当前目录下**content下的文件夹**;
- **weight**: 表示网页部署位置
- **后续视个人情况修改**

# **写第一篇文章**

```bash
# 在当前 根目录 下输入
hugo new content/posts/hello-hugo.md
```

- 使用**hugo new**会链接脚本创建markdown的上述链接:

```bash
+++
date = '2025-09-15T09:49:33+08:00'
draft = true
title = 'Hello Hugo'
+++
```

- 在当前md下输入<您的内容>:

```bash
+++
date = '2025-09-15T09:49:33+08:00'
draft = true
title = 'Hello Hugo'
+++

这是我的第一篇文章！我正在学习 Hugo 😊
```

# **本地预览网站 & 📁 构建 public/ 文件夹**

```bash
# 当前根目录下输入
hugo server -D

# 使用浏览器打开，查看效果
<http://localhost:1313>
```

- 当你想部署网站时，运行：

```bash
hugo
```

- 这条命令会生成 `**public/**` 文件夹，它包含了 **构建好的静态网页内容**，可以直接部署。
- 你部署到网页上的内容应该是在 **`public/`** 下

```bash
# 进入 public/ 目录并初始化 Git
cd public
git init
git remote add origin <https://github.com/你的用户名/你的用户名.github.io.git>
git checkout -b gh-pages

# 提交构建结果并推送到 gh-pages 分支
git add .
git commit -m "手动部署 Hugo 页面"
git push -f origin gh-pages
```

- 在 **GitHub** 设置 **`Pages`** 分支
    - 打开你的 GitHub 仓库
    - 点击菜单栏 **Settings → Pages**
    - 在 **Source** 选项中选择：
        - 分支：`gh-pages`
        - 目录：`/(root)`
    - 保存并等待 GitHub 自动生成网页（几分钟内）

# **部署到 GitHub Pages（使用 Actions 自动部署）**

## **初始化 git 并推送到 GitHub**

```bash
# 在根目录下
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:你的用户名/你的用户名.github.io.git
git push -u origin main
```

> ⚠️ 如果你使用的是 HTTPS，请把 [git@github.com](mailto:git@github.com)… 改为 [`https://github.com/](<https://github.com/>)你的用户名/你的用户名.github.io.git`

## **添加部署工作流**

- 创建文件 (在根目录下) **`.github/workflows/deploy.yml`**：(注意 **`.github`** 的文件夹也是自己创建的)
- 复制一下内容到 **`deploy.yml`** 下

```yaml
name: Deploy Hugo site to GitHub Pages

on:
  push:
    branches:
      - main  # 👈 每次推送到 main 分支就会自动部署

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build Hugo site
        run: hugo --minify

      - name: Deploy to GitHub Pages 🚀
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```
> [!tip] 注意
> - 必须给 `GITHUB_TOKEN` **写权限**（ Settings → Actions → General → Read and write permissions）。

# **总结**

- 每次想新写内容直接

```bash
# 文件存储在content下
hugo new <你的文件路径>

# 本地查看
hugo server -D

# 浏览器查看
<http://localhost:1313>

# 之后直接在根目录下,注意你当前的分支应该是 main
## 查看分支
git branch

git add .
git commit -m "你的日志"
git push origin main
```

- 此后你写的 **`deploy.yml`** 会在 **actions** 下自动部署网页，**无需**在进入到public下再次推送静态网页
- 您的网页源代码在 **`main`** 分支下，静态部署网页代码在 **`pulic`** 文件夹下(也是访问您网页的代码内容)

> [!tip]  参考
> - [使用Hugo 搭建博客并部署到 GitHub Pages \| VSVnakers 主页](https://vsvnakers.github.io/docs/hugo-deploy/)
> - [Notion-Hugo-Github博客系统一体化 - xfeng's blog](https://www.xfeng.io/posts/notion-hugo-github%E5%8D%9A%E5%AE%A2%E7%B3%BB%E7%BB%9F%E4%B8%80%E4%BD%93%E5%8C%96/#)
>-  [notion-hugo-blog：将Notion内容自动化生成Hugo网站 \| Welcome to train.sh](https://train.sh/posts/2025-08-notion-hugo-blog-open-source/)
>- [obsidian如何自动发布hugo博客 - 任意的Blog](https://renyili.org/post/obsidian%E5%A6%82%E4%BD%95%E8%87%AA%E5%8A%A8%E5%8F%91%E5%B8%83hugo%E5%8D%9A%E5%AE%A2/)
>- [使用 Obsidian 免费建个人博客 \| PrintLove](https://www.printlove.cn/obsidian-blog/)

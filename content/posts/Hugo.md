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

# 手动上传 github
## 准备

### 安装软件

```bash
sudo pacman -Sy --needed hugo git
```

### 注册 GitHub 并创建仓库

-  [Github 注册](https://github.com)
- 创建一个**与你用户名相同的仓库**，比如:

```bash
<Github用户名>.github.io
```

## Hugo 博客项目

### 创建源代码

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

### 添加主题

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

### 配置 hugo.toml

- 将下面代码复制粘贴到hugo.toml下。

```bash
baseURL: 'https://<username>.github.io/'
defaultContentLanguage: zh        # 主语言
languages:
  zh:
    languageName: 中文
    languageCode: zh-cn
    weight: 1
title: 'hansel 的垃圾站'
theme: 'PaperMod'
pagination:
  pagerSize: 9 # 首页文章显示个数
summaryLength: 999999 # 首页文章摘要字数
hasCJKLanguage: true
rssLimit: 10 # 限制 rss 输出的数量

markup:
  highlight:
    noClasses: false
    style: catppuccin-frappe
  goldmark:
    renderer:
      unsafe: true
  tableOfContents:
    startLevel: 2 # 从 h2 开始
    endLevel: 3 # 到 h4 结束
    ordered: false # 生成 ul 而不是 ol

outputs:
  home:
    - HTML
    - RSS
    - JSON

params:
  showtoc: true   # 让文章页出现目录
  tocopen: true   # 默认展开，false 则折叠
  DateFormat: "2006年01月02日"
  defaultTheme: "auto"
  homeInfoParams:
    Title: "你好，我是 hansel "
    Content: "欢迎来到我的博客主页 "

menu:
  main:
    - name: "个人"
      url: "/posts"
      weight: 1
    - name: "归档"
      url: "/archives"
      weight: 2
    - name: "学习"
      url: "/study"
      weight: 3

```

- **baseURL**: 访问你个人网页的链接;
- **url**: 链接的是当前目录下**content下的文件/文件夹**;
- **weight**: 表示网页部署位置
- `params.showtoc` 和 `params.tocopen` 开启文章的目录

## 写第一篇文章

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

## 本地预览网站 & 📁 构建 public/ 文件夹

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
## 总结

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

# 使用 Obsidian + Enveloppe 插件自动上传github

先按照 [Hugo > 手动上传 github]({{< relref "hugo.md" >}}#手动上传-github) 部分使用 hugo 初始化一个网站，并配置好你喜欢的主题，并发布到 Github 上。

## Enveloppe 插件

给 Obsidian 安装 [Enveloppe](https://github.com/Enveloppe/obsidian-enveloppe) 插件，该插件的作用是将 Obsidian 中的文章和本地附件上传到 Github 仓库，上传前可以指定文件目录、自定义内容替换等操作。

- 仓库配置

{{< figure src="/images/Hugo-1757987106488.webp"  width="700" height="288">}}

注意：

1. 生成的 token 不要放在 Github 的公共仓库，检测到 token 就会失效。
2. 通过 here 生成 token 时的 [链接](https://github.com/settings/tokens/new?scopes=repo,workflow) 会自动带上权限，你只需要设置名字和过期时间即可。

- 插件配置

> [使用 Obsidian 免费建个人博客 \| PrintLove](https://www.printlove.cn/obsidian-blog/#%E9%BB%98%E8%AE%A4%E9%85%8D%E7%BD%AE)

前往 [miaogaolin/obsidian-github-publisher-hugo](https://github.com/miaogaolin/obsidian-github-publisher-hugo) 拷贝 settings.json 设置，然后粘贴导入插件：

![Hugo-1757987358354.webp](/images/hugo-1757987358354.webp)

##  Obsidian 文章模板

配置是和 Hugo 强关联的，如果你用了其它工具，就根据自己的情况调整。
```markdown
---
title: "{{title}}"
date: "{{date}}"
tags:
  - blog
slug: "{{time}}"
share: true
description: ""
series:
lastmod:
author: hansel
dir: posts
cover.image: ""
---
```

- `data` :  创建时间，我这边生成的格式是 YYYY-MM-DDTHH:mm:ss
- `slug` : 自定义 URL 中文章的访问名称，默认用时间戳填充模板格式为X
- `share`:  配合 Enveloppe插件用的,true表示 obsidian 的文章可以发布
- `description`:  文章的描述 SEO 优化，为空时默认会截取文章前面的内容
- `series: "系列"`: 系列文章
- `lastmod`:  文章最后更新的时间
- `author`: 作者名称
- `dir` 属性：设置文章的上传目录，如果不设置，则以 `content/` 为根目录。`dir:"posts"` 表示上传到 `content/posts` 目录，也只有在 posts 目录下才会在网页上直接显示。
- `cover.image`：设置封面，在使用 Enveloppe 后会转化为二级 key。

## 发布

### Obsidian 命令

先使用命令行发布当前文章，输入 active，然后选择 Enveloppe 即可，记着文章的 share 属性要开启，即 true。

如果你想上传多个 share 为 true 的文章，使用的命令为：
- `Refresh published and upload new notes` 将所有 share 为 true 且新更新的文章发布
- `Refresh all published notes` 将所有 share 为 true 的文章都发布

### 菜单

当然插件的配置也支持菜单模式，配置前往：Plugin settings -> Menu。

# 部署博客

## 部署到 GitHub Pages（使用 Actions 自动部署）

### 初始化 git 并推送到 GitHub

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

### 添加部署工作流

- 创建文件 (在根目录下) **`.github/workflows/deploy.yml`**：(注意 **`.github`** 的文件夹也是自己创建的)
- 复制一下内容到 **`deploy.yml`** 下

```yaml
name: Deploy Hugo site to GitHub Pages

on:
  push:
    branches: [main] # 👈 每次推送到 main 分支就会自动部署
    
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          submodules: true   # 主题若用 submodule 需拉取
          fetch-depth: 0
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: latest
          extended: true

      - name: Build Hugo site
        run: hugo --minify
        
      - name: Deploy to GitHub Pages 🚀
        uses: peaceiris/actions-gh-pages@v4
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

- 在 **GitHub** 设置 **`Pages`** 分支
    - 打开你的 GitHub 仓库
    - 点击菜单栏 **Settings → Pages**
    - 在 **Source** 选项中选择：
        - 分支：`gh-pages`
        - 目录：`/(root)`
    - 保存并等待 GitHub 自动生成网页（几分钟内）

> [!tip] 注意
> - 必须给 `GITHUB_TOKEN` **写权限**（ `Settings → Actions → General → Read and write permissions`）。

# 抄袭对象

> [!tip]  参考
> - [使用Hugo 搭建博客并部署到 GitHub Pages \| VSVnakers 主页](https://vsvnakers.github.io/docs/hugo-deploy/)
> - [Notion-Hugo-Github博客系统一体化 - xfeng's blog](https://www.xfeng.io/posts/notion-hugo-github%E5%8D%9A%E5%AE%A2%E7%B3%BB%E7%BB%9F%E4%B8%80%E4%BD%93%E5%8C%96/#)
>-  [notion-hugo-blog：将Notion内容自动化生成Hugo网站 \| Welcome to train.sh](https://train.sh/posts/2025-08-notion-hugo-blog-open-source/)
>- [obsidian如何自动发布hugo博客 - 任意的Blog](https://renyili.org/post/obsidian%E5%A6%82%E4%BD%95%E8%87%AA%E5%8A%A8%E5%8F%91%E5%B8%83hugo%E5%8D%9A%E5%AE%A2/)
>- [使用 Obsidian 免费建个人博客 \| PrintLove](https://www.printlove.cn/obsidian-blog/)

---
title: git使用教程
date: 2025-09-16
lastmod:
  - 2025-09-16T19:59:43+08:00
tags:
  - 软件记录
  - git
slug: 19:59
author: hansel
share: true
dir: posts
---
# **基础配置**

```Bash
git config --global user.name "hansel"
git config --global user.email "58466533+JHXs@users.noreply.github.com"
git config --global core.editor "vim"  # 设置默认编辑器
```

# **本地仓库操作**

## **初始化仓库**

```Bash
mkdir project && cd project
git init  # 创建.git目录
```

## **基础工作流**

```Bash
# 添加文件到暂存区
git add filename       # 添加单个文件
git add .              # 添加所有修改

# 提交到本地仓库
git commit -m "提交说明"

# 查看状态
git status

# 查看提交历史
git log --oneline --graph
```

# **远程仓库操作**

## **连接远程仓库**

配置ssh密钥，将公钥传到github

```Bash
ssh -T git@github.com # 测试ssh连接
git remote add origin git@github.com:JHXs/my-docker-compose.git
git remote -v  # 查看远程仓库
```

### **为 GitHub 配置 SSH over HTTPS（推荐）**

GitHub 支持通过 HTTPS 端口（443）进行 SSH 连接，能有效绕过对端口 22 的限制。**这是最推荐的方法**，因为它几乎总能奏效。

- **编辑 SSH 配置文件**：
    
    打开或创建 `~/.ssh/config` 文件，添加以下内容
    
    ```Shell
    Host github.com
        HostName ssh.github.com
        User git
        Port 443
        # 如果你的密钥不是默认的 id_rsa，请取消下一行注释并指定正确的私钥路径
        # IdentityFile ~/.ssh/your_private_key
    ```
    
    **原理**：此配置强制 SSH 客户端通过 `ssh.github.com` 的主机名和 **443 端口**（即 HTTPS 端口）连接 GitHub 的 SSH 服务器。大多数网络环境都对 443 端口开放5。
    

## **推送与拉取**

```Bash
# 首次推送
git push -u origin main

# 后续推送
git push

# 拉取远程更新
git pull origin main
```

# **分支管理（核心功能）**

## **基础分支操作**

```Bash
git branch              # 查看分支
git branch new-feature  # 创建新分支
git checkout new-feature  # 切换分支
git checkout -b hotfix  # 创建并切换分支
```

## **合并与冲突解决**

```Bash
git checkout main
git merge new-feature  # 合并分支

# 出现冲突时：
# 1. 手动编辑冲突文件（搜索<<<<<<标记）
# 2. 解决后执行：
git add resolved-file
git commit
```

## **变基操作（可选高级功能）**

```Bash
git checkout feature
git rebase main  # 将feature分支基线移动到main最新提交
```

# **常见问题解决方案**

## **撤销操作**

```Bash
git reset --soft HEAD^   # 撤销commit但保留修改
git reset --hard HEAD^   # 彻底回退到上个版本
git checkout -- file.txt # 丢弃工作区修改
```

## **找回丢失代码**

```Bash
git reflog  # 查看所有操作记录
git reset --hard [commit_id]
```

## **忽略文件配置**

创建 `.gitignore` 文件：

```Bash
*.log
node_modules/
.env
```

## **SSH密钥配置**

```Bash
ssh-keygen -t ed25519 -C "your_email@example.com"
cat ~/.ssh/id_ed25519.pub  # 复制到Git平台SSH设置
```

## 仓库改名

- [ ] GitHub 端改名 → 2) 本地目录改名 → 3) 本地仓库远程地址同步。

最简操作，默认你本地已经克隆过该仓库。

1. **GitHub 端改名**

- 浏览器打开仓库页面 → Settings → 在 **Repository name** 一栏直接改成新名字（例如 `new-repo-name`）→ 点击 **Rename**。
- 改名后 GitHub 会自动把旧地址做 301 重定向，但建议尽快更新本地地址，避免重定向失效。

1. **本地目录改名**

```Shell
# 进入仓库所在父目录
cd /path/to/parent

# 把旧目录名改成新名字（Linux / WSL2 / macOS）
mv old-repo-name new-repo-name
# Windows PowerShell
# Rename-Item old-repo-name new-repo-name
```

1. **更新本地仓库的远程地址**

```Shell
cd new-repo-name          # 进入改名后的目录
# 方式 A：直接 set-url
git remote set-url origin git@github.com:<用户名>/new-repo-name.git
# 方式 B：先删再加
git remote remove origin
git remote add origin git@github.com:<用户名>/new-repo-name.git
```

验证一下：

```Shell
git remote -v
# 应显示：
# origin  git@github.com:<用户名>/new-repo-name.git (fetch)
# origin  git@github.com:<用户名>/new-repo-name.git (push)
```

# **推荐工作流程**

## **功能开发流程**

```Bash
1. git checkout -b feature/login
2. 开发完成后：
   git add .
   git commit -m "完成登录功能"
3. git checkout main
4. git pull origin main # git pull 相当于 git fetch + git merge
5. git merge feature/login
6. git push
```

## **团队协作规范**

- 主分支（main/prod）仅接受合并请求
- 功能分支按feature/xxx命名
- 每天开始工作前先执行 `git pull`

# **进阶技巧**

## **储藏临时修改**

```Bash
git stash        # 临时保存修改
git stash pop    # 恢复修改
```

## **选择性提交**

```Bash
git add -p  # 交互式选择修改片段
```

## **子模块管理**

添加子模块到特定文件夹

```Bash
git submodule add https://github.com/other/repo.git <outputdir>
```

删除子模块

```shell
git submodule deinit -f <outputdir>
git rm -rf <outputdir>
git commit -m "remove PaperMod theme"
```


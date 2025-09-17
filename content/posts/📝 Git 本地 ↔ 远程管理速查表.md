---
title: 📝 Git 本地 ↔ 远程管理速查表
date: 2025-09-17
lastmod: 2025-09-17T10:17:14+08:00
tags:
  - Utility
  - 软件记录
  - git
slug: 20250917T101714
author: hansel
share: true
series: git
dir: posts
cover:
  image: ""
---
# 🔗 远程仓库管理

|操作|命令|
|---|---|
|添加远程仓库|`git remote add origin <远程地址>`|
|查看远程仓库|`git remote -v`|
|修改远程仓库地址|`git remote set-url origin <新地址>`|
|删除远程仓库|`git remote remove origin`|
|重命名远程仓库|`git remote rename origin upstream`|
# 🚀 推送到远程

| 操作                   | 命令                            |
| -------------------- | ----------------------------- |
| 首次推送本地分支到远程（并建立追踪关系） | `git push -u origin main`     |
| 推送当前分支               | `git push`                    |
| 推送指定分支               | `git push origin branch_name` |
| 推送所有分支               | `git push --all origin`       |
| 推送标签                 | `git push origin <tagname>`   |
| 推送所有标签               | `git push --tags`             |
# ⬇️ 从远程获取更新

| 操作               | 命令                                                 |
| ---------------- | -------------------------------------------------- |
| 获取远程分支信息（不合并）    | `git fetch origin`                                 |
| 拉取远程分支并合并到当前分支   | `git pull origin main`                             |
| 拉取并重置为远程分支（强制覆盖） | `git fetch origin && git reset --hard origin/main` |
# 🌿 分支与远程

| 操作            | 命令                                        |
| ------------- | ----------------------------------------- |
| 查看本地和远程分支     | `git branch -a`                           |
| 建立本地分支并追踪远程分支 | `git checkout -b dev origin/dev`          |
| 修改本地分支追踪的远程分支 | `git branch --set-upstream-to=origin/dev` |
| 删除远程分支        | `git push origin --delete branch_name`    |

# 🏷️ 标签与远程

|操作|命令|
|---|---|
|新建标签|`git tag v1.0.0`|
|查看所有标签|`git tag`|
|推送标签到远程|`git push origin v1.0.0`|
|删除远程标签|`git push origin :refs/tags/v1.0.0`|

# ✅ 小贴士

- 推荐 **SSH 地址**（`git@github.com:xxx.git`），避免频繁输入密码。
    
- 第一次推送用 `-u`（`--set-upstream`），后面就可以简化为 `git push / git pull`。
    
- 用 `git fetch` 比 `git pull` 安全，避免意外合并。
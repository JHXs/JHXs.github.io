---
title: VSCode
date: 2025-09-15
tags:
  - blog
  - productivity
  - Utility
  - "#vscode"
  - vscode
slug: 19:04
share: true
description: vscode
series: 系列
lastmod: 2025-09-15T19:04:00
author: hansel
dir: posts
cover:
  image: ""
---
# 配置C环境

> [!important] [https://www.cnblogs.com/duruofei/p/15674502.html](https://www.cnblogs.com/duruofei/p/15674502.html)

## 安装Mingw

```PowerShell
scoop install main/mingw
```

  

## 安装扩展

- 安装C/C++插件,扩展-->搜索C/C++-->安装。还有code runner插件

![%E5%9B%BE%E7%89%87 2.png](_extras/%e5%9b%be%e7%89%87-2.png)

- 新建C源文件，并用vscode打开，点击调试→创建`launch.json`文件，依次选择`C++(GDB/LLDB)`, 默认配置。此时会自动在当前目录下新建`.vscode`文件夹并生成`lauch.json` 将其中代码更改如下将光标放在标签上可以显示此标签的意义
    
    ```JSON
    {
        // 使用 IntelliSense 了解相关属性。 
        // 悬停以查看现有属性的描述。
        // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "gcc.exe - 生成和调试活动文件", // 显示名称，可以自定义
                "type": "cppdbg", // 不要更改
                "request": "launch", //请求配置类型，可以为 “launch”（启动）或 “attach”（附加）
                "program": "${fileDirname}\\${fileBasenameNoExtension}.exe", //用于调试的目标程序
                "args": [],
                "stopAtEntry": false, //在程序入口处停止，一般不更改
                "cwd": "${fileDirname}", //目标的工作目录，
                "environment": [],
                "externalConsole": false,
                "MIMode": "gdb", //只能为gdb
                "miDebuggerPath": "D:\\Program\\Scoop\\apps\\mingw\\current\\bin\\gdb.exe", //gdb的路径
                "setupCommands": [
                    {
                        "description": "为 gdb 启用整齐打印",
                        "text": "-enable-pretty-printing",
                        "ignoreFailures": true
                    }
                ],
                "preLaunchTask": "C/C++: gcc.exe 生成活动文件" //在启动调试之前默认先编译，以便生成目标程序
            }
        ]
    }
    ```
    

# vscode-server

## 隧道

**创建隧道**

- 进入 `.vscode-server` 文件夹，开启隧道

```Bash
cd ~/.vscode-server
./code tunnel
```

- 将隧道创建为系统服务

```Bash
./code tunnel service install
```

解决用户登出后服务终止的问题：

执行下面命令确保用户注销后服务仍运行：

```Bash
sudo loginctl enable-linger $USER
```

或者  
修改服务配置文件（`~/.config/systemd/user/code-tunnel.service`），将 `WantedBy=multi-user.target` 改为 `WantedBy=default.target`，然后重新启用服务

- 启动服务

```Bash
systemctl --user start code-tunnel
```

或者开机自启：

```Bash
systemctl --user enable --now code-tunnel
```

- 查看日志

```Bash
./code tunnel service log
```

- 卸载服务

```Bash
./code tunnel service uninstall
```

# ctr+p

> 命令

- 输入 `@` 可快速浏览符号：函数、变量等
- 输入 `:` 可快速跳转到行号
- 输入 `>` 所有命令，等效为 `ctr+shift+p`
- 输入 `#`

# 快捷键

## 编辑器

- `ctr+d` 可快速匹配一个单词，多次输入匹配同一个单词并可同时更改这几个单词
- `alt + 点击` 可以放置多个光标
- `ALT+上/下` 上下移动一行
- `ALT+Shift+上/下` 复制该行
- `ctr + L` 快速向下选中多行

## 终端

- `ctr + ←/→` 在命令的单词之间移动

# 技巧

## 符号重命名

- 右键→查找所用引用
- 重命名符号

## 文件路径魔法

新建文件时用/自动创建嵌套目录（如src/components/Button.tsx）

## 自定义任务（Tasks）

将npm run build等命令保存为快捷任务，一键执行。

## 代码片段（Snippets）

通过JSON配置快速插入常用代码模板。
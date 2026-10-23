---
title: "macOS: the best Unix Desktop"
date: 2026-10-21
tags:
  - System
  - macOS
author: Square Zhong
description: 不是推荐
---

算是一些个人的使用习惯和笔记，称不上教程。

## 开发环境

目前使用自动化脚本和 dotfiles 管理和快速部署开发环境，详见 https://github.com/squarezhong/dotfiles

### 查看系统基本信息

```shell
system_profiler SPHardwareDataType
```

check basic hardware info and uuid

### 手动添加环境变量（Environment Variables）

```shell
# xxxx 替换为实际路径
# : 是 PATH 路径的分隔符，下方命令相当于在原有环境变量前添加你的路径
echo 'export PATH="xxxx/bin:$PATH"' >> ~/.zshrc
# 当然你也可以放在后面
echo 'export PATH="$PATH:xxxx/bin"' >> ~/.zshrc
# 添加完后重新读取并执行 .zshrc，或者开一个新的终端
source ~/.zshrc
```

### 在终端中打开

刚需功能，可惜被 macOS 藏在二级菜单

Windows 是 右键文件夹内空白处 -> Open in Terminal；
macOS 是 **右键文件夹本身** -> Services -> New xxx Tab Here；
xxx 是你用的终端应用的名称，比如用 kitty，那就是 "New kitty Tab Here"。

更推荐自定义一个在终端中打开的快捷键，参考下方“自定义快捷键”章节。

## 应用选择

参考 [自用各平台应用分享（持续更新）](https://squarezhong.github.io/posts/2025-09-21-all-platform-apps/)

### 工具类插件

我个人的偏好是尽可能不使用工具类插件，包括但不限于搜索增强、状态栏修改、Finder 增强等等等等，Default is the best, let Apple decide it.

## 常用快捷键

### 快速查询

你可以安装 [KeyClu](https://github.com/Anze/KeyCluCask)，然后就可以长按 `Command` 键查看当前应用的快捷键列表。

### Important

- `Command + Space` Spotlight 搜索（最重要的快捷键，没有之一）
- `Command + Z` 各种意义上的撤回
- `Command + T` 在各种应用中新建 tab
- `Command + W` 在各种应用中关闭 tab

### Windows

- `Command + Tab` 在应用间循环
- `Command + 1-9` 在有 Tab 的软件中切换至各个 Tab
- `Command + H` Hide current window

### Files

- `Command + C` 复制文件
- `Command + V` 粘贴文件
- `Option + Command + V` 移动文件
- `Shift + Command + .`显示隐藏文件（注意是个英文句号）

### Screenshots

- `Shift + Command + 3` 全屏截图
- `Shift + Command + 4` 框选截图
- `Shift + Command + 5` 多模式截图

### Browser

- `Option + Command + R` 进入 Chrome 阅读模式
- `Command + Shift + R` 进入 Safari 阅读模式
- `Command + R` 刷新页面
- `Command + ←` 回到上一个页面

### Photos

- `option + command + E` macOS Photos 导出照片原图（Live 会以 mov 格式导出）

### VS Code

- `Command + Shift + P` 显示 Command Palette，唯一需要记住的快捷键
- `Command + B` 显示/隐藏 Sidebar
- `Command + J` 显示/隐藏 Panel，Terminal 一般也在里面。使用这个命令调出终端的好处是在中文输入法下也可以使用，"Ctrl + \`" 在中文输入法下会失效。 

更多 VS Code 快捷键请参考 https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf

### 自定义系统快捷键

直接 Spotlight 搜索（`Command + Space`）"Keyboard shorts"，然后自定义。

我个人不怎喜欢自定义快捷键，因为我记不住。唯一的自定义快捷键是将 "Services -> New kitty Tab Here" 设置为 `Shift + Command + E`。

## 虚拟机

### Linux Server

[container](https://github.com/apple/container)

苹果官方出品支持 macOS 及以后的系统。

### Linux Desktop and Windows

[UTM](https://github.com/utmapp/UTM)

### Windows

用 [Parallels Desktop](https://www.parallels.com/products/desktop/)，跑 Windows 11 Arm

## Time Machine

不要使用机械硬盘（企业级硬盘勉强一战，叠瓦盘直接卡死），尽量使用 ssd 用于 time machine 备份，不然查找历史文件的速度会慢的你怀疑人生。

### Clean Time Machine

```bash
tmutil listlocalsnapshots /.

tmutil deletelocalsnapshots xxxxxxx
```

## Troubleshooting

### 解决macOS报告文件损坏/未知签名的错误

```bash
xattr -cr /Applications/Signal.app
```

-  `-c` flag removes all attributes
- `-r` applies recursively for the entire targeted .app directory contents.

### IINA 中文字体显示为方块

参考 [https://x.com/wangcheng678/status/1897661712150777957](https://x.com/wangcheng678/status/1897661712150777957) 以及 [https://github.com/mpv-player/mpv/issues/14878](https://github.com/mpv-player/mpv/issues/14878)

原因是 macOS 中 PingFang 不再是系统字体，在系统应用 ”Font Book“ 中手动安装 PingFang 字体即可解决。

### Prevent Creation of `.DS_Store` Files on Network Drives

Run the following command to disable `.DS_Store` creation on network drives (e.g. volumes mounted via `rclone`):

```shell
defaults write com.apple.desktopservices DSDontWriteNetworkStores -bool TRUE
```

---
title: "自用各平台应用分享（持续更新）"
date: 2025-09-21
tags: ["Software"]
author: "Square Zhong"
description: "开源是对的。"
---
<span style="color:blue">[Updated on 2026-10-21]: 使用 kitty 替代 iTerm2，MATLAB 回归</span>

<span style="color:blue">[Updated on 2026-10-08]: 添加多个系统类工具，移除多个不再使用的 App，欢迎来到 Agent 时代</span>

<span style="color:blue">[Updated on 2026-07-24]: 添加 UniGetUI、Codex、Raindrop.io、Chrome、Safari、Bitwarden，移除 Onedrive、MATLAB、Microsoft To Do</span>

## 前言

刚进大学的时候搞了套苹果全家桶，当时使用的应用都基于苹果生态选择，后来手里逐渐多出了 Windows 或 Linux 系统的台式机和笔记本，以及作为备用机的安卓，为了尽可能确保统一的使用体验（以及不想被单一生态绑架），新的应用方案提上日程。

同等条件下本人更偏好开源应用和全平台应用。

> 无特殊注明均为全平台应用

## 生产力工具

### AI

- [Codex](https://chatgpt.com/codex)：Tibo 能不能再给个 banked reset
- [Gemini](https://gemini.google.com/)：我们的 Gemini，还能重回巅峰吗（哭）？
- ~~[Cherry Studio](https://www.cherry-ai.com/)：跨平台开源 AI 客户端，用于整合各家 api~~

### Terminal

- [Termius](https://termius.com/)：GitHub 学生认证后自带 Pro，全平台同步， SSH 设备多的话很方便
- [kitty](https://github.com/kovidgoyal/kitty)（macOS & Linux）：功能丰富，使用纯配置文件进行设置，备份和同步友好。使用 ssh 初次连接某台服务器时使用 `kitten ssh <user>@<ip_address>` ，该命令会自动向服务器添加 kitty 的 terminfo
- ~~[iTerm2](https://iterm2.com/)（macOS）：开源，简洁~~ macOS 上标杆级的终端应用，但是设置太割裂了，general config 和 profile 是分开的
- [Terminal](https://github.com/microsoft/terminal)（Windows）：开源，新版本的 Win11 应该会自带这个终端

### IDE

- [VS Code](https://code.visualstudio.com/)：Vibe Coding 时代，VS Code 回归文本编辑器，未曾设想的结局。Remote - SSH 功能非常好用。
- ~~[Cursor](https://cursor.com/)（某种意义上也算 VS Code）~~ Vibe 时代返璞归真回归 VS Code 了
- [MATLAB](https://ww2.mathworks.cn/en/products/matlab.html)：已经和解了

## 学术相关

- [Zotero](https://www.zotero.org/)：开源软件，用于文献管理，插件系统强大

### 文本写作

- [Typora](https://typora.io/)：简洁易用的 Mardown 编辑器
- [LaTeX Workshop](https://github.com/James-Yu/LaTeX-Workshop)（VS Code 插件）：很大程度上替代了我使用 Word 和 PowerPoint 的需求。配置方案可以参考我的另一篇 blog [All-platform Local LaTeX Solution](https://squarezhong.github.io/posts/2025-04-24-local-latex/)。

## 生活相关

### TODO

- Obsidian plugin [Tasks](https://publish.obsidian.md/tasks/Introduction)
- ~~[Microsoft To Do](https://to-do.office.com/tasks/)：虽然没有那么精致和顺手，但免费以及全平台（Linux 上有第三方客户端）~~ 已全面转入 Obsidian 插件 Tasks

### 记账

- [钱迹](https://qianjiapp.com/)：合理的买断价格，云同步

### 交易

- 同花顺：可以登陆各类券商
- 东方财富：标的更多一些

### 音乐

- [Apple Music](https://music.apple.com/)：不像国产流媒体音乐平台那么臃肿，音质也非常不错。就是能不能别涨价了。

### 阅读器

- [Innoreader](https://www.inoreader.com/): RSS 阅读器
- ~~[Folo](https://folo.is/)：开源 RSS 阅读器~~ 容易掉登陆
- [Koodo Reader](https://koodoreader.com)：开源电子书阅读器
- [Adobe Acrobat](https://www.adobe.com/acrobat.html)（Windows）：最强 PDF 阅读器
- [PDF Expert](https://pdfexpert.com/)（macOS）：macOS 下用得最顺手的 PDF 阅读器

### 播放器

- [PotPlayer](https://potplayer.daum.net/)（Windows）：强大
- [IINA](https://iina.io/)（macOS）：基于 mpv 的开源播放器

### 影视工具

- [DaVinci Resolve](https://www.blackmagicdesign.com/products/davinciresolve)：普通版本免费无广告，并且有 Linux 端
- [OBS Studio](https://obsproject.com/)：开源且强大，用于录屏和推流，你甚至可以在 Steam 下载这个软件

### 密码管理

- [Bitwarden](https://bitwarden.com/)

### 其他工具

- [7zip](https://www.7-zip.org/download.html)（Windows）：无需多言
- [MacZip](https://ezip.awehunt.com/)(macOS)
- [GrandPerspective](https://grandperspectiv.sourceforge.net/)（macOS）：可视化存储占用
- [OminiDiskSweeper](https://www.omnigroup.com/more)（macOS）：顾名思义

### 网盘

- ~~Onedrive：Office 365 附带的 1T 空间，在 Win 和 macOS 上都是系统级的体验~~ 速度实在是慢
- [Google Drive](https://drive.google.com/)：开 Gemini 附带的…
- [坚果云](https://www.jianguoyun.com/)：主要使用 WebDAV 来同步 Zotero 里的论文

## 笔记工具

- [Obsidian](https://obsidian.md/)：已经 All in Obsidian 了
- ~~[flomo](https://flomoapp.com/)：速记~~
- ~~[Notion](https://www.notion.com/)：系统化整理，All in one~~

## 浏览器

- Chrome：老大哥还是老大哥，历尽千帆，归来仍是 Chrome
- [Raindrop.io](https://raindrop.io/)：跨平台标签同步，浏览器不储存任何标签
- ~~[Edge](https://www.microsoft.com/en-us/edge)（Windows）：Windows 自带就不动它了~~ 动不动给就想改你的设置
- ~~[Safari](https://www.apple.com/safari/)（macOS）：丐版 mba m1 也就只顶得住这个的开销了~~

## 应用管理

- [UniGetUI](https://github.com/devolutions/unigetui)（Windows）：开源项目，整合多个软件源
- [Geek Uninstaller](https://geekuninstaller.com/)（Windows）：用于 Windows 彻底卸载软件
- [AppCleaner](https://freemacsoft.net/appcleaner/)（macOS）：用于 macOS 彻底卸载软件

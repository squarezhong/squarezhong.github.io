---
title: "Windows: 均衡存乎万物之间"
date: 2025-07-17
tags:
  - System
  - Windows
author: Square Zhong
description: 碎碎念，不是教程
---

<span style="color:blue">[Updated on 2026-11-02]: 添加对系统更新的建议</span>

## Introduction

> 均衡存乎万物之间 —— 凯南

Windows 是最全面最均衡的系统，你很难找到别的系统能干但是 Windows 干不了的活，尽管未必是最优解。

## System Upgrade

尽管微软经常在新系统中加入没用的新功能，砍掉好用的老功能，但还是建议日常更新最新的安全补丁。如果当前系统已经停止支持（比如 Windows 10），请尽快更新到仍受官方支持的系统版本。

以前不少人会建议不要更新系统，但现在是人均黑客（Agent 的力量）的时代，为了你的数据和信息安全，请紧跟最新的安全更新。

## Software Recommendation

见 [自用各平台应用分享（持续更新）](https://squarezhong.github.io/posts/2025-09-21-all-platform-apps/)

## Development

人生苦短，加入 WSL 吧，可以参考我的 [WSL 笔记](https://squarezhong.github.io/posts/2026-01-26-wsl2-guide/)。

一些一定需要 Windows 原生开发的工作流我没有尝试过。

## Optimization

Windows 在拥有良好兼容性的同时系统也臃肿到了一定程度，进入 AI 时代之后更是变本加厉。微软为了强推它那个难用的要命的 Copilot，把 Copilot 塞进了一堆系统应用当中，然后就可以在季度报告上大吹特吹 Copilot 用户又增长了多少。

推荐使用 [Win11Debloat](https://github.com/Raphire/Win11Debloat) 对 Win 11 系统进行瘦身。

## PE

出问题了还得靠 PE 救命

推荐 [Ankh Tech Win PE](https://ankhtech.org/winpe.html)

## Remote Desktop

局域网下最接近原生的方案。

如果你在校园网环境下使用 Remote Desktop，可以参考我本科学校的 Wiki 文章 [校内远程桌面使用指北](https://sustech.online/service/network/remote-desktop/)。

## Disk Management

### 创建、删除、格式化分区

- 简单的需求使用系统自带的“磁盘管理”即可。
- 复杂需求使用 [DiskGenius](https://www.diskgenius.com/)。
- 针对系统盘的操作进 PE 系统后使用 DiskGenius。

### Erase SSD

优先用 SSD 控制器级 **Secure Erase / NVMe Sanitize**，比如 Samsung Magician 中的 **Secure Erase**。

次选是向所有操作系统可访问的扇区写 0

下方示例使用 [diskpart](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/diskpart) 完成写 0 操作：

```powershell
diskpart
# 在弹出的命令行窗口中执行以下命令
list disk
# disk 1 改为你摇操作的磁盘
select disk 1
detail disk
clean all
exit
```

由于 FTL 和垃圾回收机制，NAND 中理论上可能仍存在已经失效但尚未物理擦除的旧页面

### 文件恢复

[Windows File Recovery](https://support.microsoft.com/en-us/windows/windows-file-recovery-61f5b28a-f5b8-3cc2-0f8e-a63cb4e1d4c4)

## Troubleshooting

{{< details summary="开启代理后 UWP 应用无法上网" open=false >}}
安装 [Fiddler Classic](https://www.telerik.com/fiddler/fiddler-classic)。

点击左上角 WinConfig 按钮 -> 勾选你想要解除网络限制的UWP软件 -> 点击上方的保存按钮
{{< /details >}}

{{< details summary="彻底卸载百度网盘 YunShellExtV164.dll" open=false >}}

使用管理员权限运行 powershell，执行以下命令

找出哪些进程调用了该dll（`tasklist` 是 cmd 命令，但是在 powershell 也被兼容）

```powershell
tasklist /m YunShellExtV164.dll
```

强制结束所有explorer进程并重启

```powershell
taskkill /f /im explorer.exe && start explorer
```

删除 DLL 文件。

```powershell
Remove-Item -LiteralPath "$env:APPDATA\baidu\BaiduNetdisk\YunShellExtV164.dll" -Force
```
{{< /details >}}

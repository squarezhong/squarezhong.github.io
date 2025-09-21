---
title: "Meta Quest3 大陆使用指北"
date: 2025-09-21T17:32:03+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Hardware", "VR", "Game"]
author: "Square Zhong"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "不成熟 VR 时代的不成熟方案."
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/squarezhong.github.io/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

## 前言

距离所谓的“VR 元年”（2016）已经过去快 10 年，尽管有不少有意思有创新的硬件和软件产品出现，但现阶段的 VR 产品仍然是玩具性质的，没有成为真正意义上的大众消费品。不过如果你的预算充足，并且想体验一下 VR 这种较为不同的表现形式，一台主流的 VR 设备仍然可以纳入考虑。

如果你完全不想折腾，那还是建议使用字节跳动旗下的 Pico，至少你不会遇到网络相关的问题。

本文默认读者可以正常连接国际互联网。

## 系统激活与升级

### 系统激活

开机后按照提示进行激活，手机上需要下载 **Meta Horizon app。**

**网络问题解决方案**：

国内激活 Quest3 最大的问题就是网络问题，常用解决方案如下：

- 直接使用 **UU 加速器**进行加速，配合支持 UU 加速器插件的路由器体验良好，评价为少折腾、多娱乐。
- 在其他设备开启代理并勾选 [Allow connections from the LAN] 类似的选项，设置 Quest3 Wi-Fi 连接时手动设定代理。
    
    你的代理需要支持 UDP 模式，否则无法完成系统激活与升级（系统内的上网和应用下载则没有问题）
    

### 手动系统升级（Official）

如果你几经折腾仍然无法直接在 Quest3 中正常升级系统，那么可以使用官方的升级工具手动升级。

[Meta Quest Software Update](https://www.meta.com/help/quest/software_update/)

参考教程：[使用官方新工具手动升级 Quest 操作系统 - 哔哩哔哩](https://www.bilibili.com/opus/946832515434283012)

### 手动系统升级（非官方）

参考教程：https://www.lxtend.com/240628-update-meta-quest-manually/

## 开发者模式与 SideQuest

网上教程很多，基本不会有啥坑。

[官方教程](https://developers.meta.com/horizon/documentation/native/android/mobile-device-setup/?locale=zh_CN)（执行到第 5 步即可）

## 外部应用安装

Quest3 本质上是个安卓设备，可以安装大部分安卓安装包。

### 使用 SideQuest 安装

在 SideQuest 右上角选择 [Install APK file from folder on computer]，然后找到要安装的 APK 文件即可。

### ADB 安装

使用 SideQuest 安装应用时卡在  **”Checking APK against blacklist...”** （参考 [SideQuest Community Support | SideQuest](https://sidequestvr.com/space/142/p/97263/checking-apk-against-blacklist)）

解决方法是直接使用 adb 安装应用，点击 SideQuest 电脑客户端右上角的 [RUN ADB commands]，运行下方命令

```bash
adb install C:\Users\square\Downloads\AppName.apk
```

记得更改路径和应用名。

## 各类资源获取

### 游戏资源

- SteamVR：在 Steam 上购买游戏后通过 Quest3 内的 Steam Link 串流。
英特尔独显（A750，B580 等）不支持 SteamVR，请谨慎购买
- Meta Store：Quest 内、手机 App、官网均可购买，可以使用 Paypal 或信用卡支付，价格较为昂贵。也可以在淘宝等平台寻找代购。
- Pirate：请自行寻找，不推荐。

### 视频资源获取

- Meta TV：Quest3 自带的视频应用。
- Youtube VR：在大陆流畅使用需要你的网络条件比较好。
- 将普通视频资源转为鱼眼视频后观看。
- 各类 BT、PT、网盘资源，在此不作具体推荐。

## 应用 & 游戏推荐

### 游戏推荐

总体来说当前 VR 游戏的开发进度较为缓慢，游戏内容较少，没有足够多的现象级游戏。

以下列表根据自己的实际体验给出，会持续更新：

- Steam Link
- Beat Saber
- Half Life: Alyx
- [No Man's Sky](https://steamdb.info/app/275850/charts/) (VR Mode)
- VRChat
- Resident Evil 4

### 常用应用推荐

- Skybox VR：额你懂的…
- Virtual Desktop：电脑串流软件
- 4XVR Video Player：视频超分辨率

## Troubleshooting

1. Quest3 Wi-Fi 断连
    
    在无法连接 Meta 服务器的情况下系统会自动断开 Wi-Fi
    
    **解决方案**：
    
    参考 https://www.vrcoast.cn/bbs/333927.html，通过 ADB 执行以下命令：
    
    ```bash
    adb shell settings put global captive_portal_https_url https://baidu.com
    adb shell settings put global captive_portal_http_url http://baidu.com
    adb shell settings put global captive_portal_mode 0
    ```

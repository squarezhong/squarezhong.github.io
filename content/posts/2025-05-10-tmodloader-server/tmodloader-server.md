---
title: "Terraria tModLoader 服务器搭建教程"
date: 2025-05-10T12:11:03+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Server", "Game"]
author: "Square Zhong"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Host your own tModLoader server."
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

## Introduction

> 灾厄伟大，无需多言

非局域网情况下，Terraria 或者 tModLoader 游戏客户端自带的联机基本只能支持两个玩家的流畅游玩。

如果你有灾厄或其他 mod 非局域网多人联机的需求，那么搭建一个 tModLoader 服务器对于流畅的联机体验是必不可少的。

目前简中互联网能找到的 tModLoader 服务器搭建教程大多不够与时俱进，且很多仅基于 Windows Server 搭建，本文参考官方说明给出一个全平台方案。

唯一参考：[官方 README](https://docs.tmodloader.net/docs/stable/md__github_workspace_src_t_mod_loader__terraria_release_extras__dedicated_server_utils__r_e_a_d_m_e.html)

# Installation

安装前提是你的云服务器已经配置好 Docker Compose。

直接下载官方仓库的 Release：

```bash
wget https://github.com/tModLoader/tModLoader/releases/download/v2025.03.3.1/tModLoader.zip
```

解压该文件：

```bash
unzip tModLoader.zip -d tModLoader
```

运行 Docker，根据提示设置服务器参数，结束。

```bash
cd tModLoader/DedicatedServerUtils
mkdir tModLoader
sudo docker compose up
```

如果你有配置好的 `tModLoader/serverconfi.txt` ，直接使用 `sudo docker compose up -d` 即可。

后台启动后你可以使用 `sudo docker attach tml` 来访问该容器。

# Mods & Worlds

首先看一下我们创建的 tModLoader 文件夹的结构：

```bash
├── Mods
│   ├── localmod1.tmod
│   ├── localmod2.tmod
│   ├── enabled.json
│   └── install.txt
├── Worlds
│   ├── world1.twld
│   ├── world1.wld
│   ├── world2.twld
│   └── world2.wld
├── server
│   └── * contains tModLoader installation *
├── steamapps
│   └── * contains Steam workshop mods *
├── logs
│   └── * contains tModLoader logs *
├── manage-tModLoaderServer.sh
├── serverconfig.txt
└── tmlversion.txt
```

服务端安装模组和配置地图都非常简单。

## Mods

修改 `install.txt` 为对应模组编号，修改 `enabled.json` 为对应模组名称。

两个文件可以通过游戏客户端导出，方法如下：

> Obtaining install.txt
>
>
> Because the steam workshop does not use mod names to identify mods, you must create a modpack to install mods from the workshop. To get an `install.txt` file and its accompanying `enabled.json`
>
> 1. Go to Workshop
> 2. Go to Mod Packs
> 3. Click `Save Enabled as New Mod Pack`
> 4. Click `Open Mod Pack Folder`
> 5. Enter the folder with the name of your modpack
> 6. Make a `Mods` folder and copy `install.txt` and `enabled.json` file into it

## Maps

你可以直接在服务端创建世界，也可以将要使用世界的 twld 和 wld 文件放入 Worlds 文件夹。

# Troubleshooting

### 物品无法正常掉落

tModLoader v2025.1.3.1

Terraria v1.4.4.9

后期不知道是不是灾厄武器过于花里胡哨的原因，有概率出现丢物品无法掉落（直接消失），宝藏袋打开后不出东西的 bug，重启 tModLoader 服务可以暂时解决问题。
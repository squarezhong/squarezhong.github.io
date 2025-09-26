---
title: "使用RIME + 雾凇拼音打造开源输入法方案"
date: 2025-09-26T20:11:03+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Software", "DIY", "Input Method"]
author: "Square Zhong"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "将输入掌握在自己手中"
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

各家闭源输入法隐私泄露事件频发，用户的每日输入成为广告商和黑产的狂欢。为了尽可能将隐私握在自己手中，一套不依赖厂商服务的开源输入法方案提上日程。

本文使用久负盛名的 [RIME]((https://rime.im/)) 作为输入法框架，[雾凇拼音]((https://github.com/iDvel/rime-ice)
)作为配置仓库，打造一套部署简单、隐私安全、自定义程度高的输入方案。

该方案全平台可用，本文基于 macOS 进行说明，其他操作系统请根据实际情况调整。

## RIME

在[官网](https://rime.im/download/)下载所使用操作系统对应的安装包并安装。

## 雾凇拼音
### 1. 安装配置管理器 [plum](https://github.com/rime/plum) 
```shell
curl -fsSL https://raw.githubusercontent.com/rime/plum/master/rime-install | zsh
```

### 2. 安装雾凇拼音
macOS 下 RIME 配置文件位于 `~/Library/Rime/`
```shell
rime_dir="$HOME/Library/Rime" zsh rime-install iDvel/rime-ice:others/recipes/full
```

其他操作系统请修改为对应的配置文件路径。

如果你需要更新词库
```shell
rime_dir="$HOME/Library/Rime" zsh rime-install iDvel/rime-ice:others/recipes/all_dicts
```

### 3. 重新部署输入法

参考[官方文档](https://github.com/rime/home/wiki/CustomizationGuide#%E9%87%8D%E6%96%B0%E4%BD%88%E7%BD%B2%E7%9A%84%E6%93%8D%E4%BD%9C%E6%96%B9%E6%B3%95)，重新部署输入法。

## 配置

参考[RIME-ICE 新手指南](https://dvel.me/posts/rime-ice/#%e6%96%b0%e6%89%8b%e6%8c%87%e5%bc%95)进行个性化的配置。







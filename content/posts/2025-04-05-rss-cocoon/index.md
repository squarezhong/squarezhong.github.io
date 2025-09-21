---
title: "RSS：在信息时代主动化茧"
date: 2025-04-05T15:55:03+08:00
# weight: 1
# aliases: ["/first"]
tags: ["DIY"]
author: "Square Zhong"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "使用 RSS 构建属于自己的“信息茧房”."
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

在信息爆炸的当下，为了不被信息的洪流冲垮，为了不被推荐算法绑架，在所谓开放的信息世界中作茧自缚，看似落后于时代的 RSS 反而成为一方净土。

某种意义上使用 RSS 相当于自己给自己造了一个茧，用以抵御滔天的信息洪流，而且用户可以清楚地知晓茧的存在，不断地定制和升级这个茧。

## 我的 RSS 方案

### 客户端：

[Folo](https://folo.is/)（全平台方案）

### 可选客户端方案：

1. [Fluent Reader](https://github.com/yang991178/fluent-reader)（全平台客户端）
2. [FreshRSS]((https://freshrss.org/index.html))：使用 Docker 在云服务器（或者本地部署加内网穿透）部署 FreshRSS，然后用任意设备通过浏览器访问即可。
    
    
3. Inoreader 或 feedly 等在线信息聚合服务，因为会员价格较高所以我没有选择。

### 浏览器插件：

[RSSHub-Radar](https://github.com/DIYgod/RSSHub-Radar)：现在大部分网站并不原生支持 RSS，遂通过 RSSHub-Radar (Backend by [RSSHub](https://github.com/DIYgod/RSSHub)) 生成订阅连接。

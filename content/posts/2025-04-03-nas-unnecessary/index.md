---
title: "NAS: 从入坑到入土"
date: 2025-04-03T12:11:03+08:00
# weight: 1
# aliases: ["/first"]
tags: ["DIY", "Hardware", "NAS"]
author: "Square Zhong"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "纯主观的 NAS 折腾报告"
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

> 需求应当被满足，而不是被创造。
> 

## 前言

从初中时拆解机械硬盘，高中时折腾 Optane + HDD，到大一大二时折腾机器人战队的黑群晖，再到大三时入手极空间 Z4 Pro，最后出清 NAS 和硬盘，回归本机 + 云服务。正如标题所言，“NAS：从入坑到入土”，接下来会讲一下折腾 NAS 的心路历程。

## 购买 NAS 前的需求

简单概括一下我购买 NAS 的需求。

一台 24 小时低功耗运行的存储服务器，满足以下功能：

1. 较为无感地同步多台设备的照片，并且支持统一的相册管理
2. 存储大容量的音乐、番剧和电影（10T+）
3. 满足玩 BT/PT 的需求（下载各种意义上的资源），不需要电脑一直开机
4. 拥有足够优秀的刮削软件，呈现精美的海报墙，且易于分享
5. 各类服务在校园网范围内可以在浏览器中通过域名（需要 https 加密）访问
6. 各类服务在公网也能够访问
</aside>

以上的需求极空间 Z4 Pro 都能够满足，一一对应的话：

1. 极相册（现在的版本有 AI 功能，可以直接根据语义进行分类和搜索）。
2. 最基础的存储功能，当然可以满足。
3. Docker 装 iyuu + qbitorrent，你的 PT 现以自动化的方式呈现。
4. 极影视非常优秀，刮削能力出色，至少比裸 Jellyfin 强多了。
5. DNSPod 解析域名到局域网地址（路由器在校园网内的地址），路由器端口转发 80、443 至 Nginx Proxy Manager 镜像，配合 Let’s Encrypt 的证书即可实现校园网范围内通过 https 域名访问服务。
6. 极相册、极影视、存储功能自带公网访问，剩下的 Docker 镜像也可以通过“远程连接”应用访问。

一直到此时还是岁月静好的。

## 购买 NAS 后的折腾

之所以最后出掉了 NAS 和硬盘，主要有四大原因：

1. 发现原需求其实是伪需求
2. 忍不住折腾
3. 未来环境的变化
4. 不可忽视的噪音

### 被证伪的需求

**仓鼠党的终结**：大量下载上传的电影、番剧，多个 PT 站的账号，精美的海报墙，这些对我来说不过是满足一下资源分享的**虚荣心和收藏癖**，**最开心的事情是看着飞快的下载上传速度，而不是细细体会电影和番剧的精彩内容**。下载了一百部电影可能看完的还没有一部，花大价钱搞 NAS 和硬盘还不如开个 Netflix 会员。**真正带来感动的是内容本身，而不是容纳内容的文件大小**。

除非真世界末日了，不然这些囤积的资源很难真正发挥价值。

### 不必要的折腾

> 折腾的乐趣来源于折腾本身，而不是折腾后的使用。当折腾本身不能给你带来乐趣的时候，也就没必要折腾了。
> 
1. 热衷于向同学提供各种在线服务，比如 Calibre-web、Jellyfin、FreshRSS 等，以满足所谓的虚荣心，实际上根本没几个活跃用户。
2. 给一台 N97 芯片的 NAS 上了 32G ddr5 内存，日常内存占用不会超过 20%…
3. 对于 Zotero 的附件同步，Docker 装 Alist 开启 webdav + 内网穿透，现已切换为坚果云 free plan。
4. Docker 安装 memos 记笔记 + 内网穿透，现已切换为 flomo free plan。
5. Docker 安装 Qinglong 搞各种购物 app 的自动签到，评价为薅这点羊毛不如多背几个单词。
6. Dokcer 安装 Navidrome 当在线播放器，最后还是网抑云启动。
7. Docker 安装 Code Server，号称要激活 iPad Pro 的生产力，最后还是 Bilibili 启动，敲代码还是带着 mac。
8. Node Exporter + Prometheus + Grafana 搞一套花里胡哨的监测方案，最后发现不会有啥异常状况，也没有啥重要资源值得监视。
9. 极空间开放 ssh 之后第一时间安装了 Portainer，但是解决折腾的方法应当是不折腾，而不是换个简单的方式折腾。
10. …
11. 利用 Nginx Proxy Manager 进行反向代理，实现不加端口号的域名访问，在上述服务都不需要的情况下也没啥反向代理的需求了。

### 未来环境的变化

1. 保研到别的学校了，网络条件从免费的对等千兆降级到收费（还不便宜）的百兆宽带，BT/PT 啥的玩不利索。
2. 三年硕士之后就是工作了，有时间捣鼓这捣鼓那不如多敲几行代码…

### 不可忽视的噪音

对于学生党来说 NAS 只能放在卧室里（虽然也没有啥别的分区了），四块机械硬盘一起工作的噪音和震动难以忽视，PT 和各类 Docker 服务带来的断断续续的频繁读写，让炒豆子的声音一整天都不绝于耳。

综上几个原因，本学期开始的时候终于下定决心出掉了 NAS 和大部分硬盘，回归本机 + 云盘 + 流媒体的模式。互联网时代人们在云上创造了伟大的基业，又有什么理由不去享用呢。

某种意义上我从数字的牢笼中暂时脱身，但根据以往的经验来看，未来无穷无尽的折腾似乎还在等着我。

## 未来展望

放弃当仓鼠党后已经没有大容量存储的需求，直接放弃了又大又吵又慢的机械硬盘，几条 2T 的 ssd （未来还可以升级到 4T）+ 国内外云盘 + 流媒体足以满足游戏、相机照片、代码、文档存储的需求。

未来如果家庭或工作场景下又重新有了使用 NAS 的需求（比如给小孩看点经典动画片，限制部分超龄内容访问；大容量工作文件的备份），应该会直接组一套全闪 NAS，让炒豆子的声音随风而去吧。
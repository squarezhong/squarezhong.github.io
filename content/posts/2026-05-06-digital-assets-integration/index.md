---
title: "近期关于数字资产整合的尝试"
date: 2026-05-06T16:20:08+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Data", "Server", "DIY", "Privacy"]
author: "Square Zhong"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "All in boom"
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

先说一下基本情况，目前活跃使用的~~生产力类型（谁说 iPad Pro 没有生产力😄）~~数码产品包括 3 台手机（iPhone \* 2 + Android \* 1)，1 台平板（iPad），3 台笔记本（macOS \* 1 + Windows \* 1 + Linux \* 1)，1 台台式机（Linux），总计 8 个设备。

在本次数字资产整合前，笔者的笔记、书签、密码、存储管理都相当混乱，不同的设备各自为战，造成了相当的体验割裂感。整合后笔记、书签、密码分别统一到了对应的解决方案，一定程度上提升了跨设备使用的体验。

目前对于数据管理的整体原则是**本地优先 + 云端加密（local first, cloud encrypted)**，不信任完全的云端存储方案，尽可能实现安全性与易用性的平衡。整合前后的具体方案见下表。

| Type     | Notes                                                      | Bookmarks                           | Passwords + Passkey + Verification Code                                                                                    |
| -------- | ---------------------------------------------------------- | ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| Previous | Notion<br>Flomo<br>Apple Notes<br>Goodnotes (hand-written) | Edge<br>Chrome<br>Firefox<br>Safari | Edge<br>Chrome<br>Firefox<br>Apple Passwords<br>Windows Hello (Passkey)<br>Microsoft Authenticator<br>Google Authenticator |
| Current  | Obsidian                                                   | Raindrop.io                         | Bitwarden                                                                                                                  |
- Obsidian 同步使用 [Remotely Save](https://github.com/remotely-save/remotely-save) 插件完成加密存储，通过 WebDav（fnOS 自带） + Nginx 实现校园网范围内同步，未来若有需要也可以上 Cloudflare Tunnel 。不用坚果云的原因是坚果云的 WebDav 服务有单位时间访问次数限制，笔记总数较多（目前 1700+）时容易撞墙。
- Raindrop.io 只用来存储书签，泄密也无妨。
- Bitwarden 服务端使用 Docker 部署的 [Vaultwarden](https://github.com/dani-garcia/vaultwarden) + Cloudflare Tunnel

关于存储的跨平台方案还在思考中，后续有进展了会在这更新。

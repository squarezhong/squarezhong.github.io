---
title: "再一次博客迁移：我们究竟需要什么样的博客方案？"
date: 2025-09-20T15:55:03+08:00
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
description: "再一次也可能是最后一次博客迁移."
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

# 再一次博客迁移: 我们究竟需要什么样的博客方案？

## 前言

我本人在理论上一直有撰写个人博客的强烈意愿和实际行动，但实际操作过程中折腾博客系统本身的精力甚至超过了撰写博客内容的精力。过去几年我尝试过多种博客方案，今天再一次迁移博客系统，并且希望未来能够较为稳定地更新博客内容。在此总结一下我个人折腾博客的历程和目前的理解。

## 折腾历程

从初中到硕士的博客折腾之路：

博客园 & 知乎 -> 云服务器 + WordPress + 自有域名 -> Docsify + Github Pages -> Notion -> Hugo + Github Pages

- 博客园 & 知乎：更像个人账号，缺少了一点博客的“个人”感，且平台有平台的审核规则。
- 云服务器 + WordPress + 自有域名：比较重型，每年的续费费用不可忽略。
- Docsify + Github Pages：Docsify 更适合作为项目文档。
- Notion：写作体验非常不错，但不支持 RSS。
- Hugo + Github Pages：目前的方案，正在体验中。


## 什么样的博客系统适合个人？

### 几点特征

针对普通的个人博客，我认为具备以下几点特征的博客方案有利于个人长期维护：
1. **免费**：免费的主要好处是你不会因为服务器或者域名的续费问题（远超首次购买的续费价格）而放弃原有的博客方案，导致频繁的博客迁移。我个人很难在频繁迁移博客的情况下坚持创作。
2. **稳定**：稳定包括博客系统本身的稳定和数据安全。如果你的博客三天两头访问不了或者出现数据丢失，都会极大地打击博客写作的积极性。
3. **易用**：部署、创作和迁移都较为方便，最好能以 Markdown 格式写作。

### 当前方案

Hugo (博客框架) + PaperMod (主题) + GitHub Pages (免费托管) 
- Github Pages 相当于免费的轻量服务器和域名（当然也可以绑定你自己的域名），附加版本管理，稳定性和数据安全都有保障。
- Hugo + PaperMod 美观性尚可，不会花里胡哨也不至过于简陋。文章使用 Markdown 编写，即使要迁移也非常方便。
- 更新博客相当于提交 commit，还蛮有成就感的。

### 个人最不推荐的方案

最不推荐的方案就是自己购买云服务器 + 域名，然后部署博客。这个方案基本上完全不符合上方提到的三点特征。
每年稳定的的续费费用，潜在的部署故障和服务器被攻击风险，如果使用国内的云服务还需要额外增加备案的步骤，诸多麻烦和不确定性都会成为博客创作的阻碍。

虽然这种方式折腾完的成就感是最强的，最有“个人”博客的感觉，适合折腾，不适合实用。

### 另当别论

如果你的博客系统是用来赚钱的，用来做个性化订阅服务的，那么你可能需要功能更为复杂、强大的博客系统，不在本文的讨论范围之内。
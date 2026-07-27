---
title: All in Obsidian
date: 2026-07-27
tags:
  - Obsidian
  - Note
  - Software
author: Square Zhong
description: Less is more.
---
## 从 Notion 回到 Obsidian

> Notion 哪里都好，坏就坏在它是 Notion

从 All in Notion 迁移到 All in Obsidian 差不多一年了，随便写点感受。

Notion 真的是一个很强大的笔记软件，说是最强大也不为过。功能全面，非常多的外部融合选项，强大的数据库与自动化功能，大量的模板与插件，方便的协作，适应这个时代的 AI 功能，美观的界面，无一不宣示着 Notion 在现代笔记软件中的王者地位。

但正是 Notion 的强大与精美，导致它对我来说太“重”了。我模仿、借鉴着网上的模板，给自己搭建了一个可以说涵盖生活学习科研方方面面的笔记系统，以高度结构化的形式呈现，并且不断探索怎么更好地利用 Notion 越加越多的强大功能，希望将 Notion 打造成“第二大脑”，正如网上很多博主所说的那样。这样一座精心搭建的“宫殿”，后期我却完全不想打开。一座已经建成的大厦，光是维护就已颇为耗费心力，更别说还想添砖加瓦了。

这种启动困难达到顶峰之后我对先对 Notion 做了一次大精简，很多看都不想看的笔记和页面直接删除，剩下的本科四年来的笔记全部导出为 Markdown 迁移进了 Obsidian，哦不对，应该说是迁移回了 Obsidian，人生由一个个的回旋镖构成，或大或小。这种导出当然会丢掉很多东西，毕竟 Markdown 是一种熵很低的格式，~~违背热力学第二定律的格式转换自然会受到物理法则的制裁~~。

当时给自己找了很多理由来推动这场工程量不算小的迁移，包括什么本地数据更安全、Markdown 格式更通用、不被单一生态所绑架、Obsidian 更轻量启动更快、双向链接更利于网络化的知识管理等等等等。现在回想，真正的理由只有一个，那就是当时的我通过 Notion 虚构了自己幻想中的生活方式。我希望自己的生活是高度结构化、高度秩序、井井有条、精美而完备的，然而实际上我的生活~~散漫~~随遇而安、~~执行力极差~~不做计划、~~三分钟后热度~~充满着灵光乍现。可以说 Obsidian 比 Notion 与我的生活更契合。追求理想中的生活固然很好，但过好真实的生活也已经很不错了。

切换回 Obsidian 后笔记的方式从原来的“在特定位置添加特定内容”变成了想到啥直接点开 daily note，加个标签把想到的写下来，想到别的了就换个标签在写一段。除了比较系统化的笔记，大部分的胡思乱想就以一个个标签为开头记在每一天的 daily note 里，日均码字量都快上千字了。系统化的笔记也不用考虑页面该怎么布局（因为没有页面），想好放哪个文件夹就行，实在想不好打个标签丢 daily note 里也不是不行...

Obsidian 是一个非常自由的笔记软件，因为哪怕你把软件本身直接删了，你的全部笔记依然以文件夹+Markdown 文件的形式好端端躺在你的电脑里，你还可以通过一些简单的设置让 Obsidian 非常核心的双链功能以相对路径引用的格式存在，完全不受生态束缚。这种自由让 Obsidian 成为完美适配 Agent 时代的软件，不管你是用 Obsidian 内部的插件，还是直接使用外部的 Agent，都可以很好地将自己的笔记作为知识库供 AI 调用。Agent 的接入让前面那种看起来“乱”的笔记方式更加有迹可循，你找不到笔记没事，留点线索让 Agent 找的到就行。

下面是一些我所使用的配置和插件，仅供参考。

## Configuration

Appearance
- Show inline title: Off 不然容易出现两个 title 的情况

以下设置是为了获得尽可能高的 Markdown 兼容性，不至于被绑定至 Obsidian 应用本身

Files and links
- New link format: Path from current file
- Automatically update internal links: On
- Use \[\[Wikilinks\]\]: Off
- Show all file types: On

## Plugins

### Remotely Save

如果你的所有设备都在苹果生态内，直接用 iCloud 同步就好了，不需要别的插件，相信苹果...

我个人秉持着 "Local first, encrypted cloud backup and sync" 的原则，选择这个插件用于加密备份与同步。原则上不信任任何云厂商，只将加密后的数据上传至云盘。

#### WebDav

我使用的是 fnOS 自带的 WebDav 服务。

不推荐使用坚果云，有单位时间内访问次数限制，别的场景下没啥问题，用来同步笔记容易撞墙（尤其是新设备全量同步）。

#### 缺点

文件数量多了之后，移动端（包括手机和平板）使用 Remotely Save 进行同步非常慢，我的解法是直接不在手机和平板上用 Obsidian。解决不了问题本身，就解决遭遇问题的设备...

### Claudian

不需要你提供 API 或者购买插件作者提供的订阅服务，而是接入你电脑上安装的 Claude、Codex、OpenCode 或者 Pi，这点好评，顶级厂商或者开源项目自带的 harness 大概率强于个人自己折腾的，还不用额外购买订阅或者支出 api 费用。

### Iconize

~~Cosplay 一下 Notion~~
带图标的文件夹更方便用户定位。

### [Dataview](https://github.com/blacksmithgu/obsidian-dataview)

模仿一下 Notion 的数据库视图，足够轻量，功能也凑合。
配合 template 使用也足够打造简洁的聚合页。

### Tasks

装了这个之后删了所有 TODO 软件，毕竟我是会把 TODO 软件所有通知功能全关掉的人...
有个页面能聚合所有 TODO 就足够了。

### Omnisearch

自带的搜索不是很好用。

### Export Image plugin

很多时候分享笔记还是图片比较方便。
这个插件可以导出完整的笔记文件，也可以选中一段文字后 "Export selection to image"。

## 使用原则

- 文本优先
- 多使用标签与双向链接（网状结构是 Obsidian 的核心）
- 可以添加文件，但要与笔记建立联系（比如在笔记中 mention），避免孤立文件
- 有限制的使用 AI 进行搜索、总结与生成

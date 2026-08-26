---
title: 国区退场之后的 Kindle 使用指南
date: 2026-08-26
tags:
  - "Kindle"
  - "Books"
  - "DIY"
author: Square Zhong
description: 还是折腾，踏踏实实买书看书完事了
---

## 前言

2013 年 Kindle 正式入华，2023 年 6 月 Kindle 国区电子书商城关闭，正式退出中国市场，十年的运营画上句号。但 Kindle 在中国仍有大量存量用户，新设备（仅硬件）也能以较低的溢价买到。这篇文章是我本人在 Kindle 关闭国区后的一些提升使用体验的~~折腾~~尝试，可供参考。

## Kindle 是否仍值得购买？

存量用户没啥说的，接着用就行了，直接跳转 [根目录主要结构](https://squarezhong.github.io/posts/2026-08-26-kindle-guide/#根目录主要结构) 章节。

那么新用户是否仍然推荐购买 Kindle 呢？从硬件和软件两个方面来讨论。

### 硬件

{{< details summary="电子纸科普环节，不感兴趣的可以直接折叠" open=true >}}
Kindle、文石等电纸书所用的屏幕类型是 电泳式（electrophoretic）**电子纸显示器**（Electronic Paper Displays, 简称 EPD），其最核心的组件是**电子墨水薄膜**。电子墨水薄膜与 TFT 背板、驱动 IC、FPC 等一起被封装成 **电子纸显示模组**，也就是我们看到的屏幕。

电子墨水薄膜的市场基本由台湾厂商 [E Ink (元太科技)](https://www.eink.com)垄断，剩余份额由广州奥翼 OED 等中国大陆厂商占据。

电子墨水薄膜由电子墨水（Electronic Ink）层压至塑料薄膜上得到。电子墨水可以说是 EPD 最核心的材料。主流的电子墨水解决方案如下：

- [E Ink Carta](https://www.eink.com/brand/detail/Carta1000)：双粒子墨水系统，电纸书上最主流的黑白方案，通过波形驱动（waveform）控制黑白粒子的空间分布可以实现 16 级灰度。
- [E Ink Kaleido](https://www.eink.com/brand/detail/Kaleido)：Kaleido = 黑白 Carta + 印刷式 CFA（Color Filter Array，彩色滤光片阵列），电纸书主流的彩色方案。滤光片共有 RGB 三种颜色，配合 16 级灰度，所以总共有 $16\times16\times16=4096$ 种颜色。由于 RGB 滤光片会吸收相当一部分光线，所以 Kaleido 的彩色显示不够鲜艳，看起来色彩饱和度偏低。
  你可能还会注意到 Kaleido 的黑白 PPI 是 300、彩色 PPI 却只有 150。因为 Kaleido 单个像素只能显示 16 阶灰度 R、G、B 三种颜色中的一种，所以需要 $2\times2=4$ 个像素格来组合成一个彩色像素，单位长度上的有效彩色像素数只有黑白的一半，PPI 自然只有 150。
- [E Ink Gallery](https://www.eink.com/brand/detail/Gallery_3)：四粒子墨水系统，基于 E Ink ACeP 平台，通过对 Cyan（青）、Magenta（洋红）、Yellow（黄）、White（白）四色粒子在纵向位置上的排列组合，无需 CFA，单个像素就能实现全彩。由于不需要多个像素组合来显示彩色，E Ink Gallery 的彩色 PPI 也能做到 300。高端电子纸产品 [reMarkable Paper Pro](https://remarkable.com/products/remarkable-paper/pro) 就用了 Gallery 技术，虽然从参数上来看应该不是 E Ink 自家的电子纸模组。
- 更多电子墨水技术请参考 [E Ink-Electronic Ink: how it works](https://www.eink.com/tech/detail/How_it_works)

电子纸显示模组的制造主要由京东方、东方科脉等中国大陆厂商完成，元太科技自身也进行部分模组生产。

根据专业咨询机构 *CIC 灼识* 为东方科脉所做的[行业调研](https://www1.hkexnews.hk/listedco/listconews/sehk/2026/0709/12238622/sehk26062201964_c.pdf)，2025 年全球电子纸销量 552.5M 片，其中商用市场 533.8M 片，核心应用领域是智慧零售（比如电子纸标签），消费端市场 18.8M 片，核心应用领域是电纸书。可见相比商用市场，用在电纸书上的电子纸模组数量其实相当少，期待未来有更大的增长。
{{< /details >}}

#### 屏幕

目前千元价位的电纸书大部分都宣称了自己使用 E Ink Carta 1300，但这并不代表各家电纸书的电子纸显示模组完全相同，TFT 背板、光学贴合/前光/盖板等仍然可能存在差异，这些同样会影响显示效果。甚至于在波形驱动方面，封闭系统的 Kindle 可以完全以静态文字 / 图片显示质量为优先，而开放系统产品还需要考虑第三方 App 的显示效果，二者的波形驱动模式可能存在差别。

除此之外显示算法对显示效果也会产生影响，同样的书，不同的图片灰阶映射策略（256 灰阶 -> 16 灰阶）、不同的字体渲染，最终的显示效果都会有差异。

就民间评论来看，很多用户的评价是相比国产电纸书， Kindle 有着更好的显示效果，文字和漫画都更加清晰。如果你对显示效果有较高的要求，可以考虑一下 Kindle。

#### CPU + 内存 + 存储

除此之外 Kindle 的硬件配置是比较低的，其主力产品 Kindle Paperwhite 6 还在用着 MediaTek MT8113（1x A53 1 GHz）+ 512MB LPDDR4 内存 + 16GB 存储的配置。
相近价位的国产电纸书已经是 MediaTek MT8175B（4x A53 2.0 GHz）/ Snapdragon 662（4× Kryo 260 Gold + 4× Kryo 260 Silver）+ 2-4GB 内存 + 32GB/64GB 存储的配置。

尽管封闭系统使得 Kindle 对于电子书阅读的优化更加容易，但是配置上的巨大差距仍然导致 Kindle 上的一些操作较为卡顿。如果你对系统流畅度就较高的需求，请选择国产电纸书。

#### 稳定性

我认为电纸书是一个相当长期主义的电子产品，10 年的使用寿命是基本线。Kindle 的稳定性是有口皆碑的，很多 Kindle 十几年了仍然正常服役。现在国内购买 Kindle 的缺点是没有官方售后，购买时可以考虑选择那种提供一年以换代修的商家。国产电纸书建议参考网上评价，具体型号具体分析。

### 软件

#### 第三方 App

如果你目前是微信读书、番茄、起点等 App 的重度用户，那么一款开放系统（可以装安卓 App）的国产电纸书更适合你。Kindle 是封闭系统，不支持第三方 App。

#### 格式支持

主要差异就是 PDF 支持。

Kindle 原生系统不支持 PDF 重排，加上较低的硬件配置，拖动和缩放都非常卡顿，PDF 阅读体验我只能用糟糕来形容。虽然可以越狱后安装 KOReader 来支持 PDF 重排，但我相信大部分用户是不愿意折腾的。

如果你有较多的 PDF 阅读需求，请左转国产电纸书。

#### 易用性

对于财力雄厚，只购买正版书阅读的小伙伴，Kindle 仍然可以算作是一个开箱即用的产品。

但如果你有阅读第三方书籍的需求，Kindle 是比较折腾的一个产品，哪怕最简单的 Send to Kindle 的同步速度也一言难尽。~~如果你就是喜欢折腾，那 Kindle 的可玩性也不错，相关资源也非常丰富。~~

相比之下，国产电纸书的 Wifi 传书，蓝牙传书、微信传书，网盘下载等功能，显著提升了易用性。而且因为是开放系统，你直接在文石、掌阅上装 Kindle app 来 cosplay Kindle 也是可以的。

### 总结

Kindle 更纯粹，国产电纸书更强大。Kobo、PocketBook 等国内较为小众的品牌这里就不讨论了。

## 根目录主要结构

```text
Kindle 盘符/
├── documents/        # 核心电子书与个人文档目录（所有书本均在此）
│	├── dictionaries/ # 词典
│	│
│	├── xxx.azw3/.kfx # 主电子书文件 
│	└── xxx.sdr/      # 对应伴生数据目录
├── fonts/            # 用户自定义字体目录（可放入 .ttf / .otf 字体）
├── audible/          # 官方有声书存放路径（仅部分支持机型可见）
└── system/           # 系统隐藏文件夹（存放索引数据库、封面缓存与系统日志）
```
## 字体

目前 Kindle 支持 OTF 和 TTF 格式的字体，直接将字体文件放到 `fonts` 文件夹即可。

## 字典

官方的字典不够好用，尤其是阅读文言文的时候。

第三方字典获取渠道：[书伴-字典下载](https://bookfere.com/dict)，下载后放入 `documents\dictionaries\` 目录即可。

## 书籍

电纸书当然是用来看书的。

### 书籍来源

- 美亚官方商城购买：支持银联、Visa、Mastercard 等支付方式
- ~~Anna’s Archive~~
- ~~Z-library~~
- 更多资源见 [书伴-Kindle 图书资源](https://bookfere.com/ebook)

### 本地书籍导入
#### Send to Kindle

最方便的导入方式，导入后的书籍全平台同步（包括批注、高亮等）。

支持的导入方式：
- 直接通过邮件将书籍发给你的 kindle 邮箱，记得在 Amazon 官网设置中添加用于发送邮件的邮箱
- [Web 端](https://www.amazon.com/sendtokindle)
- 移动端 App
- 桌面端 App
- Chrome 扩展

支持格式：
- **EPUB (.EPUB)**
- HTML (.HTML, .HTM) 
- RTF (.RTF) 
- Text (.TXT) 
- JPEG (.JPEG, .JPG) 
- GIF (.GIF) 
- PNG (.PNG) 
- BMP (.BMP) 
- PDF (.PDF)：Kindle 不支持 PDF 重排，除了 **Kindle Scribe** 这种 10 英寸+ 的设备，别的 Kindle 设备看 PDF 有点折磨的。 

没错你没有看错现在的 Send to Kindle **不支持** .azw（KF7，本质 .mobi）.azw3（KF8）.kfx（KF10）这些 Kindle 专用文件格式。

Send to Kindle 当前的思路是全面转向 .epub，随后由亚马逊云端服务完成 校验 & 编译 -> 自动分发设备兼容的格式 全流程。

#### 数据线传输

Kindle 通过数据线连接电脑，直接将书籍文件复制到 `documents` 文件夹即可。

该传书方式不是很推荐，潜在的问题包括但不限于：
- 封面消失
- 联网后本地书籍被删除（当然被删的理论上算盗版书）
- 阅读进度、标注和笔记无法全平台同步

一定要用的话可以使用 Calibre 作为本地图书管理和格式转换工具。

## 越狱

目前（截止 2026.8.26）全 Kindle 系统版本可越狱，越狱后可以安装插件或第三方系统。

- 越狱方法参考 [Kindle Modding Wiki](https://kindlemodding.org/jailbreaking/prevent-auto-update/)
- 中文越狱教程参考 [书伴-越狱专题](https://bookfere.com/category/skills/jailbreak)

### [KOReader](https://github.com/koreader/koreader)

最流行的 Kindle 第三方软件，补齐 Kindle 不支持 PDF 重排和不能原生阅读 EPUB 格式的短板。 

KOReader 是个软件，与原生系统并存，你可以指定一部分文件格式用 KOReader 开启，剩余格式用原生系统阅读，非常方便。

安装方式：

- [官方英文教程](https://github.com/koreader/koreader/wiki/Installation-on-Kindle-devices)：直接使用 KPM（Kindle Package Manager）在线安装，非常方便。
- [书伴中文教程](https://bookfere.com/post/311.html#p_3)：没有那么与时俱进，但也够用了。

### Others

别的插件站在我个人视角来看用处不大，感兴趣的可以自己探索，推荐参考 [MobileRead Forums-Kindle Developer's Corner](https://www.mobileread.com/forums/forumdisplay.php?s=082a7b795d0954ae97ce4eb8d04801be&f=150) 和 [书伴-Kindle 越狱插件资源下载及详细安装步骤](https://bookfere.com/post/311.html)。

## 推荐网站

- [书伴](https://bookfere.com)：非常推荐，至今依然活跃的中国 Kindle 信息与教程网站，内容质量很不错。
- [Wikipedia-Amazon Kindle](https://en.wikipedia.org/wiki/Amazon_Kindle)：包含每一款 Kindle 的设备参数
- [MobileRead Forums-Kindle Developer's Corner](https://www.mobileread.com/forums/forumdisplay.php?s=a36ce7117563d020ae347f86b00f910e&f=140)：很多越狱插件的原始发布站点，想折腾的话值得深入探索。

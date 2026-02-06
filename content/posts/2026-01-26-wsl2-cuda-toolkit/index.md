---
title: "WSL2 使用 CUDA Toolkit"
date: 2026-01-26T12:11:03+08:00
# weight: 1
# aliases: ["/first"]
tags: ["CUDA", "System"]
author: "Square Zhong"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "在 WSL2 中使用 CUDA Toolkit 基本无需额外配置."
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

## Installation
默认你已经成功安装 WSL2 和某个 Linux 发行版（如 Ubuntu24.04）。

1. **安装 Nvidia 显卡 Windows 版驱动**，该驱动会自动“映射”进 WSL2。系统会自动在 WSL2 的 `/usr/lib/wsl/lib/` 目录下生成 libcuda.so 等文件。
    <span style="color: red;">**不要在 WSL2 中安装 Linux 版本的显卡驱动！**</span>，否则会覆盖映射进来的 Windows 版驱动，导致潜在的问题。

2. 根据需求安装所需版本的 [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=WSL-Ubuntu&target_version=2.0&target_type=deb_network)。
    如果你只是希望使用 Pytorch，不需要编译自定义 CUDA 算子，那么无须手动安装 CUDA Toolkit，按照 Pytorch 官方文档安装即可，Pytorch 会自带所需的 CUDA 运行时库。

## Test
To be continued...
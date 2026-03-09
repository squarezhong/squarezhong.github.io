---
title: "Raspberry Pi 4B 折腾记录之 OpenClaw"
date: 2026-03-09T14:07:08+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Server", "System", "DIY", "AI", "OpenClaw"]
author: "Square Zhong"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "生命不息，折腾不止。"
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

主要参考[Raspberry Pi - OpenClaw](https://docs.openclaw.ai/platforms/raspberry-pi)

硬件：
- Raspberry Pi 4B 2GB 内存
- 三星 128GB 白卡
## 准备工作
### 系统安装
使用官方工具 [Raspberry Pi Imager](https://www.raspberrypi.com/software/) 将系统烧录至 tf 卡。
安装的系统版本为 Raspberry Pi OS Lite (64-bit)
- Kernel version: 6.12
- Debian version: 13 (trixie)
### 修改镜像源
该版本的镜像源采用 DEB822 格式
#### Debian 镜像源修改
参考[debian | 镜像站使用帮助 | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/help/debian/)
```shell
sudo nano /etc/apt/sources.list.d/debian.sources
```
注释原内容后添加以下内容：
```
Types: deb
URIs: https://mirrors.tuna.tsinghua.edu.cn/debian
Suites: trixie trixie-updates trixie-backports
Components: main contrib non-free non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg

# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
# Types: deb-src
# URIs: https://mirrors.tuna.tsinghua.edu.cn/debian
# Suites: trixie trixie-updates trixie-backports
# Components: main contrib non-free non-free-firmware
# Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg

# 以下安全更新软件源包含了官方源与镜像站配置，如有需要可自行修改注释切换
Types: deb
URIs: https://security.debian.org/debian-security
Suites: trixie-security
Components: main contrib non-free non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg

# Types: deb-src
# URIs: https://security.debian.org/debian-security
# Suites: trixie-security
# Components: main contrib non-free non-free-firmware
# Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg
```
#### Raspberrypi 镜像源修改
```shell
sudo nano /etc/apt/sources.list.d/raspi.sources
```
注释原 `URIs` 行后修改为以下内容：
```
# URIs: http://archive.raspberrypi.com/debian/
URIs: https://mirrors.tuna.tsinghua.edu.cn/raspberrypi/
```
#### 应用修改
```shell
sudo apt update
```
### 包升级与安装
```shell
sudo apt upgrade
sudo apt install vim
sudo apt install -y git curl build-essential

# node
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs
# verify
node --version
npm --version
```
### 添加 swap 文件及内存优化
```shell
# Create 2GB swap file
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make permanent
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# Optimize for low RAM (reduce swappiness)
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```
## 安装 OpenClaw
**龙虾，启动！**

```shell
curl -fsSL https://openclaw.ai/install.sh | bash
```
此刻跟随命令行交互进行设置或者稍后运行 `openclaw config` 进行配置。
安装完成后你需要重启客户端或者运行 `source ~/.bashrc`
## OpenClaw 配置
没有特意列出的选项均为默认

### Model
OpenRouter -> StepFun: Step 3.5 Flash (free)
测试场景，选一个免费、推理速度快、性能尚可的模型。
### Gateway
- port: 18789 
- bind mode: All interface (LAN)
### Channels
#### 飞书
在大陆还是使用飞书或者钉钉比较方便，没有网络问题。
参考[OpenClaw飞书官方插件](https://www.feishu.cn/content/article/7613711414611463386)完成飞书插件的安装以及飞书机器人的接入。
### Skills
按需安装。
### Dashboard
通过 `http://<raspberry-pi IP>:18789` 访问 OpenClaw Dashboard，如遇到问题请参考 [Troubleshooting](#troubleshooting)。
将上一步配置 Gateway 时生成的 toekn 填入 [Overview]-[Gateway Access]-[Gateway Token]
## 应用场景
TODO
## Troubleshooting
### 浏览器访问 Dashboard 显示安全问题
由于树莓派安装的是 server 版本的系统，我们通过设备的浏览器以 `http://<raspberry-pi IP>:18789` 的地址访问 dashboard，通常会遇到以下两个问题：
1. `origin not allowed (open the Control UI from the gateway host or allow it in gateway.controlUi.allowedOrigins)`
2. `control ui requires device identity (use HTTPS or localhost secure context)`

解决方案：
- 允许特定源 & 禁用设备身份检查。
```shell
openclaw config set gateway.controlUi.allowedOrigins '["http://<your-droplet-ip>:18789"]' --strict-json
openclaw config set gateway.controlUi.dangerouslyDisableDeviceAuth true
openclaw gateway restart
```
**请注意该解决方案并不安全，仅供测试使用！**

更安全的方案应该是 **SSH Tunnel** 或者 **Tailscale**。




---
title: "闲置笔记本折腾记录之 fnOS"
date: 2026-03-07T20:58:08+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Server", "System", "DIY"]
author: "Square Zhong"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "哎，还是折腾。"
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
差不多一年前的这个时候，我在[NAS: 从入坑到入土](https://squarezhong.github.io/posts/2025-04-03-nas-unnecessary/)这篇文章中写下了这样一句话：
> 需求应当被满足，而不是被创造。

那么现在需求回来了，“NAS”也就回来了。
~~其实就是给自己折腾找个理由。~~

本次用于安装 fnOS 的设备为神舟 G7-CT7NA，17寸的究极傻大黑粗，核心配置为 Intel i7-9750h + Nvidia 1660ti，作为 NAS 来说性能功耗比显然是不合格的，不过既然是闲置设备也就不要求那么多了。

这台笔记本有两个 m2 硬盘位和一个 2.5 英寸 SATA 机械硬盘位，安装的存储如下：
-  Intel 760p，512G
- Samsung PM9C1A，1TB
- TOSHIBAMQ04ABD200，2TB 叠瓦
## 安装
从[官网](https://www.fnnas.com/download)下载镜像后参考[帮助中心 - 飞牛 fnOS](https://help.fnnas.com/articles/v1/start/install-os.md)安装即可。
## fnOS 功能设置
### 存储空间管理
按需自行创建存储空间，因为我三块硬盘的容量都不一样，所以直接建了 Baisc 分区。如果有重要数据请务必使用 RAID。
### 远程访问
由于暂时没有该需求，加上可能的安全性问题（请自行搜索），故不开启。
### SSH
为了修改一些笔记本特定底层设置，故开启，顺带修改了一下访问端口。
### 安全性
- 端口设置中强制开启 HTTPS 访问
- 账号安全内启用强制双重验证
- 启用防火墙（如果修改了 SSH 端口记得放行）
### 文件共享协议
默认开启。
#### 其他 Linux 设备挂载 SMB 文件夹的解决方案
安装组件
```shell
sudo apt update
sudo apt install cifs-utils
```
创建凭据文件（安全起见不将 SMB 账号密码明文写在 `/etc/fstab` 中）
```shell
vim  ~/.smbcredentials
```
在文件中写入 fnOS 的账号密码信息
```
username=<your smb username>
password=<your smb password>
```
保存退出后修改文件权限
```shell
chmod 600 ~/.smbcredentials
```
创建挂载点
```shell
sudo mkdir -p /mnt/Contents
```
修改 `\etc\fstab`
```shell
sudo vim /etc/fstab
```
在其中添加 SMB 挂载参数
```
//<device IP>/<shared folder name> /mnt/Contents cifs credentials=/home/<your Ubuntu username>/.smbcredentials,uid=1000,gid=1000,iocharset=utf8,_netdev,x-systemd.automount,x-systemd.requires=network-online.target 0 0
```
部分参数解释：
- `_netdev`：告诉系统这是一个网络设备，等待网络就绪后再处理。
- **`x-systemd.automount`**：**（断线重连的核心）** 启用按需挂载。如果 SMB 设备掉线，一旦设备恢复，再次访问该目录就会自动重新挂载。
- `x-systemd.requires=network-online.target`：确保网络完全在线后再进行挂载尝试。

重新加载并测试
```shell
sudo systemctl daemon-reload
sudo systemctl restart remote-fs.target
ls /mnt/Contents
```
#### Time Machine
手里有 mac 设备，所以开启了。
尽量使用 SSD 用于 Time Machine 备份，不要使用 HDD，不然查找历史文件的速度可能会慢到你怀疑人生（尤其是叠瓦盘）。
### 通知设置
配置了 QQ 邮箱的发送服务。
### 硬件和电源
启用 UPS 支持，可以使用笔记本内置电源作为 UPS。
## 辅助系统设置
因机而异。
### 限制 CPU 频率
由于这台笔记本 CPU 频率超过 3.9 GHz 就必定死机，所以需要手动限制一下最高频率。
笔者使用 [`auto-cpufreq`]([https://github.com/AdnanHodzic/auto-cpufreq](https://github.com/AdnanHodzic/auto-cpufreq)) 作为工具

安装 `auto-cpufreq`
```bash
git clone <https://github.com/AdnanHodzic/auto-cpufreq.git>
cd auto-cpufreq && sudo ./auto-cpufreq-installer
```

修改配置文件
```bash
sudo vim /etc/auto-cpufreq.conf
```

添加如下内容
```
# settings for when connected to a power source
[charger]
governor = performance
energy_performance_preference = performance
scaling_max_freq = 3900000
turbo = auto

# settings for when using battery power
[battery]
governor = powersave
energy_performance_preference = power
scaling_max_freq = 3900000
turbo = auto
```

你可以参考官方提供的[示例文件]([AdnanHodzic/auto-cpufreq: Automatic CPU speed & power optimizer for Linux](https://github.com/AdnanHodzic/auto-cpufreq?tab=readme-ov-file#example-config-file-contents))按需修改你的配置文件。

安装守护进程以持久化修改
```bash
sudo auto-cpufreq --install
```

使用 `auto-cpufreq --monitor`来监控 CPU 状态。

### 配置合盖不关机

```bash
sudo vim /etc/systemd/logind.conf
# find a line
#HandleLidSwitch=suspend
# change it to
HandleLidSwitch=ignore
# restart service
sudo systemctl restart systemd-logind
```
## fnOS 应用推荐
### 官方 App
官方 App 有需要直接安装即可，影视、相册等做得还是非常不错的。
顺带期待一手官方的音乐 App。
### 第三方 App
考虑到可迁移性的问题，所有第三方 App 我都会选择用 Docker 自行部署。
### Docker
支持 Docker Compose 且自带镜像仓库，好评。
由于需要用到的 Docker 服务已经在[闲置笔记本折腾记录之 Ubuntu Server | Square Zhong's Blog](https://squarezhong.github.io/posts/2026-01-16-laptop-server/)中配置好了，故此处不再额外配置，仅对 qBittorrent 所挂载的下载盘进行修改。
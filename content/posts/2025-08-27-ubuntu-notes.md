---
title: "Ubuntu: 版本答案"
date: 2025-08-27
tags:
  - System
  - Ubuntu
author: Square Zhong
description: 某种意义上已经是事实标准了
---

默认讨论 Ubuntu Server，Ubuntu Server 和 Ubuntu Desktop 的稳定性和使用体验完全是天壤之别。

人生苦短，我不用 Linux Desktop。实在要用的建议使用 Gnome 之外的图形界面。

## Network

网络的相关配置在中国大陆太重要了，如果你解决了网络问题，那你已经解决了全部问题的一半。

### SSH

Ubuntu Server 安装时自带 OpenSSH Server，可以直接连接。

### Proxy

海的那边原来是网络。

#### System Proxy

左转 Mihomo，在此不能多说。

#### Docker Proxy

Docker 不会自动走系统代理（`HTTP_PROXY` 及 `HHTPS_PROXY`），独立代理配置参考博客 [Docker: 虚拟世界的集装箱船](https://squarezhong.github.io/posts/2026-10-26-personal-docker-guide/#%E4%BB%A3%E7%90%86%E4%B8%8E%E9%95%9C%E5%83%8F)。

#### SSH Tunnel

如果你通过一台已经配置代理的电脑连接服务器，那其实可以直接让服务器通过 SSH 隧道正常连接互联网

参考我的 [SSH 笔记](https://squarezhong.github.io/posts/2026-04-10-ssh-notes/#ssh-%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6)

### Firewall

#### ufw

查看状态和规则

```bash
sudo ufw status verbose
# 显示规则编号
sudo ufw status numbered
```

放行特定端口（以 9100 为例）

```bash
sudo ufw allow 9100/tcp
```

删除对应的放行规则

```bash
sudo ufw delete allow 9100/tcp
```

启用防火墙。通过 SSH 连接时，务必提前放行实际使用的 SSH 端口（默认 22），避免失去远程访问。

```bash
sudo ufw allow 22/tcp
sudo ufw enable
```

停用防火墙（会停止 UFW 的过滤保护）

```bash
sudo ufw disable
```

参考：[UFW 手册](https://manpages.ubuntu.com/manpages/noble/man8/ufw.8.html)。UFW 与 firewalld 选择一种管理即可，避免同时启用。

#### firewalld

查看 public zone 的运行时配置（加 `--permanent` 查看永久配置）

```bash
sudo firewall-cmd --zone=public --list-all
```

放行特定端口（以 9100 为例）

```bash
sudo firewall-cmd --zone=public --add-port=9100/tcp --permanent
# 重新加载规则
sudo firewall-cmd --reload
```

#### 云服务器设置

有的时候端口连接不上不是系统设置的问题，而是你在阿里云、腾讯云等云服务器的防火墙设置页面中没有放行所需端口。

### Troubleshooting

{{< details summary="Change network interface name" open=false >}}
- Ref [systemd.link](https://www.freedesktop.org/software/systemd/man/systemd.link.html)

```shell
cd /etc/systemd/netowrk
sudo touch 10-wlan.link
sudo chmod 777 10-wlan.link
sudo vim 10-wlan.link
```

Insert follow content:
_This example assigns the fixed name "`wlan0`" to the interface with the MAC address 00:a0:de:63:7a:e6:_

```
[Match]
MACAddress=00:a0:de:63:7a:e6

[Link]
Name=wlan0
```

`NamePolicy=` is not set, so `Name=` takes effect. We use the "`10-`" prefix to order this file early in the list. Note that it needs to be before `99-default.link`, i.e. it needs a numerical prefix, to have any effect at all.
{{< /details >}}

{{< details summary="Ubuntu 虚拟机网络设置选项消失" open=false >}}
`lshw`: list hardware
```bash
lshw -C net
```

you may see `*-network DISABLED`
then run `sudo nmcli connection`


If no device is assigned for network
Refer to [https://askubuntu.com/questions/1190504/why-is-nmcli-not-configuring-device](https://askubuntu.com/questions/1190504/why-is-nmcli-not-configuring-device)
run `nmcli networking on` to enable network control by NetworkManager


Otherwise
Refer to [https://askubuntu.com/questions/1341472/ubuntu-20-04-network-disabled](https://askubuntu.com/questions/1341472/ubuntu-20-04-network-disabled)
Find a way to make `NetworkingEnabled=true`
Open `/etc/NetworkManager/NetworkManager.conf`

You may see
```bash
[ifupdown]
managed=false
```
change false to true only works when an `/etc/network/interfaces` is in use (before ubuntu 17.0)
{{< /details >}}

## Packages

### Sources Mirror

如果你已经在 [Network](#network) 部分设置了 proxy，那么可以跳过这个环节。

为了缓解国内镜像源时常出现对上游官方源同步失败的情况，建议直接使用[教育网联合镜像站]()，自动选择网络条件最佳的镜像站。配置方式见镜像站帮助。

#### 删除不用的源

- 传统格式（Ubuntu 24.04 之前）：删除 `/etc/apt/sources.list` 中对应的行
- DEB822 格式：删除 `/etc/apt/sources.list.d/ubuntu.sources`

### Installing

在软件仓库源种搜索

```shell
sudo apt search <package-name>
```

从软件仓库源安装

```shell
sudo apt install <package-name>
```

查看包的依赖

```bash
sudo apt show <package-name>
```


手动安装 `.deb` 文件，`apt` 会自动处理依赖问题

```bash
sudo apt install <package-path>
```

不推荐使用 `sudo dpkg -i` 进行安装，`dpkg` 不会自动处理依赖问题。

### Uninstalling

查看已安装的包

```shell
dpkg -l | grep -i <package_name>
```

删除包并保留系统配置文件

```shell
sudo apt remove <package_name>
```

删除包及其系统配置文件

```shell
sudo apt purge <package_name>
```

### Sanpd

这玩意实在太难用了，尤其是在大陆使用还会容易遇到网络问题，直接删了。

```bash
sudo apt purge snapd
```

## Development

### 自动化脚本

目前主要通过自动化脚本一键配置，参考 https://github.com/squarezhong/dotfiles

一些 dotfiles 中没有的开发环境配置参考：

### Java

```shell
sudo apt update
sudo apt install openjdk-25-jdk -y
# 如果需要切换默认 jdk 版本
sudo update-alternatives --config java

# 部分软件需要环境变量 JAVA_HOME
echo 'JAVA_HOME="/usr/lib/jvm/java-25-openjdk-amd64"' | sudo tee -a /etc/environment > /dev/null
source /etc/environment
# 验证环境变量是否生效
echo $JAVA_HOME
```

### Miniforge

```shell
# 下载 Miniforge 安装脚本
curl -L -O "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
# 安装
bash Miniforge3-$(uname)-$(uname -m).sh
# 安装完成后根据提示初始化 conda
```

### LaTeX

参考 [All-platform Local LaTeX Solution](https://squarezhong.github.io/posts/2025-04-24-local-latex/)

### NPM

#### 使用 nvm 进行管理

自动化脚本中已经安装了 nvm，也可以选择手动安装

```shell
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.8/install.sh | bash
```

几个最常用的命令

```shell
# 安装特定版本的 node
nvm install 24

# 使用特定版本的 node
nvm use 24

# 设置别名
nvm alias my_alias 22
# 若已安装对应版本 node 可以直接使用别名
nvm use my_alias
```

#### 修改 NPM 全局安装路径

对于多用户的服务器来说，用户不一定有读写 npm 默认全局安装路径的权限，所以将其修改为安装在用户目录的自定义文件夹。

```shell
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

接下来尽情使用 npm 的全局安装命令吧。

## Docker

参考 [Docker: 虚拟世界的集装箱船](https://squarezhong.github.io/posts/2026-10-26-personal-docker-guide/)

## System

### Check system info

Check partitions info

```bash
$ parted -l
$ df
```

check date and set timezone

```bash
$ date
$ timedatectl list-timezones
$ sudo timedatectl set-timezone Asia/Shanghai
```

check disk usage

```bash
sudo du -h --max-depth=1
```

**Ubuntu check user group**

```bash
sudo groups <USER>
```

**Change the password**

```bash
sudo passwd <user_name>
```

## Troubleshooting

### 双系统中彻底卸载 Ubuntu

献给 Ubuntu 桌面版用户。

1. 进入 BIOS，设置 Windows 为第一启动项
2. 在 Windows 删除 Ubuntu 所占用的分区。如果你的 Ubuntu 使用独立的 EFI 分区，那么到这一步就已经完成卸载了。
3. 如果你的 Ubuntu 和 Windows 共用同一个 EFI 分区，先使用 diskpart 挂载 EFI 分区（终端窗口不用关）

```powershell
diskpart
list disk
# 选择 EFI 所在的硬盘和分区
select disk 0
list partition
select partition 1
# 分配盘符 P
assign letter = p
```

4. 在文件管理器中选择磁盘 P，删除 Ubuntu 所在的文件夹，比如 `\EFI\ubuntu`
5. 回到 diskpart 取消盘符分配并退出

```powershell
remove letter = p
exit
```

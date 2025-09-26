---
title: "使用 frp 实现内网穿透"
date: 2024-09-09T12:11:03+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Network", "System"]
author: "Square Zhong"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "折腾是对的"
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

## 前置准备

1. 需要进行穿透的机器
2. 一台云服务器
3. 一个域名（如果在大陆使用最好在云服务器提供商处购买，备案会比较方便）

## 配置 frp

项目地址：https://github.com/fatedier/frp

文档地址：https://gofrp.org/zh-cn/

### Server

1. 下载 frp

    ```bash
    # make sure you choose the filename corresponds to your system architecture
    $ wget https://github.com/fatedier/frp/releases/download/v0.60.0/frp_0.60.0_linux_amd64.tar.gz
    $ tar -zxf frp_0.60.0_linux_amd64.tar.gz
    $ mv frp_0.60.0_linux_amd64 frp
    ```

    frp 文件夹包含以下文件：

    ```bash
    LICENSE   
    frpc      
    frpc.toml 
    frps      
    frps.toml
    ```

2. 编辑 frp server 配置文件

    `$ vim ~/frp/frps.toml`

    添加如下内容：

    ```bash
    bindPort = 7000 # any port you like
    ```

3. 使用 `systemd` 管理 frps 服务

    首先创建一个 frps 服务文件

    `$ sudo vim /etc/systemd/system/frps.service`
    添加如下内容：

    ```bash
    [Unit]
    Description = frp server
    After = network.target syslog.target
    Wants = network.target

    [Service]
    Type = simple
    # change the path to real absolute path
    ExecStart = /path/to/frps -c /path/to/frps.toml

    [Install]
    WantedBy = multi-user.target
    ```

    开始服务并且设置为自启动

    ```bash
    $ sudo systemctl start frps
    $ sudo systemctl enable frps
    ```

### Client

1. 下载 frp（同 server 端）

2. 编辑 frp client 配置文件

    `$ vim ~/frp/frpc.toml`

    添加如下内容：

    ```bash
    serverAddr = "x.x.x.x" # change it to your server's ip address
    serverPort = 7000

    # reconnect if fail
    loginFailExit = false

    [[proxies]]
    name = "web"
    type = "tcp"
    localPort = 5080 # any port you like
    remotePort = 6080 # any port you like
    ```

3. 使用 systemd 管理 frpc 服务

    首先创建一个 frpc 服务文件

    `$ sudo vim /etc/systemd/system/frpc.service`

    添加如下内容

    ```bash
    [Unit]
    Description = frp client
    After = network.target syslog.target
    Wants = network.target

    [Service]
    Type = simple
    # change the path to real absolute path
    ExecStart = /path/to/frpc -c /path/to/frpc.toml
    Restart = always
    RestartSec = 5

    [Install]
    WantedBy = multi-user.target
    ```

    开始服务并且设置为自启动

    ```bash
    $ sudo systemctl start frpc
    $ sudo systemctl enable frpc
    ```

现在你可以使用 `[server ip]:port` (e.g. [ip]:6080) 来远程访问你的客户端机器服务。

## 配置 Nginx 反向代理

使用 [ip]:port 访问的方式既不优雅也不安全，推荐使用 nginx 反向代理的方式实现域名访问并且隐去端口号。

### 设置域名解析

访问你的域名提供商，它应当会提供域名解析服务。

将你要使用的域名解析到云服务器的 公网 ip

### 设置反向代理

个人实践过程中才用过以下三种方式

- **Nginx Proxy Manager** (Recommended)
    
    使用 docker 部署 Nginx Proxy Manger，非常优雅。
    
    在 [SSL Certificate] 中申请域名对应的 Let’s Encrypt 证书。
    
    申请过程中 [DNS Challenge] 选择你的解析服务提供商，比如我的域名是在腾讯云购买的，就选择 [DNSPod], id 和 tokens 在 dnspod api 界面生成，不同服务商可能存在差异。
    
- 宝塔面板（极其不稳定，强烈不推荐使用）
    
- 使用 `certbot` 或许证书，在 Nginx 手动配置。
    
    你都选择这样操作了，应该不需要看这种教程，直接看官方文档就好了。
    

现在你可以直接使用域名访问你的内网服务。

如果你的选择的云服务器在国内，记得**备案**！
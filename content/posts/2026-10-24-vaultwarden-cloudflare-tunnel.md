---
title: 部署 Vaultwarden + Cloudflare Tunnel 实现跨平台密码管理
date: 2026-10-24
tags:
  - Server
  - Passwords
  - NAS
  - DIY
author: Square Zhong
description: 买个会员更方便
---

本文是 [近期关于数字资产整合的尝试](https://squarezhong.github.io/posts/2026-05-06-digital-assets-integration/) 这篇 blog 中 Vaultwarden + Cloudflare Tunnel 的具体实现，拖了五个月之久。

选择 Bitwarden 最大的理由是它是一个开源的跨平台密码管理器，服务端也可以自部署。

本文使用 Docker Compose 部署兼容服务端 [Vaultwarden](https://github.com/dani-garcia/vaultwarden)（相比官方开源服务端更加轻量），搭配官方客户端使用，并通过 Cloudflare Tunnel 提供 HTTPS 访问，无需公网 IP。

不想折腾的话也可以直接使用 Bitwarden [官方](https://bitwarden.com/pricing/) 的服务，拼家庭组的话一年不到 8 刀。

## 前置准备

- 一台能够长期运行的 Linux 主机或 NAS，已安装 Docker 和 Compose v2。
- 一个已接入 Cloudflare 的域名，本文以 `vault.example.com` 为例。
- 主机能够访问镜像仓库和 Cloudflare Tunnel 网络。

访问链路如下：

```text
Bitwarden 客户端 → Cloudflare HTTPS → Tunnel → cloudflared → vaultwarden:80
```

## 创建 Cloudflare Tunnel

在 [Cloudflare 后台](https://dash.cloudflare.com/) 的 **Networking → Tunnels** 中创建 Tunnel，命名为 `vaultwarden-tunnel`（或者别的你喜欢的名字）。如果界面要求选择连接器，选择 `cloudflared`。

在安装连接器页面的 Operating System 中选择 Docker，复制命令中 `--token` 后面的完整 Token。请保存好该 Token，泄露后应立即在后台轮换。

## 部署服务

### 创建目录

NAS 用户可以换成自己的持久化存储路径。

```bash
mkdir -p "$HOME/docker/vaultwarden/vw-data"
cd "$HOME/docker/vaultwarden"
```

部署目录结构：

```text
vaultwarden/
├── compose.yaml
├── .env
└── vw-data/
```

`vw-data` 保存数据库、附件和服务端配置。

### 设置管理密码

管理面板使用独立密码，用于管理用户和服务配置。它与个人密码库的主密码分开设置。

用 Vaultwarden 内置命令生成 Argon2id 哈希，避免把管理密码明文写入配置(若当前用户不在 docker 组中，会提示权限不足，将该用户加入 docker 组或者在命令前添加 `sudo`，后续同理）：

```bash
docker run --rm -it vaultwarden/server:latest /vaultwarden hash
```

按提示输入两次高强度密码，保存输出中以 `$argon2id$` 开头的完整 PHC 字符串。具体说明见 [管理面板文档](https://github.com/dani-garcia/vaultwarden/wiki/Enabling-admin-page)。

### 编写配置

在部署目录创建 `.env`，替换其中的域名、Tunnel Token 和管理密码哈希：

```dotenv
DOMAIN=https://vault.example.com
TUNNEL_TOKEN=replace_with_your_tunnel_token
ADMIN_TOKEN='$argon2id$replace_with_your_complete_phc_string'
```

哈希外层保留**单引号**，内部保留原始的单个 `$`。这样 Compose 不会把哈希中的内容当成变量展开。这里的单引号写法适用于 `.env` 文件，不要直接照搬到 `compose.yaml` 的哈希值中。参见 [Compose 变量规则](https://docs.docker.com/compose/how-tos/environment-variables/variable-interpolation/)。

限制文件权限：

```bash
chmod 600 .env
```

同目录创建 `compose.yaml`：

*`docker-compose.yml` 同样被兼容，但更推荐新版标准的 `compose.yaml`*

```yaml
services:
  vaultwarden:
    image: vaultwarden/server:latest
    restart: unless-stopped
    environment:
      DOMAIN: "${DOMAIN:?Set DOMAIN in .env}"
      ADMIN_TOKEN: "${ADMIN_TOKEN:?Set ADMIN_TOKEN in .env}"
      SIGNUPS_ALLOWED: "false"
      INVITATIONS_ALLOWED: "false"
    volumes:
      - ./vw-data:/data

  cloudflared:
    image: cloudflare/cloudflared:latest
    restart: unless-stopped
    read_only: true
    command: ["tunnel", "--no-autoupdate", "run"]
    environment:
      TUNNEL_TOKEN: "${TUNNEL_TOKEN:?Set TUNNEL_TOKEN in .env}"
    depends_on:
      - vaultwarden
```

- 即使不写 `networks`，Compose 也会自动创建一个项目专属的 bridge 网络。当前配置中没有 `ports`，不会向宿主机或局域网发布 Vaultwarden 端口；外部访问统一走 Tunnel。
- `SIGNUPS_ALLOWED` 关闭公开注册
- `INVITATIONS_ALLOWED` 关闭普通组织邀请，管理员仍可在管理面板邀请用户。

示例使用 `latest` 简化首次部署。长期使用建议固定经过验证的镜像版本，更新前阅读发布说明并备份。Compose 配置可参考 [Vaultwarden 官方示例](https://github.com/dani-garcia/vaultwarden/wiki/Using-Docker-Compose)。

### 启动容器

在部署目录执行：

```bash
docker compose config -q
docker compose up -d
```

- `config -q` 只检查配置，不输出展开后的 Token。
- 修改 `.env` 或 Compose 配置后，重新执行 `docker compose up -d` 应用新配置。
- fnOS 中修改项目配置后需要重新“构建”才能应用新配置。

## 配置域名访问

容器启动后，回到 Cloudflare 后台，确认 Tunnel 已连接。在该 Tunnel 的 **Routes** 中添加 **Published application**，填写以下内容：

| 项目          | 值                       |
| ----------- | ----------------------- |
| Subdomain   | `vault`                 |
| Domain      | `example.com`，替换为自己的域名  |
| Path        | 留空                      |
| Service URL | `http://vaultwarden:80` |

- Service URL 填写 Compose 服务名。**不要填 `localhost:80`**，那会指向 cloudflared 容器自身。

保存后访问 `https://vault.example.com`，应能看到 Web Vault 登录页。`.env` 中的 `DOMAIN` 必须与实际访问地址一致，包含 `https://`。域名路由设置见 [Cloudflare 文档](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/get-started/create-remote-tunnel/#2a-publish-an-application)。

## 创建账号并接入客户端

### 创建个人账号

打开 `https://vault.example.com/admin`，输入生成哈希时设置的**管理密码**。注意是原始密码，不是 Tunnel Token 或者调用 argon2 生成的哈希字符串。

在用户管理页面邀请自己的邮箱，然后访问：

```text
https://vault.example.com/#/signup
```

使用被邀请的邮箱注册，设置独立且足够长的主密码。这种方式不用临时开放公开注册；未配置 SMTP 时也可以手动进入注册页。见 [注册与管理员邀请说明](https://github.com/dani-garcia/vaultwarden/wiki/Disable-registration-of-new-users#invitations-via-the-admin-page)。

- 请妥善保管主密码，管理面板中无法找回。
- 日常设置尽量在 Compose 中维护。通过 `/admin` 保存服务配置会生成 `vw-data/config.json`，其中的设置优先于环境变量。遇到“配置更改却不生效”，检查是否被该文件覆盖，参见 [配置说明](https://github.com/dani-garcia/vaultwarden/wiki/Configuration-overview)。

### 连接各平台客户端

从 [Bitwarden 官网](https://bitwarden.com/download/)安装浏览器扩展、桌面端和手机 App。

在各客户端登录页选择 **Self-hosted / 自托管**，将服务器地址设为 `https://vault.example.com`，保存后使用刚创建的账号登录。不同平台入口略有区别，见 [客户端连接说明](https://bitwarden.com/help/change-client-environment/)。

- 浏览器建议关闭自带的密码保存提示，避免密码重复保存。
- 手机还需要在系统设置中把 Bitwarden 设为密码自动填充提供方。各设备都要配置自托管地址，Bitwarden 官方云端账号与自建实例互不通用。
- 可以新增一条测试登录项检查跨设备同步和自动填充，确认可用后再迁移正式数据。

## 迁移已有密码

从 Edge、Chrome 或旧密码管理器导出密码，在 Web Vault 的导入功能中选择对应来源。

Passkey 和两步验证（2FA）需要到各自对应的网站上手动操作迁移，较为繁琐，所以不建议频繁更改密码管理器。

迁移完成后检查常用账号和条目数量，确认电脑、手机均能同步与填充，并完成一次备份，再清理旧密码和导出文件。

## 备份与更新

### 备份

短暂停止 Vaultwarden，打包整个数据目录和部署配置实现备份，在部署目录执行以下命令（命令被放入 subshell 中执行，请全部复制，包括头尾的括号，而不是逐行执行）：

```bash
(
  set -eu
  umask 077
  mkdir -p "$HOME/backups/vaultwarden"
  backup="$HOME/backups/vaultwarden/vaultwarden-$(date +%Y%m%d-%H%M%S).tar.gz"
  docker compose stop vaultwarden
  trap 'docker compose start vaultwarden' EXIT
  sudo tar -czf - compose.yaml .env vw-data > "$backup"
)
```

- 备份包含数据库、附件、密钥和配置，也包含 Tunnel 凭据
- 请加密保存备份，并在另一台设备或异地保留副本。

### 更新

完成备份并记下当前镜像版本后，在部署目录执行：

```bash
docker compose pull
docker compose up -d
```

- 固定版本标签的部署需要同时修改镜像标签。
- 更新后检查登录、同步和附件是否正常。
- 若新版升级了数据库，直接换回旧镜像未必能回退，需要使用更新前的备份恢复。

## 常见问题

对于大多数问题，运行以下命令，把输出交给 Agent，跟着它一步步排查：

```shell
docker compose ps
docker compose logs --tail=100 cloudflared vaultwarden
```

| 现象           | 检查内容                                                                          |
| ------------ | ----------------------------------------------------------------------------- |
| 域名无法访问       | Tunnel 是否在线、路由是否保存、主机能否连接 Cloudflare                                          |
| 出现 502       | Vaultwarden 是否启动；Service URL 是否为 `http://vaultwarden:80`；两个容器是否在同一 Compose 网络 |
| 局域网 IP 无法访问  | 本文未发布宿主机端口，局域网客户端也使用 HTTPS 域名                                                 |
| 管理面板密码错误     | 输入生成哈希时的原始管理密码，并检查 `.env` 中哈希是否完整、是否使用单引号                                     |
| 环境变量修改不生效    | 执行 `docker compose up -d`，并检查 `vw-data/config.json` 是否覆盖设置                    |
| 网页正常，客户端登录失败 | 客户端是否选了自托管、地址是否带 `https://`，以及是否有额外的访问认证拦截 API                                |

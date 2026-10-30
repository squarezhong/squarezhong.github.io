---
title: "Docker: 虚拟世界的集装箱船"
date: 2026-10-26
tags:
  - Docker
  - NAS
  - Server
author: Square Zhong
description: 折腾
---

## 引言

Docker 依然是个人用户最佳的容器管理方案。

对于绝大多数问题，[RTFM](https://docs.docker.com/)，或者让你的 agent 代劳。

## 安装

安装 Docker Engine + Docker Compose 即可，不建议使用 Docker Desktop。Windows 用户请直接在 WSL2 中安装 Docker（虽然 Windows 版 Docker Desktop 默认使用基于 WSL 2 的引擎，但没要多此一举，徒增不稳定性）

- [官方文档](https://docs.docker.com/engine/install/ubuntu/)
- [阿里云文档](https://help.aliyun.com/zh/ecs/use-cases/install-and-use-docker-on-a-linux-ecs-instance#33f11a5f1800n)

- 一键脚本

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh --dry-run
```

## 代理与镜像

### 代理

#### 为 Docker Daemon 配置代理

`daemon.json` 默认位置 `/etc/docker/daemon.json`

参考 https://docs.docker.com/engine/daemon/proxy/#daemon-configuration

主要对**镜像拉取**生效。

#### 为 Docker CLI 配置代理

参考 https://docs.docker.com/engine/cli/proxy/

Docker CLI 代理，实际上并不是给 `docker` 命令本身设置代理，而是通过 CLI 配置向容器传递代理环境变量，比如 Dockerfile 中的 `RUN pip install` 就会走 Docker CLI 代理。

### 添加可用镜像站

大部分 Docker Hub 镜像站已经失效 / 不够稳定 / 仓库不够新，直接用代理，或者自己搭一个 Docker Hub 镜像。

## Docker CLI

[官方 cheatsheet](https://docs.docker.com/get-started/docker_cheatsheet.pdf) 比我的嘟囔有用。

建议用 Docker Compose 创建和管理容器，可维护性更好。

### 权限

如果当前用户不在docker的用户组中（大概率不在）：

- 对于个人 NAS、homelab 和 workstation，可以考虑将当前用户加入 docker 组

```shell
sudo usermod -aG docker "$USER"
newgrp docker
```

运行 `docker ps` 验证当前用户是否已拥有足够权限

- 对于多用户服务器，建议在每一句命令前加上 `sudo`.

### 拉取镜像

```shell
docker pull <image-name>
```

创建并运行容器，参考 https://docs.docker.com/reference/cli/docker/container/run/

```shell
docker run --name <container_name> <image_name>
```

常用选项：

- `--name <container name>` 为容器命名
- `--restart always|unless-stopped|no ` 重启方式
- `-v Source (Host):Destination (Container)` 文件（夹）映射
- `-p <host_port>"<container_port>` 端口映射

### 停止、删除与查看日志

```shell
docker stop <container_name>
docker rm <container_name>
docker logs <container_name>
```

### 查看所有运行中的容器

```shell
docker ps
```

### 在容器内执行命令

```shell
docker exec CONTAINER_NAME [command]
```

### 保存镜像

https://docs.docker.com/reference/cli/docker/image/save/#description

```shell
docker save <image_name> > xxxx.tar
```

### 导出容器

https://docs.docker.com/reference/cli/docker/container/export/

```shell
docker export <container_name> > xxxx.tar
```

### Docker Volume

```shell
docker volume ls
docker volume create <volume_name>
docker volume rm <volume_name>
```

#### 导出 volume

```shell
docker run --rm -v your_volume_name:/data -v $(pwd):/backup alpine \
  tar cvf /backup/backup.tar -C /data .
```

#### 导入 volume

```shell
docker run --rm -v your_volume_name:/data -v $(pwd):/backup alpine \
  sh -c "rm -rf /data/* && tar xvf /backup/backup.tar -C /data"
```

## Build

[Dockerfile Overview](https://docs.docker.com/build/concepts/dockerfile/)

鸽了。

## Docker Compose

*人生苦短，我用 Docker Compose。*

你只需要一个 `docker-compose.yml`，这里有[一些不错的示例](https://github.com/docker/awesome-compose)

### 启动服务

```shell
docker compose up -d
```

- `-d` 表示后台运行（detach）

### 停止服务

```
docker compose down
```

## 容器

~~**暴论环节**：从实用性角度出发，对于大部分个人用户的生活场景而言，NAS 也好，homelab 也罢，都是“伪”需求（以折腾本身为乐趣的除外）。相比自部署的服务，商业公司的产品通常更加便捷、稳定，算上时间成本甚至更便宜。开发和企业级场景另当别论，但那是 devops 干的事了。~~

以下是我长期使用后最终留下的几个容器。

- 为了迁移方便，建议使用确定的版本 tag 而不是 `latest`，以免迁移时遇到各种奇妙的兼容性问题。
- 不建议用群晖 NAS 做家用 Docker 服务器，这家公司目前沉迷企业级，家庭用户的优先级比较低。

### Caddy

用于替代 Nginx Proxy Manager 进行 https 反向代理，Caddy 使用配置文件进行配置的方式更符合我的喜好。

我的使用场景是在校园网范围内实现通过域名（不用添加端口）以 https 的方式访问本地部署的服务。

在同一文件夹中（比如 `caddy-lan`）新建三个文件：`docker-compose.yml`，`.env` 和 `caddy/Caddyfile`

```yaml
# docker-compose.yml

name: caddy-lan

services:
  caddy:
    image: ghcr.io/caddybuilds/caddy-cloudflare:2.11.7-alpine
    restart: unless-stopped
    ports:
      # 必须是分配给 Docker 宿主机的局域网 IPv4 地址。
      - "${LAN_IP:?Set LAN_IP in .env}:80:80/tcp"
      - "${LAN_IP:?Set LAN_IP in .env}:443:443/tcp"
      - "${LAN_IP:?Set LAN_IP in .env}:443:443/udp"
    environment:
      CF_API_TOKEN: "${CF_API_TOKEN:?Set CF_API_TOKEN in .env}"
      LAN_CIDRS: "${LAN_CIDRS:?Set LAN_CIDRS in .env}"
    volumes:
      - ./caddy:/etc/caddy:ro
      - ./data:/data
      - ./config:/config
    logging:
      driver: json-file
      options:
        max-size: "10m"
        max-file: "3"
```

在 `.env` 中设置 Docker 宿主机 IP，局域网网段以及 [Cloudflare API (user) Token](https://developers.cloudflare.com/fundamentals/api/get-started/create-token/)。Cloudflare 中域名解析指向：

- 若宿主机直连校园网，则直接指向宿主机在校园网中的 ipv4 地址；
- 若路由器连接校园网，宿主机连接路由器，则域名解析指向路由器在校园网中的 ipv4 地址，然后在路由器设置中将 80 和 443 端口转发给宿主机。

我的网络拓扑是后面这一种情况。

```shell
# .env

# 运行 Caddy 的 Docker 宿主机所持有的局域网 IPv4 地址。
LAN_IP=192.168.31.250

# 允许本地局域网以及路由器直连的上行子网访问。
# 请自行修改为符合需求的网段，我这里是允许小米路由器以及整个大的校园网下的 IP 访问
# 如有其他经路由的客户端网段，按需添加。多个 CIDR 之间用空格分隔。
LAN_CIDRS="192.168.31.0/24 10.0.0.0/8"

# Cloudflare API Token：Zone / Zone / Read + Zone / DNS / Edit。
# 将 Zone Resources 限制为自己的域名。不要使用 Global API Key。
CF_API_TOKEN=
```

最后在 `caddy/Caddyfile` 中设置需要被反向代理的服务的局域网地址和对应域名（将 server1、server2 等修改为你实际的二级域名）

```caddyfile
# caddy/Caddyfile

{
	acme_ca https://acme-v02.api.letsencrypt.org/directory
}

*.artinx.tech {
	tls {
		dns cloudflare {env.CF_API_TOKEN}
	}

	log

	# 不是局域网的访问直接排除
	@outside not remote_ip {$LAN_CIDRS} 127.0.0.1/32 ::1/128
	@server1 host server1.example.com
	@server2 host server2.example.com

	route {
		respond @outside "LAN access only" 403
		reverse_proxy @server1 192.168.xxx.xxx:xxxx
		reverse_proxy @server2 192.168.xxx.xxxx:xxxx
		respond "Not Found" 404
	}
}
```

### VaultWarden

参考  [部署 Vaultwarden + Cloudflare Tunnel 实现跨平台密码管理](https://squarezhong.github.io/posts/2026-10-24-vaultwarden-cloudflare-tunnel/)

### Qbitorrent

最强大的 BT 下载与管理工具，没有之一。

我所使用的组合：

**Qbitorrent WebUI + Client-Blocker**

- Qbittorrent WebUI 为本体
- 不使用任何美化前端，我是原教旨主义者
- Client-Blocker 是一个拦截器，可以拦截迅雷等吸血客户端。在 PCDN 遭整治后疯狂恶意下载 BT 种子的今天尤为重要。

```yaml
services:
  qbittorrent:
    image: lscr.io/linuxserver/qbittorrent:5.2.4
    container_name: qbittorrent_5.2.4
    # network_mode: "host"
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - WEBUI_PORT=21088
      - TORRENTING_PORT=16881
    volumes:
      - ./config:/config
      # 修改为你实际的下载路径
      - /mnt/Videos/Contents:/downloads
    ports:
      - 21088:21088
      - 16881:16881
      - 16881:16881/udp
    restart: unless-stopped
    
  blocker:
    image: simpletracker/qbittorrent-clientblocker:3.7
    container_name: qbit-blocker_3.7
    restart: unless-stopped
    volumes:
      - ./blocker/config.json:/app/config.json
```

Blocker 所用的 `config.json` 参考 [Simple-Tracker/qBittorrent-ClientBlocker-Docker 版本使用](https://github.com/Simple-Tracker/qBittorrent-ClientBlocker#docker-%E7%89%88%E6%9C%AC%E4%BD%BF%E7%94%A8)

### Portainer

Portainer、Grafana 这类工具在我看来是给运维团队准备的，homelab 其实没必要折腾这些。

#### Agent 安装

```bash
services:
  portainer:
    image: portainer/portainer-ce:2.45.3
    container_name: portainer
    restart: always
    ports:
      - "8000:8000"
      - "9443:9443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data

volumes:
  portainer_data:

```

#### 添加 Docker Standalone 环境

https://docs.portainer.io/admin/environments/add/docker

### 性能监控方案

Node Exporter + Prometheus + Grafana

#### Node Exporter + Prometheus

[Monitoring a Linux host with Prometheus, Node Exporter, and Docker Compose](https://grafana.com/docs/grafana-cloud/send-data/metrics/metrics-prometheus/prometheus-config-examples/docker-compose-linux/?utm_source=chatgpt.com#monitoring-a-linux-host-with-prometheus-node-exporter-and-docker-compose)

#### Grafana

用 [Grafana Cloud](https://grafana.com/) 省点事。

面板推荐：[1860](https://grafana.com/grafana/dashboards/1860-node-exporter-full)，8919。

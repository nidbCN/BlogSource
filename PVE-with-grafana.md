---
title: 使用 Grafana 监控你的 PromoxVE 集群
author: Gaein nidb
tags: 
  - Linux
  - 运维
categories: [服务器运维]
date: 2022-10-03 19:11:23
---

原来的虚拟机是一台实验用的设备，因为过度耦合，所以准备迁移到一台新开的专门提供服务的虚拟机，系统为 `Ubuntu server 22.04 LTS`。

本博客实际上是由旧的服务器向新的服务器迁移并且重新部署的过程中写的，因此 InfluxDB 没有使用最新的 `2.4` 版本而是使用了 `1.8.10`。

## InfluxDB

### 安装 InfluxDB

参考 [influxdata | 清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/influxdata/)

TUNA 这里并没有适用于 Ubuntu 22.04 LTS 的选项，不过问题不大。

向 `/etc/apt/sources.list.d/influxdb.list` 写入：

```conf
deb https://mirrors.tuna.tsinghua.edu.cn/influxdata/ubuntu/ jammy stable
```

添加公钥：

```sh
wget -qO https://repos.influxdata.com/influxdb.key | sudo tee /etc/apt/trusted.gpg.d/influxdb.asc
```

> 为什么不用 `apt-key` ？因为已经废弃辣，详见 `man apt-key`

之后使用 `apt install influxdb` 安装即可。

### 配置 InfluxDB

主要目的就是两个：开放 `udp` 和 `http` 连接。

Promox VE 使用 udp 连接，Grafana 使用 http。

修改 `/etc/influxdb/influxdb.conf`

`[http]` 部分修改如下：

```conf
[http]
  # Determines whether HTTP endpoint is enabled.
  enabled = true

  # Determines whether the Flux query endpoint is enabled.
  # flux-enabled = false

  # Determines whether the Flux query logging is enabled.
  # flux-log-enabled = false

  # The bind address used by the HTTP service.
  bind-address = ":8086"
```

取消 `enabled` 和 `bind-address` 的注释即可。

`udp` 部分修改如下

```conf
[[udp]]
  enabled = true
  bind-address = ":8089"
  database = "proxmox"
  # retention-policy = ""
```

也是取消两个注释，然后将 `database` 节点设置为 `proxmox` 。

还需要创建用户，因为我是从以前的迁移过来的，所以不需要再创建。

### 启用 InfluxDB

使用命令

```sh
systemctl start influxdb
```

即可。

如果报错类似于以下的

```log
run: open server: open tsdb store: mkdir /var/lib/influxdb/data/_internal/_series: permission denied
```

可能是因为使用过 `root` 用户运行 `influxd`。

执行

```sh
sudo chmod -R influxdb:influxdb /var/lib/influxdb/*
```

即可。

## Proxmox VE

点击 `DataCenter`，再点击 `Metric Server`。

依次点击 `Add`，`InfluxDB`，在 `server` 处指定提供 InfluxDB 的虚拟机的 IP。

## Grafana

### 安装 Grafana

依旧是参考 [grafana | 清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/grafana/) 。

首先使用

```sh
wget -qO- https://packages.grafana.com/gpg.key | sudo tee /etc/apt/trusted.gpg.d/grafana.asc
```

将

```conf
deb https://mirrors.tuna.tsinghua.edu.cn/grafana/apt/ stable main
```

写入 `/etc/apt/sources.list.d/grafana.list`。

然后执行：

```sh
sudo apt update
sudo apt install grafana
```

### 配置 Grafana

大部分配置都是从原服务器上恢复（因为实在不想重新设置面板了）

* /var/lib/grafana/grafana.db         # 数据库文件
* /etc/grafana/grafana.ini            # grafana配置文件
* /var/lib/grafana/plugins            # 插件目录

#### 设置匿名访问

编辑配置文件，找到

```ini
[auth.anonymous]
# enable anonymous access
enabled = true
```

取消注释并改为 `true`。

## Nginx

grafana 默认监听在 3000 端口，这样用起来非常的 low，这里使用 nginx 把 `localhost:3000` 反向代理到 `http://grafana-server/monitor/` 。

修改配置文件 `/etc/nginx/sites-enabled/default` ，在 `server` 块内添加：

```conf
# Grafana
location /monitor/ {
  proxy_set_header Host $host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_pass http://localhost:3000;
}
```

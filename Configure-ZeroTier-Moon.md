---
title: 配置 ZeroTier 私人节点
author: Gaein nidb
categories:
  - 电脑软件
tags:
  - 软件教程
  - 网络
date: 2021-10-7 17:07:35
---


## 准备工作

一台有 **静态公网 IP** 的服务器，装有 Ubuntu 20.04 LTS 以及 ZeroTier-one 。

> Ubuntu 安装 ZeroTier-one 详见 [Download - ZeroTier](https://www.zerotier.com/download/#downloadLinux)

## 创建网络

在 [ZeroTier Central](https://my.zerotier.com/network) 中创建一个网路并设置为 private。

## 配置服务器

> 参考 [Private Root Servers | ZeroTier Documentation](https://docs.zerotier.com/zerotier/moons)

### 创建 world 定义

转到目录 `/var/lib/zerotier-one`

```sh
cd /var/lib/zerotier-one
```

生成 `moon.json`

```sh
zerotier-idtool initmoon identity.public >>moon.json
```

**此文件包含机密信息，请妥善保管**

修改 `moon.json`

```sh
vim moon.json
```

在 `roots` 中的第一个子节点的 `stableEndpoints` 节点中填入 `<你的服务器IP>/<ZeroTier端口>` 如下：

```sh
"stableEndpoints": [ "1.2.3.4/9993" ]
```

### 签发 moon

签发 `000000deadbeef00.moon`

```sh
zerotier-idtool genmoon moon.json
```

> 此文件不包含机密信息，但是它从包含有机密信息的 `moon.json` 签发而来。

### 启用端口

开启 Ubuntu 防火墙的 `9993` 端口：

```sh
ufw allow 9993
```

服务商控制面板如有防火墙请放行 `9993` 端口。

## 配置客户端

转到 `ZeroTier` 目录

* Windows: `C:\ProgramData\ZeroTier\One`
* Macintosh: `/Library/Application Support/ZeroTier/One`
* Linux: `/var/lib/zerotier-one`
* FreeBSD/OpenBSD: `/var/db/zerotier-one`

在 `ZeroTier` 目录下创建文件夹 `moons.d`

将服务端生成的 `.moon` 文件复制到 `moons.d` 文件夹中并重启 ZeroTier
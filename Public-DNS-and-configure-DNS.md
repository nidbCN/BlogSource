---
title: 常见公共DNS服务&Linux和Windows下修改DNS的方法
author: Gaein nidb
categories:
  - 软件教程
tags:
  - DNS
  - Windows
date: 2021-6-12 14:57:12
---

因为校园网 DNS 污染，导致我无法连接 `githubusercontent.com` ，所以准备更换一个公共 DNS。以下为常见的一些公共 DNS 服务。

DNS 全称是 Domain Name System ，可以理解为负责把域名变成 IP 的一个玩意。

> 注：所有 IPv6 地址均无阿里云测试，因为我服务器没有 v6。目前只有这两三种网络测试，选择 DNS 时候还是自己 ping 一下看看。

## 常见公共 DNS 服务

### 阿里 DNS

网址: https://alidns.com/

#### IPv6

| 主 DNS       | 辅 DNS            | 太原电信 | 唐山电信 |
| ------------ | ----------------- | -------- | -------- |
| 2400:3200::1 | 2400:3200:baba::1 | 10ms     | 19ms     |

#### IPv4

| 主 DNS    | 辅 DNS    | 太原电信 | 唐山电信 | 北京阿里云 |
| --------- | --------- | -------- | -------- | ---------- |
| 223.5.5.5 | 223.6.6.6 | 15ms     | 20ms     | 4ms        |

### 百度 DNS

网址: https://dudns.baidu.com/

| 主 DNS       | 辅 DNS | 太原电信 | 唐山电信 | 北京阿里云 |
| ------------ | ------ | -------- | -------- | ---------- |
| 180.76.76.76 | 无     | 13ms     | 18ms     | 5ms        |

### DNSPOD（腾讯）

网址: https://www.dnspod.cn/products/public.dns

#### 免费版

| 主 DNS       | 辅 DNS | 太原电信 | 唐山电信 | 北京阿里云 |
| ------------ | ------ | -------- | -------- | ---------- |
| 119.29.29.29 | 无     | 12ms     | 21ms     | 7ms        |

#### 专业版

| 主 DNS   | 辅 DNS   | 太原电信 | 北京阿里云 |
| -------- | -------- | -------- | ---------- |
| 见控制台 | 见控制台 | 20ms     | 未测试     |

### 114DNS

网址: https://www.114dns.com/

#### 无劫持

| 主 DNS          | 辅 DNS          | 太原电信 | 唐山电信 | 北京阿里云 |
| --------------- | --------------- | -------- | -------- | ---------- |
| 114.114.114.114 | 114.114.115.115 | 33ms     | 34ms     | 29ms       |

#### 拦截 钓鱼病毒木马网站

| 主 DNS          | 辅 DNS          | 太原电信 | 唐山电信 | 北京阿里云 |
| --------------- | --------------- | -------- | -------- | ---------- |
| 114.114.114.119 | 114.114.115.119 | 34ms     | 34ms     | 29ms       |

#### 拦截 色情网站

| 主 DNS          | 辅 DNS          | 太原电信 | 唐山电信 | 北京阿里云 |
| --------------- | --------------- | -------- | -------- | ---------- |
| 114.114.114.110 | 114.114.115.110 | 47ms     | 36ms     | 31ms       |

### Google Public DNS

网址: https://dns.google.com/

#### IPv6

| 主 DNS               | 辅 DNS               | 太原电信 | 唐山电信 |
| -------------------- | -------------------- | -------- | -------- |
| 2001:4860:4860::8888 | 2001:4860:4860::8844 | 43ms     | 253ms    |

#### IPv4

| 主 DNS  | 辅 DNS  | 太原电信 | 唐山电信 | 北京阿里云 |
| ------- | ------- | -------- | -------- | ---------- |
| 8.8.8.8 | 8.8.4.4 | 58ms     | 49ms     | 54ms       |

### 1.1.1.1

网址: https://1.1.1.1/dns/

#### IPv6

| 主 DNS               | 辅 DNS               | 太原电信 | 唐山电信 |
| -------------------- | -------------------- | -------- | -------- |
| 2606:4700:4700::1111 | 2606:4700:4700::1001 | 79ms     | 202ms    |

#### IPv4

| 主 DNS  | 辅 DNS  | 太原电信    | 唐山电信 | 北京阿里云 |
| ------- | ------- | ----------- | -------- | ---------- |
| 1.1.1.1 | 1.0.0.1 | 188ms（辅） | 165ms    | 165ms      |

## 配置 DNS

### Windows 10

1. 点击右下角网络图标，然后点击"Network & Internat settings"；

![图标](https://img.cdn.gaein.cn/website_used/blog/Public-DNS-and-configure-DNS/01.webp)

2. 点击正在使用的网络（比如我的是"Ethernet"，如果你在使用 WiFi 这里应该会显示"Wi-Fi"），点击"Properties"；

![设置页面](https://img.cdn.gaein.cn/website_used/blog/Public-DNS-and-configure-DNS/02.webp)

在 "IP Settings" 处点击 "Edit"，依次输入 "Preferred DNS" 和 "Alternate DNS"，如果有 IPv6 那么在下面的 "IPv6" 栏的对应位置输入 IPv6 DNS。

![输入DNS](https://img.cdn.gaein.cn/website_used/blog/Public-DNS-and-configure-DNS/03.webp)

### Ubuntu

我手里只有 Ubuntu，其它 Linux 如何配置未知。貌似更改 `/etc/resolv.conf` 是通用方法。

由于我们使用的是 Ubuntu，Ubuntu 的 DNS 是由 systemd-resolve 来管理的，因此我们不需要手动更新 `/etc/resolv.conf` （事实上更新了也会被覆盖）。

关于错误示范请见我在 18 年干的傻事：

[Ubuntu 下 apt“无法解析域名”解决方案](https://blog.gaein.cn/passages/Fix-Ubuntu-DNS-Error/)

1. 确定使用的是 systemd 管理的解析服务：使用命令 `systemd-resolve --status` 查看服务状态；
2. 编辑 DNS 文件：`/etc/systemd/resolved.conf`  
   `DNS` 为 DNS 地址，可以配置多个 DNS，用空格隔开。  
   `FallbackDNS` 为备用 DNS，同理。
   比如：
   ```
   [Resolve]
   DNS=223.5.5.5 119.29.29.29 180.76.76.76
   FallbackDNS=223.6.6.6
   ```

另请参阅：[Use DNS over TLS - Fedora Magazine](https://fedoramagazine.org/use-dns-over-tls/)

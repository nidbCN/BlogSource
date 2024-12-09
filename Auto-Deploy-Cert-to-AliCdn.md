---
title: '将Certbot/ACME.sh自动化申请的证书自动部署到阿里云CDN'
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - CSharp
  - dotNET
  - 开源工具
image: "https://img.cdn.gaein.cn/website_used/blog/Auto-Deploy-Cert-to-AliCdn/banner.webp"
date: 2024-07-02 19:54:27
lastmod: 2024-08-30 11:32:27
---

## 功能

定期检查阿里云CDN所使用的SSL证书是否临近到期，如果临近到期则使用本地的签发的证书进行更新。

证书的签发仍由 certbot 或 acme.sh 等工具完成，这里只涉及到将本地的证书文件部署到 阿里云CDN。

项目地址：[nidbCN/AliCdnSSLWorker: 自动读取证书文件并上传到阿里云CDN](https://github.com/nidbCN/AliCdnSSLWorker)

## 使用场景

没错，阿里云ECS、阿里云DNS、阿里云CDN，但是问题来了——签不起阿里云的证书了。

之前阿里云的免费证书随便签，再后来改成一年20个，这都没啥，毕竟在阿里云的控制台里面手动签一个也没啥麻烦，但是从今年开始，证书的有效期变成了仨月，加之这么多证书签发的时间也不尽相同，这就让我不得不经常性的去签发、更新证书。

在懒得换证书导致网站宕了一阵时间后，准备把证书换到 Let's Encrypt 签发的 SSL 证书了。签发之类的自然有脚本，也可以用阿里云DNS进行验证。我使用的是 [Nginx Proxy Manager](https://nginxproxymanager.com/) 里面内建的 ACME，由于我不想破坏这个程序的环境，不愿意再里面再去植入签发完成后去调用我的程序上传等，因此我决定使用挂载证书目录，定期检查实际部署证书的有效期实际再扫描内部证书文件并上传的方法。

当然，也预留了 HTTP 接口，可以在证书签发完成后通过 curl 等命令调用去更新所有受管理的证书。

## 使用方法

### 安装

#### 使用二进制

Github 上没有提供预编译的二进制 ~~因为我懒~~ ~~有空传好吧~~ 因此需要使用 .NET SDK8 进行编译。

进入项目目录后使用

```
dotnet publish -c Release
```

即可编译并创建发布，当然如果临时调试等使用可以使用 `dotnet run` 运行。

#### 使用 docker

使用 `docker build -t <tag> .` 即可编译容器。

或使用 `docker pull registry.cn-beijing.aliyuncs.com/nidb-cr/alicdn-ssl-worker:git` 拉取编译好的镜像。

按照 “配置” 章节的内容配置好选项。

拉取完成后使用 `docker run` 运行即可。参数自行编写，如果不会推荐使用下面的 docker compose。

#### 使用 docker compose

以下为示例 docker compose 文件，将其内容写入到 `docker-compose.yaml`。

```yaml
volumes:
  ssl_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: '/data/example/letsencrypt'

configs:
  alicdn-ssl.conf:
    file: './alicdn-ssl/appsettings.json'

services:
  cdn-ssl:
    image: 'registry.cn-beijing.aliyuncs.com/nidb-cr/alicdn-ssl-worker:git'
    environment:
      TZ: 'Asia/Shanghai'
    ports:
      - 5057:5057
    depends_on:
      - nginx
    restart: unless-stopped
    volumes:
      - ssl_data:/data/ssl
    configs:
      - source: alicdn-ssl.conf
        target: '/app/appsettings.json'
```

需要修改的地方为 `volumes.ssl_data.driver_opts.device` ，将 `/data/example/letsencrypt` 改为存放你 acme.sh 签发证书的目录，该目录下通常有 `keys` 和 `lives` 等子目录。

按照 “配置” 章节的内容配置好选项。

使用 `docker compose up -d .` 启动服务。

### 配置

配置使用标准的 .NET IConfiguration 框架，配置文件由命令行参数、环境变量、.NET secret以及配置文件等提供，详细内容可见微软文档：[配置 - .NET | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/core/extensions/configuration)。

推荐使用命令行参数、环境变量和配置文件 `appsettings.json`。其中命令行参数比较多用于临时调试等。多种方式可以组合使用且配置节点名称（即 key-value 中的 key）一致。当某个参数被重复配置时，优先级为 “命令行参数 > 环境变量 > 配置文件”。

以下为配置文件 `appsettings.json` 的样例和说明：

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },  // Logging 参考 https://learn.microsoft.com/zh-cn/dotnet/core/extensions/logging?tabs=command-line#configure-logging-without-code
  "CertConfig": {
    "CertFileName": "fullchain.pem",      // 证书公钥文件
    "PrivateKeyFileName": "privkey.pem",  // 证书私钥文件
    "CertSerchPath": "/data/ssl/live",  // 证书文件的目录（挂载在docker里面的目录），程序会扫描该目录所有子目录中的文件，查找在以上两个选项中配置的证书文件名
    "IntervalHour": "12",               // 每次检查的间隔，每隔{IntervalHour}执行一次检查，若当前HTTPS证书在{IntervalHour}内过期则会更新（小时）
    "CacheTimeoutMin": "30",            // 文件缓存间隔，若上次读取证书文件超过{CacheTimeoutMin}则重新读取（分钟）
    "DomainList": [                     // 交给程序管理的域名列表
      "blog.gaein.cn",
      "img.cdn.gaein.cn",
      "static.cdn.gaein.cn"
    ]
  },
  "AliCdnConfig": {
    "AccessKeyId": "LTA*********************",            // 阿里云 AK ID
    "AccessKeySecret": "*****************************",   // 阿里云 AK Secret
    "Endpoint": "cdn.aliyuncs.com"
  },
  "ApiConfig": {
    "IpAddress": "0.0.0.0",   // HTTP API 监听地址
    "Port": 5057              // HTTP API 监听端口
  }
}
```

以上所有配置节点均可通过命令行和环境变量复写，如不改变配置文件将 HTTP 端口更改为 `8001`：

* 环境变量：`export ApiConfig_Port=8001` (bash)
* 命令行参数：`./AliCdnSSLWorker --ApiConfig:Port 8001` 或 `ApiConfig:Port=8001`、 `/ApiConfig:Port 8001`。使用 `dotnet run` 启动同样也可以使用命令行参数。

详见：[配置提供程序 - .NET | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/core/extensions/configuration-providers)

### 使用

其实不用干什么哒！让它跑着就行了。

#### Web API

为了方便控制，该项目启动了一个 HTTP 服务器并暴露了 WebAPI

##### 强制上传所有证书

```
HTTP GET <IpAddress>:<Port>/force_refresh/
```

你可以使用你喜欢的 HTTP 客户端发送请求，如：

```bash
curl http://<ipaddress>:<port>/force_refresh/
```

或使用命令行参数 `--refresh` 或 `-r` 启动二进制发送更新指令，如：

```bash
sudo docker exec alicdn-ssl dotnet AliCdnSSLWorker.dll -r
```

该命令会从之前的配置中读取 HTTP API 监听的 IP 地址和端口，并向该端口发送请求。

如刷新成功你将会获得一个 `HTTP 200 OK` 的响应。


export Ali_Key="<key>"
export Ali_Secret="<secret>"

./acme.sh --issue --dns dns_ali -d gaein.cn -d www.gaein.cn -d v4-cn-bjs3.gaein.cn -d api.gaein.cn -d img.cdn.gaein.cn -d static.cdn.gaein.cn

./acme.sh
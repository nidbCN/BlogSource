title: '将ASP.NET Core应用程序部署到docker中'
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - CSharp
  - dotNET
  - AspNetCore
  - 编程
  - 后端
  - docker
date: 2021-4-4 12:10:02

---

# 将ASP.NET Core应用程序部署到docker中

## 前言

> 最近用ASP.NET Core随便摸了一些API和新博客的后端，为了方便以后迁移和管理准备部署在docker中。

## 编译

由于使用docker简介相当于已经指定了系统（Linux），因此我们可以使用更多编译选项。

```bash
dotnet publish -c Release -p:PublishReadyToRun=true --no-self-contained -r ubuntu.20.04-x64 -o ./Release
```

#### 发布模式

使用`--configuration`或者`-c`来指定发布的模式。默认为`Debug`，由于我们需要发布正式版所以采用`Release`。

#### R2R

ReadyToRun：在编译的时候除了编译中间码外也编译目标架构的机器码，但是仍然会保留中间码。这样会造成程序体积的翻倍，但是会加快启动速度，不过由于程序体积变大也可能减缓启动速度，~~是个很玄学的选项~~
不过微软文档上表示。

> For instance, ReadyToRun can be used to reduce the response latency of the first use of Web API in an ASP.NET application.

> 比方说呢，R2R可以加快ASP.NET Core Web API的第一次响应速度。

更多内容见微软文档[Microsoft Docs | ReadyToRun development overview](https://docs.microsoft.com/en-us/dotnet/core/deploying/ready-to-run)

#### 非自部署发布

使用`--no-self-contained`或者`--self-contained false`来指定不要自部署发布，即不包含.NET runtime。这样做有利于减少发布程序的大小。

但是在没有.NET runtime的机器上运行时需要先安装.NET runtime。

不过我的想法是在docker里面运行，可以使用ASP.NET Core的镜像，包含了.NET runtime。所以不需要自部署发布。

#### 目标平台

使用`--runtime <RUNTIME_IDENTIFIER>`或`-r`来指定目标平台。后面接目标平台标识符，即Runtime Identifiers (RIDs)。

RID列表见微软文档：[Microsoft Docs | Runtime Identifiers (RIDs)](https://docs.microsoft.com/en-us/dotnet/core/rid-catalog)

因为启用了R2R所以需要指定目标平台。

另外**我觉得**（并没有根据的胡扯）指定发布平台比全平台编译效果好。

#### 输出目录

使用`--output <OUTPUT_DIRECTORY>`或者`-o`设置输出目录。

#### 更多

> 更多选项见[Microsoft Docs | dotnet CLI](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-publish)

## DockerFile

> Dockerfile 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。

#### 引用ASP.NET Core镜像

首先我不希望发布自部署应用程序，因为它过于庞大。所以我们可以考虑引用ASP.NET Core Runtime的Docker镜像：

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0
```
这句话引用了ASP.NET Core的docker镜像，里面有包含Runtime。

##### 添加镜像加速

docker官方的镜像仓库慢的理解不能，这边我使用了阿里云的镜像加速服务（因为我是阿里云服务器，这样速度也非常可观）

1. 打开阿里云控制台的容器镜像服务中的[镜像加速器](https://cr.console.aliyun.com/cn-beijing/instances/mirrors)Tag；
2. 按照下面的操作文档修改，比如Ubuntu为：
   1. 进入目录`/etc/docker`（如果没有就创建一个）
   2. 编辑文件`daemon.json`（如果没有就创建一个）
   3. 写入如下内容：`{"registry-mirrors": ["https://<token>.mirror.aliyuncs.com"]}`，网址在上面的“加速器地址”处有写。
   4. `sudo systemctl daemon-reload`重载
   5. `sudo systemctl restart docker`重启docker

#### 设置目录

首先需要使用`COPY`命令复制我们编译出来的文件，然后使用`WORKDIR`来指定工作目录（就是启动程序的路径）。

```dockerfile
## Copy Files
COPY . /public

## Set Workdir
WORKDIR /public
```
> 请注意替换成自己编译输出的目录

#### 设置时区

为了确保时间正确，设置一下时区

```dockerfile
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
RUN echo "Asia/Shanghai" >/etc/timezone
```

#### 开放端口

```dockerfile
## Expose Port 80
EXPOSE 80
```

由于docker中是生产环境，所以会使用80端口而不是默认的5000。可以在执行程序的时候更改端口。总之，这里写你要暴露的端口咯。

#### 运行程序

```dockerfile
CMD ./WebApplication
```

> 注意替换为你的程序名

使用参数`--urls "http://*:6000"`来设置启动的端口（127.0.0.1为监听本机）。可以使用类似于`--urls "http://*:6000;http://*:6001;http://*:6002;http://*:6003"`监听多个端口。

或者你可以使用环境变量和其它方法，可以参考这篇文章：[5 ways to set the URLs for an ASP.NET Core app](https://andrewlock.net/5-ways-to-set-the-urls-for-an-aspnetcore-app/)

## 创建容器

```bash
docker build -t MyWebApplication:v1.4.2 Release/
```

这句话将会创建一个docker容器，命令的格式如下

```bash
docker build -t <container name>:<tag> <directory>
```

注意替换为自己容器名称和标签以及构建出来的目录

## 运行容器

```bash
docker run -d -p 6000:80 MyWebApplication:v1.4.2
```

其中`-p 6000:80`的意思是讲容器暴露的80端口映射到本机的6000端口，而后面的`MyWebApplication:v1.4.2`则是刚才创建容器时候写的`container name`和`tag`。
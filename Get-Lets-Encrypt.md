---
title: 使用DNSPOD API申请Let's Encrypt通配符SSL证书
author: wr410
categories:
- 网站建设
tags:
- SSL证书
- https
date: 2019-01-19 01:44:54
---


从CSDN转载
原文：《Let's Encrypt 申请通配符证书（使用DnsPod解析的域名操作）》
链接：https://blog.csdn.net/wr410/article/details/79559369
作者：wr410
遵守作者提出的“原创文章，转载请注明来源。”要求

DNSPOD API TOKEN快速的申请了一个，当然前提是域名使用DNSPOD解析，签发速度还是非常快的
通配符证书这次只接受ACMEv2协议申请，也就意味着需要通过客户端来申请了。

## 1、部署客户端

### 1A、下载：

```bash
wget https://get.acme.sh
```

默认下载出来的文件名是index.html，自己加参数或者重命名什么的自己看着办吧！

### 1B、执行：

```bash
bash index.html
```

是的，我就是这么懒。

### 1C、安装：

脚本会去github上下载客户端，稍等片刻，会安装到home目录下，中间会有红字告警，可以忽略。

安装完成后会添加cron记录，自己可以通过crontab查看处理。

## 2、准备工作

客户端支持的域名注册商（本文只用DNSPOD演示）

基本原理就是通过DNS域名的API调用进行所有权确权

### 2A、域名服务商-申请API权限

进入控制台的用户中心-安全设置，最底下申请API TOKEN。

填写名称，提交后就会得到ID和TOKEN（注意不要动）敲黑板！敲黑板！敲黑板！

保持姿势，把得到的ID和TOKEN以你最快的速度保存下来，
可以选择复制（←正常人）或者截屏（←高手），一旦关掉就没了。

Get-Lets-Encrypt

![1](https://img.cdn.gaein.cn/website_used/blog/Get-Lets-Encrypt/01.webp)

### 2B、客户端环境变量

``` bash
export DP_Id="id"
export DP_Key="token"
```

我们需要设置两个环境变量，分别是2A中获得的ID和TOKEN

### 3、申请签发

之前安装好客户端之后会更新PATH，所以自己刷新或者重新登录一下服务器吧！

`acme.sh --issue --dns dns_dp -d *.wrui.cf --debug`

脚本说明就自己打help看，如果没有什么特殊需要（例如什么ECC的什么4096的奇葩用户）的话，就按上面这条命令来就好了。

参数说明：
![2](https://img.cdn.gaein.cn/website_used/blog/Get-Lets-Encrypt/02.webp)

--dns 是指定域名服务商，dp就是dnspod，-d是需要签发的域名可以有多个-d参数

--debug参数和多数命令的verbose一样会显示详细日志，当然如果没有需要，不想看着刷屏的话就去掉吧！

![4](https://img.cdn.gaein.cn/website_used/blog/Get-Lets-Encrypt/04.webp)

申请过程中会有120秒的等待DNS时间，稍安勿躁，等待提示Success之后就可以到提示的路径下获得证书啦！

![3](https://img.cdn.gaein.cn/website_used/blog/Get-Lets-Encrypt/03.webp)

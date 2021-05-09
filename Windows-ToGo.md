---
title: '世界那么大，旅行时别忘了带上Windows——windows to go 入坑'
author: Gaein nidb
categories:
  - 电脑软件
  - 电脑硬件
tags:
  - WTG
  - 软件介绍
cover: 'https://s2.ax1x.com/2020/01/25/1eVjMR.png'
date: 2020-01-24 23:38:30
---
Windows to go，说白了就是在U盘里面装Windows，随身带着Windows操作系统和软件。只要在BIOS里面设置了U盘启动就能进入U盘里面的Windows系统。
<!-- more -->
官方说明详见 https://docs.microsoft.com/zh-cn/windows/deployment/planning/windows-to-go-overview （机翻）
## 准备

1. 如果使用Windows自带的Windows to go软件，需使用移动硬盘（插上电脑显示在设备和驱动器里，而不是可移动驱动器）
2. 最好选购USB3.1或者USB3.0接口的移动硬盘或U盘，USB2.0由于速度瓶颈会严重影响使用体验
3. 移动硬盘或U盘的容量在64GB及以上，32G可能无法正常运行（我选择的是256G的SSFD，读写可到2000MB/S）
3. 电脑有键盘，用于修改BIOS
4. Windows系统安装ISO镜像文件（我选择的是Windows10 企业版）
 
## 优缺点

1. 优点：便携性
2. 缺点：对U盘有一定要求，数据不是很安全，容易遭到破坏，而BitLocker又不能兼容IOS系统

## 软件

1. 官方工具
2. 萝卜头出品的WTGA

## 制作

基本上是下一步无脑操作，网上有很多类似帖子

## 开机

开机按DEL进入主板BIOS，在BOOT中将U盘启动调到硬盘或者Windows boot manager前面，保存并退出
如果不希望更改BIOS，可以在启动列表中直接选择U盘 

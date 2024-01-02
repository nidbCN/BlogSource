---
title: 华硕 C5F 改 BIOS 支持 NVMe SSD 启动
author: Gaein nidb
categories:
- 电脑硬件
tags:
- BIOS
date: 2018-12-05 16:01:40
---

## 前言

最近入手了阿斯加特的AN2系列的NVMe SSD，苦于主板不支持NVMe协议，没法用作系统盘。百度无果之后就尝试着自己MOD一个BIOS了
首先在华硕官网找到C5F的BIOS，另外拿了一个970PRO的BIOS（华硕支持NVMe的AM3+主板好像只有970）大上周用了C6H的BIOS好像刷坏了，还专门买了个编程器

网上的模块我不知道为什么没法用（大概是因为他们都是Intel板子的吧<瞎扯淡）于是只好自己从其他支持NVMe板子中提取

## 实践

### 工具准备

#### 文件

![文件目录](https://img.cdn.gaein.cn/website_used/blog/CVF-NVMe-BIOS/01.webp)

首先把C5F的BIOS复制到UBU的目录下，然后运行UBU.BAT，升级一些东西，为NVMe模块挪出地方

#### 升级模块

![控制台](https://img.cdn.gaein.cn/website_used/blog/CVF-NVMe-BIOS/02.webp)

按照提示升级，需要一些英文基础，**注意网卡那边的升级不要选错版本！**
完成后输入0退出，再输入0保存为BIOS.BIN

#### 提取模块

![Fl842R.png](https://img.cdn.gaein.cn/website_used/blog/CVF-NVMe-BIOS/03.webp)

使用MMTool打开970的BIOS文件

> 注意，需要使用MMTool 4打开，MMTool5会出错。
> 
> MMTool在Windows10下会显示不全，所以我开了个Windows7的虚拟机来操作

![打开后](https://img.cdn.gaein.cn/website_used/blog/CVF-NVMe-BIOS/04.webp)

成功读取BIOS，找到NVMe相关的模块（NVMe、NVMeSmm、NVMeINT13）

![模块](https://img.cdn.gaein.cn/website_used/blog/CVF-NVMe-BIOS/05.webp)

选中“NVMe” Extract菜单，点击Extract，提取。文件名为“模块.ffs”

![提取](https://img.cdn.gaein.cn/website_used/blog/CVF-NVMe-BIOS/06.webp)

其他两个也如法炮制

![选中模块](https://img.cdn.gaein.cn/website_used/blog/CVF-NVMe-BIOS/07.webp)

再打开C5F的BIOS，找到CSMCORE，插入这三个模块，注意勾选“Insert Compress”

插入完成后应该在CSMCORE同卷（01）的最后

保存文件

![文件大小](https://img.cdn.gaein.cn/website_used/blog/CVF-NVMe-BIOS/08.webp)

4194304字节大概是3.99MB，BIOS能够盛下

![刷入BIOS](https://img.cdn.gaein.cn/website_used/blog/CVF-NVMe-BIOS/09.webp)

使用AFU刷入BIOS，点击OPEN打开，FLASH写入，期间会有弹窗提示，注意关闭其他软件。

> 刷BIOS有风险，请做好准备，手里有BIOS烧录器的最好。

虽然这样下来主板里面仍然没有我的SSD，但是试了一下总算可以正常使用系统了，算是勉强解决，后续再研究。

## 参考

* [【2017整理】如何在使用AMI UEFI BIOS的老平台上加入NVMe支持](http://tieba.baidu.com/p/5085204352)
* [主板没法从NVMe启动咋办？硬改BIOS！](http://tieba.baidu.com/p/4963703768)
* [老夫卿发少年狂——老主板升级PCI-E NVMe SSD攻略](https://post.smzdm.com/p/555177/)

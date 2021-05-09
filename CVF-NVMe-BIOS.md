---
title: 华硕C5F改BIOS支持nvme SSD启动
author: Gaein nidb
categories:
- 电脑硬件
tags:
- BIOS
cover: 'https://s1.ax1x.com/2018/12/05/Fl8qaD.png'
date: 2018-12-05 16:01:40
---
最近入手了阿斯加特的AN2系列的NVMe SSD，苦于主板不支持nvme协议，没法用作系统盘。百度无果之后就尝试着自己MOD一个BIOS了
<!-- more -->
## 前言

最近入手了阿斯加特的AN2系列的NVMe SSD，苦于主板不支持nvme协议，没法用作系统盘。百度无果之后就尝试着自己MOD一个BIOS了
首先在华硕官网找到C5F的BIOS，另外拿了一个970PRO的BIOS（华硕支持nvme的AM3+主板好像只有970）大上周用了C6H的BIOS好像刷坏了，还专门买了个编程器
网上的模块我不知道为什么没法用（大概是因为他们都是Intel板子的吧<瞎扯淡）于是只好自己从其他支持nvme板子中提取
## 实践
###### 1
![Fl8hG9.png](https://s1.ax1x.com/2018/12/05/Fl8hG9.png)

首先把C5F的BIOS复制到UBU的目录下，然后运行UBU.BAT，升级一些东西，为nvme模块挪出地方
###### 2
![Fl8fPJ.png](https://s1.ax1x.com/2018/12/05/Fl8fPJ.png)

按照提示升级，需要一些英文基础，**注意网卡那边的升级不要选错版本！**
完成后输入0退出，再输入0保存为BIOS.BIN
###### 3
[![Fl842R.png](https://s1.ax1x.com/2018/12/05/Fl842R.png)](https://imgchr.com/i/Fl842R)

使用MMTool打开970的BIOS文件

**注意，需要使用MMTool 4打开，MMTool5会出错，MMTool在Windows10下会显示不全，所以我开了个Windows7的虚拟机来操作**
###### 4
[![Fl85x1.png](https://s1.ax1x.com/2018/12/05/Fl85x1.png)](https://imgchr.com/i/Fl85x1)

成功读取BIOS，找到Nvme相关的模块（Nvme、NvmeSmm、NVMEINT13）
[![Fl8R54.png](https://s1.ax1x.com/2018/12/05/Fl8R54.png)](https://imgchr.com/i/Fl8R54)
###### 5
选中“Nvme” Extract菜单，点击Extract，提取。文件名为“模块.ffs”
[![Fl8bVO.png](https://s1.ax1x.com/2018/12/05/Fl8bVO.png)](https://imgchr.com/i/Fl8bVO)

其他两个也如法炮制
###### 6
[![Fl8oKx.png](https://s1.ax1x.com/2018/12/05/Fl8oKx.png)](https://imgchr.com/i/Fl8oKx)

再打开C5F的BIOS，找到CSMCORE，插入这三个模块，注意勾选“Insert Compress”
[![Fl87qK.png](https://s1.ax1x.com/2018/12/05/Fl87qK.png)](https://imgchr.com/i/Fl87qK)

插入完成后应该在CSMCORE同卷（01）的最后
###### 7
保存文件
![Fl8Tr6.png](https://s1.ax1x.com/2018/12/05/Fl8Tr6.png)

4194304字节大概是3.99MB，BIOS能够盛下
[![Fl8qaD.png](https://s1.ax1x.com/2018/12/05/Fl8qaD.png)](https://imgchr.com/i/Fl8qaD)

使用AFU刷入BIOS，点击OPEN打开，FLASH写入，期间会有弹窗提示，注意关闭其他软件。
## 刷BIOS有风险，请做好准备，手里有BIOS烧录器的最好。
虽然这样下来主板里面仍然没有我的SSD，但是试了一下总算可以正常使用系统了，算是勉强解决，后续再研究。

## 参考
* [【2017整理】如何在使用AMI UEFI BIOS的老平台上加入NVMe支持](http://tieba.baidu.com/p/5085204352)
* [主板没法从NVME启动咋办？硬改BIOS！](http://tieba.baidu.com/p/4963703768)
* [老夫卿发少年狂——老主板升级PCIE NVMe SSD攻略](https://post.smzdm.com/p/555177/)

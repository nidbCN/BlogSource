---
title: 在surface pro上使用WindowsToGo - surface pro启动win to go
author: Gaein nidb
categories:
  - 电脑软件
tags:
  - WTG
  - surface
  - 软件介绍
cover: 'https://s2.ax1x.com/2020/02/05/1rng1A.md.png'
date: 2020-02-02 08:25:08
---
在上一篇博客中我介绍了WindowsToGo，那么如何在surface上面使用WTG呢？我重复设置了很多次也参考了网上设置surface Book的教程，终于找到了完美的解决方案。
<!-- more -->

> 传送门：世界那么大，旅行时别忘了带上Windows——windows to go 入坑  https://blog.gaein.cn/passages/windows-to-go/

## 进入UEFI固件设置（BIOS设置）
方法一：在关机时候进入UEFI固件设置
1. 关闭 Surface，然后等待大约 10 秒钟以确保其处于关闭状态。
2. 按住 Surface 上的调高音量按钮，同时按下并释放电源按钮。
3. 屏幕上会显示 Microsoft 或 Surface 徽标。继续按住调高音量按钮。显示 UEFI 屏幕后，释放此按钮。

方法二：通过 Windows 加载 UEFI 固件设置菜单
1. 选择“开始”菜单>“设置”>“更新和安全”>“恢复”。
2. 在“高级启动”下，选择“立即重启”。
	![设置菜单](https://s2.ax1x.com/2020/02/05/1ruAnx.png)
3. 在“选择一个选项”下，选择“疑难解答”>“高级选项”>“UEFI固件设置”，然后选择“重启”
	
## 操作系统设置
1. 打开“此电脑”观察系统盘图标上面是否有一个小锁的标准如果有，继续，没有则重启进入BIOS。
2. 右键系统盘>“取消或关闭BitLocker”
	
## UEFI固件设置

1. 关闭TPM
2. 更改启动顺序，将usb调到ssd之前
	如图所示（别问我怎么拍照的，我扫描仪扫的）
	> 注意：我使用的是surface pro3，固件设置和后续的pro4、5、6、7图形界面有很大区别，但是选项是相通的嘛

	![UEFI固件设置](https://s2.ax1x.com/2020/02/05/1ruaCQ.jpg)
	
## 插入U盘，重新启动即可进入WTG系统

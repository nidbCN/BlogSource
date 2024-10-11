---
title: 'Powershell Set-Adapter 的工作原理'
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

## 起因

某不愿意透露姓名的薛姓网友想要使用 wmi 更改网卡的 MAC 地址，但是他发现 Set-Adapter 可以做到这一点，于是想丁真一下 Set-Adapter 是怎么实现的。

然后看了一下，现在就忘了，所以不写了
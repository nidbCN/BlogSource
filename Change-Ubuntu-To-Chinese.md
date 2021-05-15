---
title:  Ubuntu终端更改为中文
author: Gaein nidb
tags: 
  - Linux
  - 运维
categories: [服务器运维]
date: 2018-08-17 09:36:49
---

由于本人英文渣，加之一个服务器终端是中文一个服务器终端是英文（当然两个服务器都穷的续不起了）

所以今天更改Ubuntu的语言为中文

因为是服务器所以只能通过终端更改

首先安装下语言包

```sh
sudo apt search language-pack-zh-hans
```
我先使用apt搜索了一下语言包

![搜索结果](https://img.cdn.gaein.cn/website_used/blog/Change-Ubuntu-To-Chinese/01.webp)

使用命令安装他们

```sh
sudo apt install language-pack-zh-hans
sudo apt install language-pack-zh-hans-base
```

安装下对于程序的语言支持

```sh
sudo apt install `check-language-support -l zh`
```

将语言设置为中文

```sh
sudo localectl set-locale.UTF-8
```

重启系统

```sh
sudo reboot
```

随便输入个什么命令，比如 `date`

![显示](https://img.cdn.gaein.cn/website_used/blog/Change-Ubuntu-To-Chinese/02.webp)

显示为中文，修改成功

如果没有修改成功的话，使用文本编辑器编辑文件

```sh
sudo vim  /etc/default/locale
```

![编辑文件](https://img.cdn.gaein.cn/website_used/blog/Change-Ubuntu-To-Chinese/03.webp)

将 `LANG LANGUAGE等` 后面换成

```
zh_CN.UTF-8
```

![编辑文件内容](https://img.cdn.gaein.cn/website_used/blog/Change-Ubuntu-To-Chinese/04.webp)

重启系统后就是中文了

![中文显示](https://img.cdn.gaein.cn/website_used/blog/Change-Ubuntu-To-Chinese/05.webp)


参考：

1. [ubuntu如何在终端下更改系统语言 - Linux大神博客](https://www.linuxdashen.com/ubuntu如何在终端下更改系统语言)
2. [Ubuntu 14.04 英文SSH终端更改为中文显示_雅礼·实验-CSDN博客](https://blog.csdn.net/cnylsy/article/details/52474433)

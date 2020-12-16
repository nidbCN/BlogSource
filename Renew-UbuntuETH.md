title: Ubuntu更改IP后重新配置网卡
author: Gaein nidb
categories:
- 服务器运维
tags:
- Linux
- Ubuntu
cover: 'https://s2.ax1x.com/2019/02/12/kwXZUf.png'
date: 2019-02-12 21:04:35
---
编辑/etc/network/interfaces：
` vim /etc/network/interfaces `
内容如下：

``` bash
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static	//指定为静态
address 43.226.***.**	//服务器IP
netmask 255.255.240.0	//网关
gateway 43.226.156.1	//子网掩码

```

不同的服务器或者不同的IDC配置文件有可能不同，请自行修改
修改完成后使用：
` sudo /etc/init.d/networking restart `
重启网络设备

title: 'CentOS部署MySQL8步骤'
author: Gaein nidb
categories:
  - 服务器运维
tags:
  - 网站建设
  - 数据库
  - MySQL
cover: 'https://s2.ax1x.com/2019/07/15/ZouxhD.png'
date: 2019-07-15 10:52:59
---
早就想去写的一篇博文，因为我在部署现在这个网站的服务器时，使用了最新版的MySQL，按照网上的教程安装出现了不少问题，写在这里备忘。
<!-- more -->
# 获取rpm
1. 访问 [https://www.mysql.com/downloads/](https://www.mysql.com/downloads/)
![MySQL官网的下载界面](https://s2.ax1x.com/2019/07/15/ZoEUeO.md.png)
选择Yum Repository
2. 选择适合于自己系统的版本，比如我的是CentOS7，则选择这个，点击下载（Donwnload）
![下载栏](https://s2.ax1x.com/2019/07/15/ZoEJQx.png)
3. 忽略下载页面的登录提示，右键"No thanks,just start my download."，复制连接
![下载页面](https://s2.ax1x.com/2019/07/15/ZoEYy6.md.png)
4. 连接到服务器，使用wget命令下载rpm
```
wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
```
![wget下载完成](https://s2.ax1x.com/2019/07/15/ZoEtOK.png)
# 安装MySQL
1. 导入rpm，使用命令：
```
sudo rpm -Uvh mysql80-community-release-el7-3.noarch.rpm 
```
![导入rpm](https://s2.ax1x.com/2019/07/15/ZoEGS1.png)
2. 完成后使用：
```
 yum install mysql-community-server
```
安装MySQL社区版
![Mysql社区版安装](https://s2.ax1x.com/2019/07/15/ZoEawD.md.png)
提示后按y开始安装，会自动下载文件，可能需要一段时间，依网速和服务器位置而定
提示"Is this ok"时再次输入y并回车，确认
![确认](https://s2.ax1x.com/2019/07/15/ZoEBYd.md.png)
# 设置MySQL
1. 启动MySQL
使用
```
sudo service mysqld start
```
启动MySQL服务
使用
```
sudo service mysqld status
```
查看状态
![状态](https://s2.ax1x.com/2019/07/15/ZoEylt.png)
不能使用mysqld来启动，会启动失败
![启动失败](https://s2.ax1x.com/2019/07/15/ZoEdTe.png)
2. 连接到MySQL
MySQL8不支持无密码登录，在初次安装时就自动生成了root用户的密码
使用
```
 sudo grep 'temporary password' /var/log/mysqld.log
```
查看root用户的密码
![查看密码](https://s2.ax1x.com/2019/07/15/ZoEDfA.png)
使用
```
mysql -u root -p
```
并输入默认密码（Linux系统输入密码时不会显示）
连接到MySQL服务器
![连接到MySQL](https://s2.ax1x.com/2019/07/15/ZoE66P.md.png)
# 修改默认密码
连接到MySQL服务器后，使用
```
alter user 'root'@'localhost'IDENTIFIED BY 'pWD123456-';
```
修改root的密码，其中'pWD123456-'是要修改成的新密码
> 注意！MySQL8默认安装 validate_password 插件。这将要求密码包含至少一个大写字母，一个小写字母，一个数字和一个特殊字符，
并且密码总长度至少为8个字符。

如果不符合密码规范，修改密码会失败
```
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
```
修改成功会控制台输出
```
Query OK, 0 rows affected (0.01 sec)
```
![实例](https://s2.ax1x.com/2019/07/15/Zoe76I.png)
使用“exit”或“quit”命令退出MySQL

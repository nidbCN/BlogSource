---
title: MySQL8服务器配置远程连接用户
author: Gaein nidb
tags: 
 - 网站建设
 - 数据库
 - MySQL
categories: 服务器运维
date: 2019-07-15 08:50:50
---
最近需要有网站程序连接到数据库，但网站程序放在另外一个服务器上，所以需要设置数据库的远程连接。不同于网上给root远程连接权限的方法，我准备新建一个用户。
<!-- more -->
>最近需要有网站程序连接到数据库，但网站程序放在另外一个服务器上，所以需要设置数据库的远程连接。不同于网上给root远程连接权限的方法，我准备新建一个用户。

> 服务器环境：
操作系统：CentOS7
MySQL版本：8.0.16 MySQL Community Server 

########## 首先，登录服务器ssh
使用
``` 
mysql -u root -p
```
并输入密码，进入mysql
由于我使用的是MySQL8，安全级别比以前高，不能同时创建用户并且赋值
首先创建用户
``` 
create user testuser@'%' identified  by 'TestUserPWD0';
```
再进行赋值
``` 
grant all privileges on *.* to testuser@'%' with grant option;
```
刷新
``` 
flush privileges
```
因为MySQL8使用的是caching_sha2_password加密规则，所以为了方便连接，需要更改一下规则
``` 
ALTER USER 'testuser'@'%' IDENTIFIED WITH mysql_native_password BY 'TestUserPWD0';
```

########## 注意事项
testuser是你要创建的用户的用户名
TestUserPWD0是新创建的用户的密码
MySQL8默认安装 validate_password 插件。这将要求密码包含至少一个大写字母，一个小写字母，一个数字和一个特殊字符，并且密码总长度至少为8个字符。
如果无法连接，主要检查服务器防火墙或安全狗等规则限制

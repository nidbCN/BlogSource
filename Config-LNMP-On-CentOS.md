---
title: CentOS配置PHP+Nginx+MySQL——记一次环境配置的过程
author: Gaein nidb
categories:
  - 服务器运维
tags:
  - PHP
  - MySQL
  - CentOS
  - nginx
  - Linux
date: 2020-04-16 15:40:30
---

## 缘由

> 最近阿里云的服务器到期了，由于阿里云学生机活动规则更改了，而我手里的服务器已经升级无法再使用优惠价格续费。只好中转其它服务器，再将手里的第二台服务器退款。总之是很折腾的

## 过程

我选择的安装方式并不是一键安装包、也没有用面板。总感觉那些东西安装完成之后不在自己习惯的位置。用着很不舒服，改配置文件还要搜索。所以我使用熟悉的yum来进行安装

### 使用我自己写的脚本

` wget https://res.gaein.cn/files/bash/init.sh&&sudo ./init/sh `

进行自动化安装
（如果提示未知命令请先安装wget，参考下文）

> 脚本因为用不到所以删了

### 安装基础软件I

包括 

* wget (下载) 
* tmux (终端复用)。

没有他们无法完成下一步的配置yum源。

#### 安装wget

使用命令 ` wget ` 

如果输出

```bash
wget: missing URL
Usage: wget [OPTION]... [URL]...
Try 'wget --help' for more options.
```

则已经安装wget。

如果输出未知命令之类的，则使用 ` yum install -y wget ` 安装wget。
#### 安装tmux

> tmux是一款优秀的终端复用神器，它可以在后端运行。也就是在里面运行的进程不会在你关闭SSH时结束。对于压缩转移大文件、下载大文件很有用（比如稍后我们安装MySQL用到将近一个小时，就是在tmux里面操作的）同时tmux还提供窗口切分、切换，让我们更方便的使用终端。


**目前已知tmux和lrzsz不兼容**

![tmux截屏](https://img.cdn.gaein.cn/website_used/blog/Config-LNMP-On-CentOS/01.webp)

使用命令 `tmux --help` 来查看是否安装了tmux。如果输出

```bash
usage: tmux [-2CluvV] [-c shell-command] [-f file] [-L socket-name]
            [-S socket-path] [command [flags]]
```
则已经安装，输出未知指令之类的，则使用 `yum install -y tmux` 进行安装

##### 使用tmux

tmux采用快捷键 `Ctrl+B` 来激活控制台。同时按下键盘上面的`Ctrl` 和 `B` ，然后在按键盘就是tmux命令（快捷键）了。注意，使用 `Ctrl+C` 无法强制结束tmux，只会结束里面的任务。
`Ctrl+B` 后的常用快捷键（注意先按Ctrl+B再按下快捷键！）

- ` D `隐藏tmux（类似于windows的最小化，隐藏窗口而不退出，里面的进程继续）
- ` X `关闭tmux当前窗口（需要输入 ``` y/n ``` 确定或取消）
- ` N `下一个窗口
- ` P `上一个窗口
- ` % `将窗口分为左右两块
- ` " `将窗口分为上下两块，注意是英文半角的"

记住这些命令也就暂时够了，其它的以后再研究

### 配置yum源

默认的yum源速度较慢、而且提供的软件不是很多。

####  配置epel

使用命令 `yum install -y epel-release` 安装epel

#### 使用阿里镜像

输入命令 

`wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo`

下载repo文件

#### 配置WebStatic

WebStatic提供最高到PHP7.2（目前我就需要PHP,yum默认的是PHP5.6）

使用命令

`yum install -y https://mirror.WebStatic.com/yum/el7/WebStatic-release.rpm `

安装rpm包

配置完后别忘了使用

`yum clean all && yum makecache` 来清空yum的缓存，使用 `yum update` 来进行更新。

### 安装基础软件II

操作服务器的一些常用软件。一般来说都预装的。可惜，我这神奇服务器没有。

包括：
* vim(文本编辑) 
* curl(下载) 
* tar(tar压缩) 
* 7za(7z压缩)  
* lrzsz(通过XShell终端传输文件) 
* git

对于这些软件我无所谓版本，直接 `yum install` 安装就可以(不说都是最新的也旧不了太多吧)

这些软件都可以使用yum安装

`yum install -y curl lrzsz yum-utils vim p7zip git-core`

### 安装nginx

使用命令

`wget http://nginx.org/packages/centos/7/x86_64/RPMS/nginx-1.16.1-1.el7.ngx.x86_64.rpm `

下载rpm包，然使用命令

` rpm -ivh nginx-1.16.1-1.el7.ngx.x86_64.rpm `

安装nginx官方的rpm（版本1.16.1）需要其它可以自行到官方查看

[nginx官网](https://nginx.org/packages/)

> 注意：使用 `yum install nginx` 安装的为WebStatic源中的1.12版

#### 常用命令

```bash
nginx -v #查看nginx版本
service nginx start|stop|restart #启动|停止|重启nginx服务
nginx -t #检查配置文件是否正确
nginx -s reload #重载配置文件
nginx #启动nginx
```
### 安装PHP
使用命令

`yum install -y php72w-common php72w-fpm php72w-opcache php72w-gd php72w-mysqlnd php72w-mbstring php72w-pecl-redis php72w-pecl-memcached php72w-devel`

进行安装

这里安装了PHP PHP-FPM和一些常用模块，可以自行裁决。一般这些模块已经可以完美的支持WordPress、ZBlog等博客程序了

> 如果想要安装PHP其它版本，把72w改成版本+w就可以咯

### 安装MySQL
参考以前写过的文章：

[CentOS部署MySQL8步骤](https://blog.gaein.cn/passages/MySQL8-CentOS7/)

进行，这里只写主要步骤

下载rpm 

`wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm`

//由于MySQL的rpm很大，所以采用先下载后安装

使用命令导入MySQL rpm 

`sudo rpm -Uvh mysql80-community-release-el7-3.noarch.rpm`

使用命令

`yum install -y mysql-community-server `

来安装

### 常用命令

![mysql截屏](https://img.cdn.gaein.cn/website_used/blog/Config-LNMP-On-CentOS/02.webp)

更多命令见 [MySQL常用命令：导入导出、创建数据库等](https://blog.gaein.cn/passages/MySQL-usage/)

```bash
service mysqld start ##启动
service mysqld status ##查看状态
sudo grep 'temporary password' /var/log/mysqld.log ##查看默认密码
mysql -u root -p ##连接
```

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'pWD123456-'; #更改密码
SHOW DATABASES; #显示数据库
USE 数据库名; #切换到数据库
exit #退出
```

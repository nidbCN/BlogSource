---
title: MySQL常用命令：导入导出、创建数据库等
author: Gaein nidb
categories:
  - 服务器运维
tags:
  - MySQL
  - 数据库
  - 服务器
cover: 'https://s1.ax1x.com/2020/04/17/JV7sE9.jpg'
date: 2020-04-17 15:10:44
---
由于博客和其它的程序，经常要接触MySQL数据库。自己暑假想要学习PHP，肯定也要用到MySQL数据库。为了避免经常上网查常用的命令，就写篇博客备用。
<!--more-->
p.s. 为什么我感觉我从一个写桌面程序(CSharp.NET)的孩子变成了web运维(???)
## 相关链接
[菜鸟教程](https://www.runoob.com/mysql)
[MySQL官网下载](https://www.mysql.com/downloads/)
## 终端命令
###### 连接MySQL
```bash
mysql -p ##使用root连接，如果需要在命令行中输入密码，则密码与"-p"之间没有空格
```
参数
```bash
-u ##指定用户
-p ##密码（必选，新版MySQL已经不支持无密码登录）
-P ##端口，默认3306
-h ##服务器地址
-D ##数据库名
```
###### 开启、关闭MySQL
```bash
mysqladmin -u root -p shutdown ##关闭MySQL，需要输入密码
##切记！千万不要使用kill等命令关闭MySQL，可能会造成数据损坏
service mysqld start ##启动MySQL
service mysqld status ##查看运行状态
```
###### 查看默认密码
```bash
sudo grep 'temporary password' /var/log/mysqld.log  ##刚安装完MySQL时候使用此命令查看初始化的密码
```
## 备份与恢复
###### 备份（导出.sql文件）
终端输入：
```bash
mysqldump -u root -p 用户名 数据库名 > dump.sql
```
###### 恢复（导入.sql文件）
MySQL输入：
```mysql
SOURCE /data/文件路径;
```
###### 修复
有时候错误的关闭MySQL会造成数据损害，此时可以尝试修复。MySQL内输入命令：
```mysql
CHECK TABLE 表名称;##检查数据表是否损坏
REPAIRE TABLE 表名称;##修复损坏的表
REPAIR TABLE EXTENDED 表名称;##速度慢，修复率高
```
## MySQL命令
###### 用户管理
> 新版MySQL的密码规则要求密码必须包含大小写字母、特殊字符、数字，并且长度在8个字符及以上。不符合会弹出"ERROR 1819 (HY000): Your password does not satisfy the current policy requirements"
> 在某些数据库中localhost可能是"%"
########## 新建用户
```mysql
CREATE USER '用户名'@'localhost' IDENTIFIED BY '密码';
```
########## 添加权限
```mysql
GRANT 权限 ON 数据库.数据表 TO '用户名'@'localhost' WITH GRANT OPTION;
```
其中权限有
```
1) 授予数据库权限时，<权限类型>可以指定为以下值：
SELECT：表示授予用户可以使用 SELECT 语句访问特定数据库中所有表和视图的权限。
INSERT：表示授予用户可以使用 INSERT 语句向特定数据库中所有表添加数据行的权限。
DELETE：表示授予用户可以使用 DELETE 语句删除特定数据库中所有表的数据行的权限。
UPDATE：表示授予用户可以使用 UPDATE 语句更新特定数据库中所有数据表的值的权限。
REFERENCES：表示授予用户可以创建指向特定的数据库中的表外键的权限。
CREATE：表示授权用户可以使用 CREATE TABLE 语句在特定数据库中创建新表的权限。
ALTER：表示授予用户可以使用 ALTER TABLE 语句修改特定数据库中所有数据表的权限。
SHOW VIEW：表示授予用户可以查看特定数据库中已有视图的视图定义的权限。
CREATE ROUTINE：表示授予用户可以为特定的数据库创建存储过程和存储函数的权限。
ALTER ROUTINE：表示授予用户可以更新和删除数据库中已有的存储过程和存储函数的权限。
INDEX：表示授予用户可以在特定数据库中的所有数据表上定义和删除索引的权限。
DROP：表示授予用户可以删除特定数据库中所有表和视图的权限。
CREATE TEMPORARY TABLES：表示授予用户可以在特定数据库中创建临时表的权限。
CREATE VIEW：表示授予用户可以在特定数据库中创建新的视图的权限。
EXECUTE ROUTINE：表示授予用户可以调用特定数据库的存储过程和存储函数的权限。
LOCK TABLES：表示授予用户可以锁定特定数据库的已有数据表的权限。
ALL 或 ALL PRIVILEGES：表示以上所有权限。
2) 授予表权限时，<权限类型>可以指定为以下值：
SELECT：授予用户可以使用 SELECT 语句进行访问特定表的权限。
INSERT：授予用户可以使用 INSERT 语句向一个特定表中添加数据行的权限。
DELETE：授予用户可以使用 DELETE 语句从一个特定表中删除数据行的权限。
DROP：授予用户可以删除数据表的权限。
UPDATE：授予用户可以使用 UPDATE 语句更新特定数据表的权限。
ALTER：授予用户可以使用 ALTER TABLE 语句修改数据表的权限。
REFERENCES：授予用户可以创建一个外键来参照特定数据表的权限。
CREATE：授予用户可以使用特定的名字创建一个数据表的权限。
INDEX：授予用户可以在表上定义索引的权限。
ALL 或 ALL PRIVILEGES：所有的权限名。
```
（权限列表摘自biancheng.net）
########## 回收权限
```mysql
REVOKE 权限 ON 数据库.数据表  FROM '用户名'@'localhost';
```
########## 更改密码
```mysql
ALTER USER '用户名'@'localhost' IDENTIFIED BY '密码'; ##更改密码
```
########## 删除用户
```mysql
DROP USER '用户名'@'localhost';
```
###### 数据管理
########## 列出数据库
```mysql
SHOW DATABASES;
```
########## 选中数据库
```mysql
USE 数据库名;
```
########## 查看数据库大小
```
USE information_schema;
select concat(round(sum(data_length/1024/1024),2),'MB') as data from tables;##查看总大小
select concat(round(sum(data_length/1024/1024),2),'MB') as data from tables where table_schema='数据库名';##查看指定数据库大小
select concat(round(sum(data_length/1024/1024),2),'MB') as data from tables where table_schema='数据库名' and table_name='表名';##查看指定表的大小
```
########## 新建数据库
```mysql
CREATE DATABASE 数据库名;
```
########## 删除数据库
```mysql
DROP DATABASE 数据库名;
```

title: 'GET、POST都是什么——常用的HTTP方法——ASP.NET Core Web API(4)'
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - CSharp
  - WebAPI
  - dotNET
  - AspNetCore
  - 编程
  - 笔记
  - 后端
date: 2021-2-3 23:45:55

---

大家在处理Web程序的时候经常使用一些`GET`、`POST`等词汇，这是HTTP协议中规定的HTTP方法，用来发送给服务器表示对一些资源的处理。这里记录一下这些常见的HTTP方法的含义及作用。

<!--more-->

## 前言

> 根据杨旭老师在哔哩哔哩上的课程 [ASP.NET Core 3.x 构建 RESTful API](https://www.bilibili.com/video/BV1XJ411q7yy) 记录的笔记，以后的笔记会根据课程进行更新。

> 这一节主要说各种常见HTTP方法的用途和作用。

## HTTP 请求方法

> 关于 HTTP 方法，可以参考[HTTP 请求方法 - HTTP | MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods)

不同的动作作用于同样一个 URI，区别他们的是 HTTP 方法

#### “增删改查”

###### HTTP POST

创建/添加一个资源，比如发表一篇文章或者评论。

POST 的参数放在请求的 body 里。

POST 收到请求应该返回新创建的资源以及可以获取该资源的唯一标识 URI。

###### HTTP GET

获取一个资源，比如访问界面、列出产品列表等。

GET 的参数通常使用查询字符串，在 URI 的?后边，使用<参数> = <值>，多个查询使用"&"号连接。

GET 收到请求应该返回所查询的资源。

###### HTTP DELETE

删除一个资源，比如删除用户、文章等。

DELETE 通常没有参数。

DELETE 收到请求后应该删除资源（或标记为删除），无需返回信息，返回状态码`HTTP 204`即可。

###### HTTP PATCH

更新一个资源的一部分，比如用户更改头像、联系方式。

PATCH 的参数放在请求的 body 里面。

PATCH 收到请求后应该更新资源，返回状态码`HTTP 204`即可。

###### HTTP PUT

替换更新一个资源，Id 不变。如果没有这个资源则创建，但是多次请求结果不变。比如过户某账号。

PUT 的参数放在请求的 body 里，是完整的对象。

PUT 收到请求后应该替换资源但是 ID 不变，返回状态码`HTTP 204`即可。

#### 总结

| 动作 | HTTP 方法 | Payload | 参数位置 | URI | 请求更改 | 响应内容 |
| ---- | --------- | ------- | -------- | --- | -------- | -------- |
| 查询 | GET | 查询参数 | Query | 端点 + ID | 无修改 | 单个资源或多个资源的集合 |
| 添加/创建 | POST | 要创建的资源的对象 | Body | 端点 | 新建资源 | 新创建的资源 |
| 局部修改/更新 | PATCH | 待修改资源的JSON键值对 | Body | 端点 + ID | 更新资源的某些字段 | 无需返回 |
| 替换 | PUT | 要替换的单个资源对象 | Body | 端点 + ID | 替换除了ID外整个资源 | 无需返回 |
| 使用预定义的标识进行创建 | PUT | 要创建的单个资源对象 | Body | 端点 + ID | 创建了新的资源 | 返回新创建的资源 |
| 移动/删除 | DELETE | 无 | 可含Query | 端点 + ID | 删除资源 | 无需返回 |
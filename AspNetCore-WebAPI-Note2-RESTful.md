---
title: 什么是 REST——RESTful API 简介——ASP.NET Core Web API(2)
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
date: 2021-02-02 15:48:49
lastmod: 2025-01-14 10:17:00
---

经常听说 RESTful API ，那么究竟什么是 REST，怎么判断一个 API 的成熟度呢？

## 前言

> 根据杨旭老师在哔哩哔哩上的课程 [ASP.NET Core 3.x 构建 RESTful API](https://www.bilibili.com/video/BV1XJ411q7yy) 记录的笔记，以后的笔记会根据课程进行更新。

> 这一节主要是说说啥是 REST，REST 的优点与约束以及成熟度模型。

> 关于 API 设计的规范，可以看微软的 API 指南：[microsoft/api-guidelines: Microsoft REST API Guidelines](https://github.com/microsoft/api-guidelines)

## RESTful API

> 主要是 REST 的概念和介绍（挺无聊的，抄 PPT）

### 什么是 REST

REST: 状态表述转换

符合 REST 的良好 Web 应用设计：

1. 一组网页的网络（一个虚拟状态机）；
2. 点击链接来前进（状态转换）；
3. 点击链接的结果是下一个网页（程序的下一个状态）。

REST 是一种架构风格，而不是规范或者标准，它需要一些规范、协议和标准来实现。REST 与协议无关。JSON 与 HTTP 并不是 REST 强制的（但是大部分都是）。

### 优点

1. 简单高效；
2. 可扩展性、可修改性高；
3. 可移植性；
4. 可靠性；
5. 可视性。

### REST 的约束

考虑到以下约束，并确定不保证互不干扰

1. 客户端-服务端：独立部署（前后端分离）；
2. 无状态：服务端不需要客户端的会话（相关请求需要全部包含，客户端要维护自己的状态）；
3. \*统一的资源接口：API 接口必须统一，用相同标准的接口；
   1. 资源的标识：URI、数据类型（？）、媒体类型对数据进行描述（application/json 等）；
   2. 通过表述对资源进行操作：获取的时候服务端将删除或修改操作的 URI 返回；
   3. 带有自我描述的信息：把相关的信息随着请求一起发送到服务端；
   4. 超媒体作为应用程序状态的引擎：即使用 HTTP 状态码；
4. 多层系统：多层架构，每一层与不相邻的层解耦；
5. 可缓存：每个响应信息都应返回它是否可缓存；
6. （按需编码）：服务器可以拓展客户端的功能，比如发送一些 JavaScript 给网页客户端。

> 如果一个 Web API 没有实现这五个约束，则不是 RESTful API。不是 RESTful API 并不能说明它不好。合适才是最好的。

### Richardson 成熟度模型

用来评估 RESTful API 的成熟度，有四个级别

#### Lv.0 沼泽

仅仅使用 HTTP 协议，其余部分没有实现。比如在同一个 URI 上同一个 HTTP 动词做不同的操作（反正就是垃圾的一批）

比如以前常见的 `POST https://api.example.com/cmd.php?command="getpost"&arg="page\=3"` 多了之后容易令人迷迷糊糊，调试起来也不方便。

#### Lv.1 资源

URI 正确使用，不同的资源有 URI 做区分。但是 HTTP 动词没有正确（比如查询使用`POST`方法）

#### Lv.2 动词

HTTP 动词正确使用，返回 HTTP 状态码正确（符合统一资源接口）。

> ~~我也就这样了~~

#### Lv.3 超媒体

前面全部正确。同时返回了其它操作的链接。

> 这仅仅是 RESTful 的前提，只有达到 Lv.3 才可能是 RESTful API。
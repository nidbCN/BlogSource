title: 'Asp.NET Core 入门(2)——配置文件'
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - CSharp
  - dotNET
  - AspNetCore
  - 编程
  - 笔记
  - 后端
date: 2021-1-28 02:19:14

---

ASP.NET Core 提供了一个默认的配置文件——"appsettings.json"，这个文件中写了启动ASP.NET Core的一些配置，比如环境、log等级等，也支持写入自定义的JSON对象。

<!--more-->

# 前言

> 这是学习ASP.NET Core的笔记，主要是根据微软MVP杨旭的课程来走的，当然也有自己的偏向。关于这些内容的笔记和代码以及更多的简介在[Github](https://github.com/nidbCN/ASP.NET-Core-Note)上。

> ASP.NET Core 提供了一个默认的配置文件——"appsettings.json"，这个文件中写了启动ASP.NET Core的一些配置，比如环境、log等级等，也支持写入自定义的JSON对象。  
> 这一篇主要是看 ASP.NET Core 项目的配置文件等内容。

# ASP.NET Core 中的配置信息

## 特点

1. 使用键值对；
2. 存储在内存、JSON、XML、INI 等文件，或者系统环境变量；
3. 配置信息与配置系统是解耦的，不是和某一部分写死的；
4. 可以依赖注入。

## 信息源

按照查找顺序排序

1. 配置文件通常为 `appsettings.json` 和 `appsettings.{Environment}.json`
2. Secret Manager（？）（优先级高于 `appsettings.json` ）
3. 环境变量
4. 命令行参数（优先级最高）

## 自定义配置

1. 在 `appsettings.json` 里面添加自定义信息；
2. 在 `StartUp` 的构造方法中添加参数，类型为 `IConfiguration` ，赋值给一个 `readonly` 的私有变量，或者只有 `get` 访问器的变量；

### 使用索引获取

实际上这个接口对象实际上是一个存有配置信息键值对的列表，可以通过索引比如：  
JSON

```json
{
  "Logging": {
    // ...
    // Some Key-value
    // ...
  },
  "AppConfig": {
    "port": 3306
  }
}
```

> C#

```cs
var myConfig = Configuration["AppConfig:port"];
// string myConfig = "3306"
```

### 映射到类

但是 C#作为强类型语言，比较好的做法是用强类型表示，可以避免很多错误。  
将自定义的 JSON 对象映射到一个类中：

1. 创建类

```cs
public class AppConfig
{
    // 字段名称和JSON文件中应该是对应的
    public int Port { get; set; }
}
```

2. 注册服务

```cs
    // 其中GetSection的参数是string 键
    services.Configure<AppConfig>(
        Configuration.GetSection("AppConfig")
    );
```

3. 使用自定义配置文件，如 `config.json` ，需要更改 `CreateHostBuilder` 方法，使其返回值调用 `ConfigureAppConfiguration` 方法：

```cs
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        // 调用这个方法
        .ConfigureAppConfiguration((context, configBuilder) =>
        {
            // 清除源
            configBuilder.Sources.Clear();
            // 添加json文件夹
            configBuilder.AddJsonFile("config.json");
        })
        // ...
        // Code
        // ...
        ;
```

## 使用配置

完成上文的服务注入后：  
在接口中注入，构造函数添加以下参数：

```cs
public SomeMethod(IOptions<AppConfig> options)
{
    // ...
    // Codes
    // ...
}
```

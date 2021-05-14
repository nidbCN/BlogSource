---
title: ASP.NET Core 代码各个文件都是什么——项目结构——ASP.NET Core 入门(1)
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
date: 2021-1-26 22:35:31
---

## 前言

> 不知不觉新的一年来了，大学半年生活也落下帷幕。经过这半年和各位大佬们的摸鱼，深感自身技能不足，趁着寒假没什么事情，学习一下 web 框架——ASP.NET Core（主要是后端部分）。选择 ASP.NET Core 是因为 ASP.NET Core 是高效的web 框架，同时自己用了挺长时间的 CSharp ，转战其它语言也挺不习惯。另外，算是半个软粉了。  
> ~~不过有一个缺点是，ASP.NET 这玩意老是自动蓝成链接。~~

> 这是学习ASP.NET Core的笔记，主要是根据微软MVP杨旭的课程来走的，当然也有自己的偏向。关于这些内容的笔记和代码以及更多的简介在[Github](https://github.com/nidbCN/ASP.NET-Core-Note)上。

> 这一篇主要是看 ASP.NET Core 项目的结构、配置以及一些基础的内容。

## ASP.NET Core

ASP.NET Core 和 ASP.NET 不是一回事，ASP.NET Core 基于.NET Core（或者.NET 5），它的效率远高于基于 Framework 的 ASP.NET。  
ASP.NET Core 项目本质上仍然是.NET Core 控制台应用。

## 项目基本结构

### Main 方法

程序入口点，配置整个 ASP.NET Core 然后运行。  
调用了一个静态方法

```cs
IHostBuilder CreateHostBuilder(string[] args)
```

调用这个方法，返回 `IHostBuilder` 执行 `Build()` 方法后控制台项目转变为了 ASP.NET Core 项目，然后执行 `Run()` 方法启动。

### CreateHostBuilder 方法

lambda 表达式

```cs
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(
            // ...
            // Code
            // ...
        );
```

，右侧返回调用 `Host` 静态类的一个静态方法 `CreateDefaultBuilder()` 的返回调用 `ConfigureWebHostDefaults()` 方法，配置 ASP.NET Core。  
第二个 lambda 表达式

```cs
// ...
// Code
// ...
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

左侧参数名为自动推断类型的 `webBuilder` ，将 WebBuilder 的一些方法返回作为参数传入。

> 这部分可以配置 ASP.NET Core 相关的内容，只需要调用 `webBuilder.<方法>()`

然后 `webBuilder` 使用 `StartUp` 类来初始化。

### StarUp 类文件

这个类没有实现任何的接口等，这里面方法的名称是运行时的约定名称，运行时通过名字来进行调用两个方法：`ConfigureServices`和`Configure`。

#### ConfigureServices 方法

负责配置依赖注入（DI）等

##### 依赖注入 DI

它是 ASP.NET Core 不可分割的一部分，依赖于 IoC 容器。

首先在 ASP.NET Core 启动时会进行注册，可以向 IoC 容器请求被注册的服务的示例。在注册时可以配置被注册的服务的生命周期。  
生命周期分为三种：

1. `Transient` : 每次被请求这个服务的时候都会生成一个新的实例；
2. `Scoped` : 每次 Web 请求的时候生成一个新的实例，生命周期到 Web 请求最终处理完；
3. `Singleton` : 一旦被创建，每次请求都是这一个实例；

###### 注册服务

使用通过 `IServiceCollection` 接口的变量 `service` 注册服务，比如 MVC 的注册方法：

```cs
services.AddControllersWithViews();     // 传统 ASP.NET Core MVC
```

不需要 `Views` 做 API 只用 Controllers 则使用

```cs
services.AddControllers();              // 添加控制器
```

###### 自定服务

1. 创建一个文件夹(通常命名为"Services")
2. 新建一个接口，比如 `IClock`

   ```cs
    public interface IClock {}
   ```

3. 新建 CSharp 类，实现接口
   ```cs
    public class ChineseClock: IClock {}
   ```
4. 注册自己写的服务：
   ```cs
    services.AddTransient<>();
    services.AddScoped<>();
    services.AddSingleton<>();
   // 注册指定生命周期的服务
   ```
   比如：
   ```cs
   // 泛型：有实例请求IClock接口的对象,返回ChineseClock类型的实例
    services.AddSingleton<IClock, ChineseClock>();
   ```
5. 使用服务：

   1. 建立文件夹"Controllers"，ASP.NET Core 一般都有一个文件夹叫做这个名字，里面放着所有的控制器；
   2. 新建类"xxxxController.cs"，按照约定，应该以 Controller 结尾;
   3. 首先让新建的这个类（比如 `HomeController` ）继承 Controller（在命名空间 `Microsoft.AspNetCore.Mvc` 下）这个父类；
   4. 写构造函数，参数类型为前面定义的接口；

      ```cs
        public class HomeController: Controller
        {
            public HomeController(IClock clock)
            {
            }
        }
      ```

      > 注册服务后，传给控制器的参数的类型为泛型的第二个参数；这样写实现一个接口可以保证在更改服务注入其它类的时候不需要更改控制器的传入参数。这样可以使控制器和具体的服务类解耦。

###### DI 的优点

1. 解耦，没有强依赖，利于进行单元测试；
2. 不需要了解具体的服务类；
3. 不需要手动管理服务类的生命周期。

#### Configure 方法

实际上传入的参数 `IApplicationBuilder app` 对应的是一个服务，通过依赖注入（在 `CreateHostBuilder()` 时候）注入进来。

此方法用来配置 ASP.NET Core 对处理 HTTP 请求的管道（pipeline），指定整个应用对 HTTP 请求的处理方法，HTTP 请求从管道进去，又从管道返回。

##### 管道与中间件

如果管道什么也没有，那么对 HTTP 请求不会做任何处理。

放在管道里面、处理请求的叫做中间件。

###### 中间件：开发模式设置

```cs
// 判断是否为开发模式
if (env.IsDevelopment())
{
    // 开发模式中间件：显示错误信息页面
    app.UseDeveloperExceptionPage();
}
```

通过设置环境变量来启用或关闭开发模式（？）

环境变量名称为： `ASPNETCORE_ENVIRONMENT`
值为：

1. 开发环境——"Development"
2. 生成环境——"Production"
3. 预发布环境——"Staging"
4. 自定义环境——"自定义字符串"
   > 使用 `env.IsEnvironment("自定义字符串")` 来判断自定义环境变量。

##### 中间件：路由

```cs
app.UseRouting();   // 中间件：使用路由

```

路由中间件能判断 HTTP 请求出现在那个端点。

##### 中间件：端点

端点就是 HTTP 请求的 URL 的结尾部分。这一部分会被中间件件处理。

比如 `https://api.gaein.cn/WebCommitApi/write` 中 `WebCommitApi/write` 就是端点。

```cs
app.UseEndpoints(endpoints =>
{
    // 对端点进行配置
    endpoints.MapGet("/", async context =>
    {
        // 匹配端点后在浏览器打印HW
        await context.Response.WriteAsync("Hello World!");
    });
});
```

通过 lambda 表达式来配置端点。  
如果使用属性标签（就是在 Action 上面中括号书写路由的话），只需要这样就可以了。

```cs
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
});
```

##### 中间件：静态文件

如果需要静态文件的话（返回 HTML、JS 等）需要中间件。

```cs
app.UseStaticFiles();
```

想要使用静态文件，需要新建文件夹名为`wwwroot`，在文件夹里面的静态文件可以被访问到（不需要在 URL 加上"wwwroot"）。

图片存储的路径为
`./wwwroot/daily-picture/today.png`

则 url 为
`https://api.gaein.cn/daily-picture/today.png`

##### 中间件：强制 HTTPS

如果需要将 HTTP 请求强制跳转到 HTTPS 则需要中间件。

```cs
app.UseHttpsRedirection();
```

##### 中间件顺序

中间件的顺序就是处理的顺序，顺序很重要。比如 Auth 会放在前边，路由、端点等写在认证的后边。

## 运行 ASP.NET Core 应用

在 VS 中运行时，有两个选项：可以选择 IIS Express，也可以选择项目本身（ASP.NET Core 控制台应用内嵌了一个 Web 服务器，名叫 Kestrel）。  
可以在“项目”->“右键”->“属性”来配置启动时候的工作。

### 环境设置

1. 通过环境变量设置；
2. 通过约定名称方法，比如 `Configure()` 方法和 `ConfigureDevelopment()` 方法。
3. 通过约定名称类，比如 `StarUpDevelopment` 类。注意，需要设置 Program.cs 文件中的 `webBuilder` ，将其更改为
   ```cs
   webBuilder.UseStartup(typeof(Program));
   ```

### 包管理

#### 后端

##### NuGet

#### 前端

> 但是实际上手的话前后端分离香一些

##### NPM

1. 添加 `package.json` 文件（有模板）
2. 在 `devDependencies` 中添加包，使用 json 键值对来设置包和版本，比如：
   ```json
    "devDependencies": {
        "aPlayer": "1.10.1"
    }
   ```

##### LibMan

1. “右键”->“添加”->“添加客户端库”
2. “提供程序”选择"unpkg"
3. 搜索、安装，会自动安装到 wwwroot 文件夹

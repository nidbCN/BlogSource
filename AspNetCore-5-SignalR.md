title: 'Asp.NET Core 入门(3)——SignalR 实时通信'
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
date: 2021-1-30 13:32:57

---

SignalR 是.NET Core 的实现实时通讯的开源框架，抽象于长轮询、SSE 和 WS 这三种技术之上。用于实时的 web 应用。

<!--more-->

# 前言

> 这是学习 ASP.NET Core 的笔记，主要是根据微软 MVP 杨旭的课程来走的，当然也有自己的偏向。关于这些内容的笔记和代码以及更多的简介在[Github](https://github.com/nidbCN/ASP.NET-Core-Note)上。

> SignalR 是.NET Core 的实现实时通讯的开源框架，抽象于长轮询、SSE 和 WS 这三种技术之上。用于实时的 web 应用。 
> 这一篇主要是SignalR的介绍和 ASP.NET Core 项目使用SignalR等内容。

# SignalR

SignalR 是.NET Core 的开源实时框架，抽象与三种技术（见下）之上。无论使用哪种技术，使用 SignalR 是没有感觉到区别的。  
用于实时的 web 应用。  
传统的是浏览器发送请求、服务器处理请求、返回 payload；实时的 web 应用由 web 服务器主动通知客户端数据有变化。

## 技术

SignalR 使用了三种“底层”的技术来实现实时 Web。分别是：

1. Long Polling: 长轮询；
2. Server Sent Event;
3. Websocket.

Signal 采用了回落机制，有限使用 WS，如果浏览器不支持再降级为 SSE 和 Long Polling。

### 轮询

Polling 是定期向服务器发送请求，有变化则更改数据，很简单，但是浪费资源。  
长轮询：与轮询的不同之处是如果服务器上面的数据没有更改，则保持连接（不会立即返回 `HTTP 204` 并断开），直到超时。超时后再次亲求。

### Server Sent Events (SSE)

使用 SSE，web 服务器可以在任何时间发送数据。而客户端（浏览器）会一直监听进来的信息，这个连接也会一直开放，直到服务器主动关闭。  
浏览器会使用一个叫做 EventSource 的对象来处理传过来的信息。

优点：使用简单，HTTP 协议。
缺点：浏览器有最大并发、只能发送文本，只能单项通讯。

### WebSocket

WS 是不同于 HTTP 的另一个 TCP 协议。

#### 优点

1. 使用 WS 消息可以双向发送（全双工）；
2. 没有 HTTP 的延迟，信息流没有完成的时候 TCP 端口始终打开；
3. 大部分情况下（现代浏览器） SignalR 会使用 WS 协议，这也是最有效的传输方式；
4. WS 可以传输文本或者二进制文件；
5. WS 不受 SSE 连接数限制，大部分浏览器对 WS 连接支持数为 50 个。

#### 生命周期

1. 发送一个 HTTP 请求到服务器进行握手；
2. 通讯；
3. 关闭（会返回关闭原因）。

#### HTTP 握手

1. 每一个 WS 开始的时候都是一个简单的 HTTP Socket；
2. 客户端发送 GET 请求升级 Socket(HTTP 101)；
3. 服务器同意的话（HTTP 101），升级为 WebSocket。

#### 消息类型

消息类型可以是文本或二进制。  
每个消息由一个或多个 Frame 组成。

## RPC

RPC 的优点是可以像调用本地方法一样调用远程服务。
SignalR 采用了 RPC 范式来进行通讯。服务端和客户端可以相互调用彼此的方法。SignalR 负责序列化和反序列化。

## Hub

Hub 是 SignalR 的一个组件，是服务端的一个类。
在 ASP.NET Core 中自己创建的 Hub 类需要继承与基类 Hub。  
在 Hub 类里面可以调用客户端上面的方法，客户端也可以调用 Hub 类里面的方法。  
Hub 可以序列化和反序列化，被序列化的参数的格式叫做 Hub 协议。Hub 的默认协议是 JSON，也支持 MessagePack（二进制的，更紧凑更快速）。

## 横向扩展

负载均衡器会保证每个进来的请求按照一定的逻辑分配。但是长轮询时候请求发送到不同的服务器就很容易出问题。

## 实践

### 配置项目

在 `StartUp` 中注册服务：

```cs
public void ConfigureServices(IServiceCollection services)
{
    // 添加控制器
    services.AddControllers();
    // 添加SignalR
    services.AddSignalR();
}
```

自定义一个测试用的类（这里写个技术调用的）

```cs
public class CountService
{
    private int Count;

    // 获取技术
    public int GetLatestCount() => Count++;
}
```

注册服务

```cs
services.AddSingleton<CountService>();
```

创建一个 Hub，命名为 `CountHub.cs`

```cs
using Microsoft.AspNetCore.SignalR;

namespace SignalRDemo
{
    public class CountHub: Hub
    {
        // 定义变量存放注入的service
        private readonly CountService CntService;

        // 服务注入
        public CountHub(CountService countService)
        {
            CntService = countService;
        }

        // 获取总和
        public async Task GetLatestCount()
        {

            var user = Context.User.Identity.Name;

            Console.WriteLine(user);

            int cnt;
            do
            {
                cnt = CntService.GetLatestCount();
                Thread.Sleep(100);

                // 向所有客户端（正经写应该是返回给Connect的客户端？）返回新的数据
                await Clients.All.SendAsync("ReceiveUpdate", cnt);
            }
            while (cnt < 10);
            await Clients.All.SendAsync("Finished");
        }

        // ...
        // Code
        // ...
    }
}

```

使用 Hub：配置端点，加入

```cs
// 使用Hub，参数为亲求的端点
endpoints.MapHub<CountHub>("/count");
```

创建一个控制器 `CountController`

```cs
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.SignalR;
using System.Threading.Tasks;

namespace SignalRDemo.Controllers
{
    [Route("api/count")]
    public class CountController: Controller
    {
        private readonly IHubContext<CountHub> CntHub;

        // 注入Hub
        public CountController(IHubContext<CountHub> cntHub)
        {
            CntHub = cntHub;
        }

        // Post请求时返回执行客户端上的方法"SomeFunc"，传递参数对象
        public async Task<IActionResult> Post()
        {
            await CntHub.Clients.All.SendAsync("SomeFunc", new { random = "object" });

            return Accepted(1);
        }

    }
}
```

在 Hub 中重写 `OnConnectedAsync()` 来执行一些方法：

```cs
public override async Task OnConnectedAsync()
{
}
```

常见的操作有：

```cs
// 获取ConnectId
var ConnectId = Context.ConnectionId;
// 获取当前客户端
var client = Clients.Client(ConnectId);

// 执行客户端上面的一个方法，参数为 方法名、参数（对象）
await client.SendAsync("SomeFunc", new { });

// 除了这个客户端（指定ConnectId）以外，其它的客户端执行
await Clients.AllExcept(ConnectId).SendAsync("SomeFunc");

// 将某个客户端加入组，参数为ConnectId和字符串组名
await Groups.AddToGroupAsync(ConnectId, "myGroup");
// 将某个客户端移除，参数同上
await Groups.RemoveFromGroupAsync(ConnectId, "myGroup");
// 对某组中的客户端执行某个方法
await Clients.Group("myGroup").SendAsync("SomeFunc");
```

使用 Hub 身份认证，需要加入 `[Authorize]` 属性

可以使用

```cs
var userName = Context.User.Identity.Name;
```

获取认证的用户名。其它属性也类似。

> 至于 SignalR，我会以后有时间来研究一下。

### 客户端

使用 libman 安装 `SignalR@next`

#### JavaScript

```js
let connection = null;

setupConnection = () => {
  // 设置连接，其中withUrl是请求的url
  connection = new signalR.HubConnectionBuilder().withUrl("/api/count").build();

  // 获得返回值，这里的第一个字符串参数是Hub中Clint.SendAsync("ReceiveUpdate", cnt)中的第一个参数（？可以看作是双方的一个约定）
  connection.on("ReceiveUpdate", (update) => {
    // 打印返回的更新数据
    console.log(update);
    // 更改ID为resultDiv的节点内容（jq）
    $("#resultDiv").text(update);
  });

  connection.on("SomeFunc", function (obj) {
    console.log(obj);
    // SomeFunc是客户端的方法，obj则是传入的参数
  });

  // 开始这个连接
  connection
    .start()
    .catch((error) => console.error("ERROR!" + error.toString()));
};

setupConnection();

// 执行服务端方法，字符串参数为方法名，后面为参数
connection.invoke("GetLatestCount", id);
```

> 翻译自[5 ways to set the URLs for an ASP.NET Core app](https://andrewlock.net/5-ways-to-set-the-urls-for-an-aspnetcore-app/)
> 版权声明：本文:[5 ways to set the URLs for an ASP.NET Core app](https://andrewlock.net/5-ways-to-set-the-urls-for-an-aspnetcore-app/)为[Andrew Lock](https://andrewlock.net/about/)原创，依据 [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) 许可证进行授权，转载请附上出处链接及本声明。


# 为ASP.NET Core应用程序设置URLs的5种办法

默认情况下，ASP.NET Core应用程序监听以下URLs：
* http://localhost:5000
* https://localhost:5001

在这篇文章里我将给出5种不同的办法来改变你的应用程序监听的URLs。

在启动程序的时候改变ASP.NET Core监听的URLs有很多种办法。我有一篇[旧博客](https://andrewlock.net/configuring-urls-with-kestrel-iis-and-iis-express-with-asp-net-core/)说了在ASP.NET Core 1.0中你可以选用的几种办法，这些办法在ASP.NET Core 3.x中大致是相同的：

* `UseUrls()` —— 在 *Program.cs* 中写死URLs；
* 环境变量 —— 使用 `DOTNET_URLS` 或者 `ASPNETCORE_URLS` 来设置URLs；
* 命令行参数 —— 从命令行启动时使用 `--urls` 参数来设置；
* 使用 `launchSettings.json` —— 在 `applicationUrl` 这个节点中设置；
* `KestrelServerOptions.Listen()` —— 手动使用 `Listen()` 为Kestrel服务器设置地址。

在下面我们来看各个办法的详细说明。

## 你能用什么样的URLs

在这篇文章里我说了你可以监听的"URLs"，但是你不可能只用URL。这有3类URL可以用来监听的URLs。

* IPv4和IPv6的“回路网络接口”（例如 `http://localhost:5000`）。它的格式为:`{协议}://{回路网络接口地址}:{端口}`；
* 在你机器上可用的特殊IP地址（例如 `http://192.168.8.31:5005`）。它的格式为:`{协议}://{IP地址}:{端口}`；
* 指定端口的 “任意” IP地址（例如 `http://*:6264`）。它的格式为:`{协议}://*:{端口}`。

`port` 在上述模式中也是可选的 —— 如果你省略了它，将会使用方案的默认端口（`https` 端口是 `80`，`https` 的端口是 `443`）。

上述模式中你选择那种取决于你的部署机制。比方说，你在一台服务器上托管多个应用程序，你需要设置明确的IP地址。如果你在容器中托管，一般来说你可以使用地址 `localhost`。

> 注意观察 “任意” IP地址的格式 —— 你不一定必须要用 `*`，你可以使用一切不是IP地址和 `localhost` 的租房因此，这也就意味着你可以使用像 `http://*`、`http://+`，`http://mydomain` 或者 `http://example.org`。所有这些都会有相同的行为，并且监听任何（你输入的）IP。如果你只想从一个域名那处理请求，你需要额外设置 [host filtering](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-3.1#host-filtering)。

当你知道你需要你的程序监听那些URLs时，你需要告诉你的程序这些事。在这篇博客里我写了干这件事5种可能的方法。

## UseUrls()

第一种、并且也是最简单的设置绑定URL的的办法是在 `IWebHostBuilder` 中使用 `UseUrls()` 写死：

```cs
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
                webBuilder.UseUrls("http://localhost:5003", "https://localhost:5004");
            });
}
```

把URLs写死看起来并不是一个优雅且可拓展的解决方法，所以这个方法并不怎么用在实际使用中。

幸运的是，你仍旧可以从外部配置文件、环境变量活命令行参数中设置URLs。

## 环境变量

.NET Core使用两 *种* 设置：
* **App设置** 是你通常所使用的，并且从 *appSettings.json* 、环境变量以及其它地方加载。
* **Host设置** 是用来设置基础的东西的，比如 [hosting环境变量](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/environments?view=aspnetcore-3.1) 和host使用的URL。

当考虑如何为应用程序设置URLs时，我们感兴趣的办法是 *Host设置* 。默认情况下，host设置的值从三个不同的源读取：

* 带有 `DOTNET_` 前缀的环境变量。这些环境变量被去除了前缀添加到集合里面；
* 命令行参数；
* 带有 `ASPNETCORE_` 前缀的环境变量，这些环境变量也被添加到了集合里面，仅对ASP.NET Core应用程序有作用。如果你创建了一个.NET 通用主机的服务，那么这些环境变量将不会添加；

如果你不手动重写方法 `UseUrls()`，ASP.NET Core将会使用从设置系统中读取的 `URLS` 键对应的值。基于前文，你可以使用两个环境变量中的任意一个设置URLs：

* DOTNET_URLS
* ASPNETCORE_URLS

> 如果你同时设置了两个环境变量， `ASPNETCORE_URLS` 的参数将会优先被使用。

你可以像通常做的那样在你的环境中设置环境变量。比如，使用命令提示符：

```cmd
setx ASPNETCORE_URLS "http://localhost:5001" 
```

使用PowerShell：

```ps1
$Env: ASPNETCORE_URLS = "http://localhost:5001"
```

或者是在bash里：

```bash
export ASPNETCORE_URLS="http://localhost:5001;https://localhost:5002"
```

就像你在上面看到的那样，你可以通过用分号分割它们来传递多个被监听的地址（用HTTP和HTTPS都行）。

> [Marc在评论中](https://disq.us/p/28v6d6c)指出，如果你在Docker中运行它，[Dockerfile会将`ASPNETCORE_URLS` 环境变量设置为端口80](https://github.com/dotnet/dotnet-docker/blob/402a1591e224b681015be628986eefa5012e940e/3.1/runtime-deps/alpine3.11/amd64/Dockerfile#L15)

## 命令行参数

另外一个设置host设置值的办法是使用命令行。如果设置了环境变量则命令行参数会重写它们。只需要使用 `--urls` 参数：

```bash
dotnet run --urls "http://localhost:5100"
```

像前面那样，你可以通过用分号分割来传递多个URLs：

```bash
dotnet run --urls "http://localhost:5100;https://localhost:5101"
```

环境变量和命令行参数可能是生产环境中设置URLs用的最多的办法了，但是对于在本地开发来说可能略显笨重，因此在本地开发通常简单的使用 *launchSettings.json*

## launchSettings.json

大部分ASP.NET项目模板在 `Properties` 文件夹中包含 `launchSettings.json` 文件。这个文件包含了启动ASP.NET Core应用程序的几种配置。一个典型的例子是包含从命令行直接启动选项的定义还有一个使用IIS Express。这个文件配置了Visual Studio中Debug的下拉菜单：

![Visual Studio](https://img.cdn.gaein.cn/blog/posts/AspNetCore-Set-Urls/launchsettings.png)


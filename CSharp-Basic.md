# C# 基础

> 《C#高级编程（第 11 版）》 笔记，系统入 .NET

> 由于这么多年来一直用 C#摸鱼，所以基础部分只写一些自己认为比较重要的。今天看了一天书，一百来页，结果刚才发现好像都忘了...所以来开个笔记。

## .NET 应用程序和技术

### .NET Framework

旧技术，只能运行于 Windows 操作系统。梦开始的地方。`.NET Framework 4.8` 后无更新计划，但会继续支持。

### .NET Core & .NET

`.NET Core` 是跨平台的、开源的高性能平台。近年来 `.NET Core` 很有起色。`.NET 5` “合并了” `.NET Core` 和 `.NET Framework`，未来的 `.NET` 会以 `.NET` 为名发布，比如今年年末计划发布的 LTS 版本：`.NET 6`

> 如果你开一个新项目的话，用 .NET Core/.NET 5 吧！如果不是维护旧代码或者有特殊需求，真的不建议用 .NET Framework 了。而且新平台上手难度也很低。

### C#

面向对象的类型安全的高性能语言。语法上所 C-Style，与 C/C++、Java 很相似（但是更像 Java 一点），不过相比于 Java 来说语言特性方面有很多优点。

### 关于相关概念的理解

`.NET` 大部分情况下可以指一个生态：它有着一个公共基础架构，包括语言（C#、F#、VisualBasic）以及编译器和运行库组建（比如 GC）。

在其上所`.NET 标准库`

要注意`.NET Standard`不是一个实现而是一个协定，本协定规定了需要实现什么 API。`.NET Framework`、`.NET Core` 和 `Mono` 平台实现了这个协定。但是他们各自实现了 `CLR` —— 公共语言运行时，但是 `.NET Framework` 实现的 `CLR` 就叫做 `CLR`，而`.NET Core` 所实现的 `CLR` 叫做 `CLR Core`。因此 `CLR` 是概念还是实现需要看具体语境。

### CLR

`CLR` 的职责是：将构建时的中间码（IL）编译为本机码。同时还负责加载类型、垃圾回收以及线程处理。

### 开发工具

个人心中觉得的易用程度：

1. Visual Studio 2019 + JetBrains ReSharper 插件
2. Visual Studio Code + C#插件
3. JetBrains ReSharper
4. Visual Studio

> 注： Visual Studio for MAC 没用过，家贫。

## 核心 C#

### C#基础

Hello World:

```cs
using System;

namespace Example
{
    class Program
    {
        static void Main()
        {
            Console.WriteLine("Hello World");
        }
    }
}
```

`Main` 函数是程序的入口点，但是在 C#8 之后，可以忽略`Main`函数直接书写代码，就像“脚本”一样：

```cs
using System;

Console.WriteLine("Please input your name");
var userName = Console.ReadLine();
Console.WriteLine($"Hello, {userName}");
```

它将从第一行代码开始顺序执行。

下面来看看分析下 HelloWorld 的代码：

C#的语法很符合 C/C++那一套：

1. 分号结束语句
2. 标识符区分大小写
3. 花括号包裹语段

包括很多像定义变量、赋值、函数声明与调用什么的也都很相似。

`using` 引用了名称空间
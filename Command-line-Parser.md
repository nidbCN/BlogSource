---
title: C#解析命令行参数
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - CSharp
  - dotNET
  - 翻译
  - 编程
date: 2021-08-02 22:24:35
---

## 版权声明

> "[Command Line Parser for C# | DC Coding](https://codingblog.carterdan.net/2019/04/12/command-line-parser/)" by [Dan Carter](https://codingblog.carterdan.net/About/), ‎translate from original.
>  
> 本文翻译自 [Dan Carter](https://codingblog.carterdan.net/About/) 的 "[Command Line Parser for C# | DC Coding](https://codingblog.carterdan.net/2019/04/12/command-line-parser/)"。

> Anther's permission:
> 原作者许可：
> ![permission](https://img.cdn.gaein.cn/website_used/blog/Command-line-Parser/1.webp)

---

我最近正在折腾 Command Line Parser。这是一个处理、解析命令行参数和选项的样板代码 C# 库（可以用 NuGet 来安装）

它使用常见的约定来确定选项的处理方式（类似于 C 的 `getopt` 函数）。

它的 GitHub page 上有很完善的文档，但是我仍然决定在这里分享一下怎么用。

## 简单选项

假设我们现在正在创建一个用户获取文件属性（大小、文件类型等）的程序，希望支持以下命令行选项：

* `<文件路径>` (必须)
* `-d` 或 `--detailed` （如果存在这个参数则输出所有能获取到的文件属性，否则则输出最常用的信息）
* `-o <文件路径>` 或 `--output <文件路径>` （指定文件属性输出的位置）

然后我们可以创建一个包括可能的命令行选项的属性类 `Options` ，使用特性来修饰期望的具体的用法：

```csharp
class Options
{
    [Value(0)]
    public string FilePath { get; set; }

    [Option('d', "detailed")]
    public bool Detailed { get; set; }

    [Option('o', "output")]
    public string Output { get; set; }
}
```

`Value` 特性定义了按位置识别的参数，`Option` 特性定义了按名字识别的参数。

然后在主函数中轻轻松松的像下面这样写：

```csharp
class Program
{
    static void Main(string[] args)
    {
        CommandLine.Parser.Default.ParseArguments<Options>(args)
            .WithParsed<Options>(o => {
                // 解析成功，继续运行app
            })
            .WithNotParsed<Options>(e => {
                // 解析错误，处理错误
            });
    }
}
```

`Options` 类的实例 `o` 将会被用解析的选项填充，你可以使用它来控制你应用程序的行为。

## 动词

这个库也支持类似于“动词”的玩意，比如 Git（一种版本控制软件）有很多你可以执行的特殊的动作（`git clone`，`git commit`，`git pull` 等），分别有它们自己的选项

在 Command Line Parser 中，你可以为每个动词创建选项类:

```csharp
[Verb("clone")]
class CloneOptions {
    // ...
}

[Verb("commit")]
class CommitOptions {
    // ...
}

[Verb("pull")]
class PullOptions {
    // ...
}
```

然后在你的主函数中定义你要对每个动词干什么以及如何处理错误：

```csharp
class Program
{
    static int Main(string[] args) 
    {
        return CommandLine.Parser.Default.ParseArguments<AddOptions, CommitOptions, CloneOptions>(args)
            .MapResult(
                (CloneOptions o) => { 
                    // clone
                },
                (CommitOptions o) => {
                    // commit
                },
                (PullOptions o) => {
                    // pull
                },
                e => 1);
    }
}
```

## 自定义

上面给出 例子使用了 `CommandLine.Parser.Default` （默认解析方案），但实际上有很多方法来自定义解析。例如，你可以设置要解析的参数的区域性，或者设置解析时是否区分大小写。

## 帮助文本

Command Line Parser 中一个很棒的功能是它可以为你的应用程序生成帮助文本。你需要做的只有在特性中添加 `HelpText` 属性，然后传递 `--help` 参数的某人就会获取到帮助文本。 

例如，之前我们的 `Options` 类可以更改为：

```csharp
class Options
{
    [Value(0, HelpText = "The file to display information for.")]
    public string FilePath { get; set; }

    [Option('d', "detailed", HelpText = "Whether to output detailed information about the file.")]
    public bool Detailed { get; set; }

    [Option('o', "output", HelpText = "If specified, a file to output the results to.")]
    public string Output { get; set; }
}
```

然后使用 `--help` 参数运行应用程序，将会有以下输出：

```
-d, --detailed    Whether to output detailed information about the file.

-o, --output      If specified, a file to output the results to.

--help            Display this help screen.

--version         Display version information.

value pos. 0      The file to display information for.
```

这个文本会在解析参数失败的时候自动输出。

你也可以给你的应用程序添加一个总结，包括一些使用的例子。这些玩意会被添加到帮助文本中。

## 结论

Command Line Parser 带走了解析命令行选项相关的样板代码。我可以清楚的感觉到，这个库从有几个选项的小程序到巨大的复杂应用程序都很有用。

我现在在一个未来博客（指[原作者的博客](https://codingblog.carterdan.net/)）可能提到的足球比赛模拟器用到了这个库...
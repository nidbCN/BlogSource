
## 版权声明

> "[Command Line Parser for C# | DC Coding](https://codingblog.carterdan.net/2019/04/12/command-line-parser/)" by [Dan Carter](https://codingblog.carterdan.net/About/), ‎translate from original.
>  
> 本文翻译自 [Dan Carter](https://codingblog.carterdan.net/About/) 的 "[Command Line Parser for C# | DC Coding](https://codingblog.carterdan.net/2019/04/12/command-line-parser/)"。

> Anther's permission:
> 原作者许可：
> 

---

我最近正在折腾 Command Line Parser。这是一个处理、解析命令行参数和选项的样板代码 C# 库（可以用 NuGet 来安装）

它使用常见的约定来确定选项的处理方式（类似于 C 的 `getopt` 函数）。

它的 GitHub page 上有很完善的文档，但是我仍然决定在这里分享一下怎么用。

简单选项

假设我们现在正在创建一个用户获取文件属性（大小、文件类型等）的程序，希望支持以下命令行选项：

<文件路径> (必须)
-d or --detailed （如果存在这个参数则输出所有能获取到的文件属性，否则则输出最常用的信息）
-o <文件路径> 或 --output <文件路径> （指定文件属性输出的位置）

然后我们可以创建一个包括可能的命令行选项的属性类 `Options` ，使用特性来修饰期望的具体的用法：

C#
class Options
{
    [Value(0)]
    public string FilePath { get; set; }

    [Option('d', "detailed")]
    public bool Detailed { get; set; }

    [Option('o', "output")]
    public string Output { get; set; }
}

`Value` 特性定义了按位置识别的参数，`Option` 特性定义了按名字识别的参数。

然后在主函数中轻轻松松的像下面这样写：

C#
class Program
{
    static void Main(string[] args)
    {
        CommandLine.Parser.Default.ParseArguments<Options>(args)
            .WithParsed<Options>(o => {
                // parsing successful; go ahead and run the app
            })
            .WithNotParsed<Options>(e => {
                // parsing unsuccessful; deal with parsing errors
            });
    }
}
The Options object o will be populated with the parsed options, which you can then use to control the behaviour of your app.

Verbs
It also supports the idea of verbs. For example, Git has a number of different actions you can take (git clone, git commit, git pull etc) which each have their own sets of options.

With Command Line Parser, you create a separate options class for each verb:

C#
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
And then in your Main method you define what to do for each verb, as well as how to handle parsing errors:

C#
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
Customisation
The examples I’ve given here use CommandLine.Parser.Default, but it’s possible to customise the parser in a number of different ways. For example, you can set the culture for arguments to be parsed in, or whether to parse values case-sensitively or not.

Help text
One of the really nice things about Command Line Parser is that it can generate help text for your application. All you need to do is add the HelpText property to the attributes, and then if someone passes in the --help switch they’ll get the full help text.

For example, our Options class from before could be changed to:

C#
class Options
{
    [Value(0, HelpText = "The file to display information for.")]
    public string FilePath { get; set; }

    [Option('d', "detailed", HelpText = "Whether to output detailed information about the file.")]
    public bool Detailed { get; set; }

    [Option('o', "output", HelpText = "If specified, a file to output the results to.")]
    public string Output { get; set; }
}
Then running the app with the --help switch gives us the following output:

Output
  -d, --detailed    Whether to output detailed information about the file.

  -o, --output      If specified, a file to output the results to.

  --help            Display this help screen.

  --version         Display version information.

  value pos. 0      The file to display information for.
This text is automatically output if parsing fails.

You can also add an overall summary for your application, along with some examples of how to use it. These are added to the help screen.

Conclusion
Command Line Parser takes away the boilerplate code associated with parsing command-line options. I can see that this would be useful on a small app with a couple of options right up to a hugely complex app with dozens of options.

I’ve recently used it on a football match simulation app, which may be the subject of a future blog post …
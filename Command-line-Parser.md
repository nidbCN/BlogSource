
## 版权声明

> "[Command Line Parser for C# | DC Coding](https://codingblog.carterdan.net/2019/04/12/command-line-parser/)" by [Dan Carter](https://codingblog.carterdan.net/About/), ‎translate from original.
>  
> 本文翻译自 [Dan Carter](https://codingblog.carterdan.net/About/) 的 "[Command Line Parser for C# | DC Coding](https://codingblog.carterdan.net/2019/04/12/command-line-parser/)"。

Anther's permission:



---

I recently came across Command Line Parser. It’s a C# library (available on NuGet) that takes care of the boilerplate code often associated with parsing, validating and using options specified as command line arguments.

It uses common conventions for how it assumes the options will appear (similar to C’s getopt function).

The GitHub page has pretty comprehensive documentation on how to use it, but I thought I’d share here briefly how it works.

Simple options
Suppose we’re creating an application which describes a file in terms of its properties (size, file type etc), and suppose the desired command-line options are:

<file path> (required)
-d or --detailed (if present gives as much information as it can find, otherwise gives the most commonly-wanted info)
-o <file path> or --output <file path> (specifies a file to output the file properties to)
Then we create an Options class with properties for all the possible command-line options, decorated with attributes detailing the expected usage:

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
The Value attribute defines a positional argument, and the Option attribute defines an argument that is passed in by name.

Then in the Main method, it’s as simple as doing the following:

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
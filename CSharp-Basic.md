---
title: 基础 C#笔记 
author: Gaein nidb
categories:
  - 学习资料
tags:
  - 笔记
  - 编程
  - CSharp
  - dotNET
date: 2021-04-24 21:38:19
---

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

## 2 - 核心 C#

### 2.1 - C#基础

#### Hello World

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

#### C#8的“脚本”

`Main` 函数是程序的入口点，但是在 C#8 之后，可以忽略`Main`函数直接书写代码，就像“脚本”一样：

```cs
using System;

Console.WriteLine("Please input your name");
var userName = Console.ReadLine();
Console.WriteLine($"Hello, {userName}");
```

它将从第一行代码开始顺序执行。

#### 每一段代码都干了啥

下面来看看分析下 HelloWorld 的代码：

##### C#的语法

C#的语法很符合 C/C++那一套：

1. 分号结束语句；
2. 标识符区分大小写；
3. 花括号包裹语段；
4. 单行注释以 `//` 开头，多行注释包裹在 `/*` 与 `*/` 之间。

包括很多像定义变量、赋值、函数声明与调用什么的也都很相似。

##### 名称空间

名称空间是把相关类组合在一起的方式。`namespace` 后跟名称空间，花括号内的类属于该名称空间。

`using System;` 引用了名称空间 `System` 。声明语句允许引用这个类（`Console`），而不用去写 `System.Console.WriteLine();`

可以使用 `using static` 不仅可以打开名称空间，也可以引用类的所有静态成员 `using static System.Console` 后可以直接使用 `WriteLine()` 方法。

##### Program类

所有的C#代码都应该包含在类中（C#8那种“脚本”编程除外。）类的声明包含 `class` 关键字，后面跟类名和一对花括号。

事实上这个类叫啥名都行啊

##### 主函数

`C` 、 `C++` 、 `Java` 等很多编程语言都有这个概念，主函数（`Main` 方法）是程序的入口点，程序从这里开始执行。`Main()` 方法的返回值为 `void` 或者 `int`。

在C#中，方法的定义如下：

```cs
// [modifiers修饰符] return_type返回类型 MethodName方法名([parameters参数])
   public           int                ConvertToInt            (double input)
```

`WriteLine` 是一个静态方法，所以不需要先实例化一个 `Console` 对象。

### 2.2 - 变量

使用以下语法声明变量

```cs
// datatype类型 identifier标识符;
   int          number;
```

#### 2.2.1 - 初始化变量

C#中，变量在使用之前必须要被初始化。使用未初始化的变量无法通过编译。如果变量是类或结构中的字段，则会自动初始化为0（是在内存中的0，而不是字面量0）。

在C#中实例化一个对象，需要使用 `new` 关键字：

```
HttpClient client = new HttpClient();
```

#### 2.2.2 - 类型推断

在C#中可以使用类型推断让编译器自动推断类型（使用 `var` 关键字）。在类型名非常长的时候很爽，事实上大部分变量我都在用类型推断让编译器去帮我推断：

```cs
var num = 6; // num is int.
var option = new JsonSerializerOptions()
{
    WriteIndented = true,
};
// option is JsonSerializerOptions.
```

需要遵循以下规则：
1. 变量必须被初始化，不然编译器就没有推断的依据（谁知道你 `var t` 相干啥啊）
2. 初始化器不能为空（也没人知道你 `var t = null` 相干啥）
3. 初始化器必须放在表达式中
4. 不能把初始化器设置为一个对象，除非在初始化器中创建了一个新对象（不能把对象赋值过去`var obj = sb;`，但是你可以在等号右面整个新对象`var obj = new StringBuilder();`）

#### 2.2.3 - 变量的作用域

变量是有作用域的，它遵循以下规则：
1. 只要类的局部变量在某个作用域内，其字段（也称为成员变量）也在该作用域内（对象的字段和对象的作用域一样）；
2. 局部变量存在于声明该变量的块语句或方法结束的右括号之前的作用域内；
3. 在 `for` 、 `while` 或类似语句中声明的局部变量存在于该循环体内。

##### 局部变量的作用域冲突

同名的局部变量不能在同一个作用域内声明两次。

```cs
var t = 5;
vat t = "str";
```
这必报错好吧。

如果在不同的作用域内声明了两个同名变量，小作用域内会使用作用域小的变量（但是我觉得应该尽量避免这种情况）

##### 字段和局部变量的作用域冲突

在类级别（类中）定义的会被认为是字段，而方法内的看作局部变量。

```cs
using System;

namespace Test
{
    class Program
    {
        static int i = 10;
        static void Main()
        {
            int i = 30;
            Console.WriteLine(i); // Output: 30.
        }
    }
}
```

可以看到，在小作用域（方法中）使用了小作用域的变量（`Main` 里面定义的 `i`）而不是字段`i`。在这一部分C#隐藏了作用域更大的字段`i`。
想要使用类级别的变量可以使用 `Program.i` 这样的语法（`object.fieldName`）

#### 2.2.4 - 常量

使用 `const` 关键字定义常量：

```cs
const int size = 1024;
```

这个 `const` 和 C/C++ 中有区别，它的原理更像 `#define SIZE (1024)`。编译器在编译的时候将它替换为字面量。因此，在 `const` 关键字后面定义的常量要在被编译之前能确定它的值。

```cs
const string dir = Environment.CurrentDirectory;    // Error.
```

上面这语句中编译器在编译的时候不知道 `Environment.CurrentDirectory` 的值，因此没有办法编译为常量。

1. 常量必须在声明时初始化。指定了其值后，就不能再改写了。
2. 常量的值必须能在编译时用于计算。因此，不能使用从变量中提取的值来初始化常量。
3. 常量总是隐式静态的。不需要也不允许添加 `static` 关键字。

### 2.3 - 预定义数据类型

#### 2.3.1 - 值类型和引用类型

C#把数据类型分为两种：
* 值类型
* 引用类型

从概念上，值类型存储的是变量的值，而引用类型存储的是变量的引用。

值类型存储在（堆）栈-Stack中，而引用类型存储在堆-Heap中。

在C#中，基本的数据类型大部分值类型，比如`int`、`double`、`bool`等，而大多数更复杂的类型（自己定义的类等）是引用类型。结构体是值类型。

CLR会定期删除不能使用的引用。

如果变量为引用类型则可以把它设置为 `null` ，表示它不引用任何对象。

> 如果去访问一个指向 `null` 的变量的非静态成员函数或字段则会引发一个运行时异常。

#### 2.3.2 - .NET 类型

数据类型的C#关键字从编译器映射到.NET数据类型。在语法上可以把基本类型看作支持某些方法的类。

#### 2.3.3 - 预定义的值类型

##### 整型

有符号整型：

* `sbyte` - 8位
* `short` - 16位
* `int` - 32位
* `long` - 64位
  
无符号整型：
* `byte` - 8位
* `ushort` - 16位
* `uint` - 32位
* `ulong` - 64位

在C#中，所有的数据类型都以与平台无关的方式定义，以备迁移。
所有的整数类型都能被赋予十进制或者十六进制或者二进制，十六进制和二进制使用前缀：
* `0x` - 十六进制
* `0b` - 二进制

如果没有显示的声明则变量默认为 `int` 类型，可以使用后缀：
* `U` - 无符号
* `L` - 长整型
* `UL` - 无符号长整型

可以使用下划线作为数字分隔符，增强可阅读性，比如：

```cs
uint binary = 0b1111_1011_1100_1011_1101_1100_0010;
```

##### 浮点型

* `float` - 32位
* `double` - 64位

指定值为 `float` 需要使用后缀 `F`：

```cs
float width = 23.8F;
```

##### decimal类型

专门用于财务的类型。

`decimal` - 128位

> 不是基本类型，计算的时候会有性能损失。

需要在数字后面加上 `M` 来指定。

##### bool类型

它只有 `true` 和 `false`

> 注意：在C#中 `bool` 类型和整数值不能相互隐式转换。也就是说：
> ```cs
> int flag = 1;
> if (flag) 
>   Console.WriteLine("Yes!"); // wrong.
> if (flag == 1) 
>   Console.WriteLine("Yes!")  // right.
> ```

##### 字符类型

`char` 仅用于存储字符。它可以使用单引号包裹的字面量赋值，也可以使用Unicode、强制转换的整数和十六进制数以及转义字符。

#### 2.3.4 - 预定义的引用类型

##### object类型

在C#中，`object` 类型就是最终的父类型，所有的内置类型和用于定义的类型都从它派生而来。

##### string类型

C#有 `string` 关键字，在遮罩下转换为 `System.String`。

`string` 是一个引用类型，但是目前来说 `string` 类基本上已经实现其语义遵循一般的、直观的字符串规则。

> 可以直接用 `==` 来比较字符串的值啦！

C#字符串也可以包含Unicode和十六进制数转义序列。

前缀：
* `@` 这个字符后所有的字符都看成原来的含义，不会被解释为转义，并且也可以包含换行符（可以写多行）。
* `$` 这个字符串中使用插值，可以使用 `{var}` 来在字符串中插入值，比如：
  ```cs
  var name = "Jack";
  var note = $"Hello, {Jack}!";
  ```

### 2.4 程序流控制

#### 2.4.1 - 条件语句

##### if语句

```cs
if (condition) 
{
    // codes...
}
else
{
    // other codes...
}
```

为了安全和保持一致，建议只要使用if语句就加上花括号，如果真的只有一行并且不想加花括号可以这样写：

```cs
if (isFault) return;
```

##### switch 语句

```cs
switch (t)
{
    case 1:
        Console.WriteLine("1");
        break;
    case 2:
        Console.WriteLine("2");
        break;
    case 3:
    case 4:
        Console.WriteLine("3 or 4");
        break;
    default:
        Console.WriteLine("UnKnow number");
        break;
}
```

> 注意：case的值必须是常量表达式，不允许使用变量。

C#这个玩意它很安全，如果激活了一个 `case` 则其它的 `case` 就不会被激活，而且 `break` 是强制加入的。如果上一个 `case` 没有语句则可不写 `break` 来“滑滑梯”。

还有一个玩意叫 `switch表达式` ，这个东西书上没写，我搓了个 `switch` 来用代码提示转换成了表达式看看：

```cs
var code = 200;

var result = code switch
{
    200 => "OK",
    400 => "BadRequest",
    _ => "UnKnow"
};
```

这个东西呢适用于以下场景：不同的条件返回不同的值，但是类型是一样的。

#### 2.4.2 - 循环

> 这些都跟C差不多咯

##### for 循环

```cs
var sum = 0;

for (var i = 0; i < 100; ++i)
{
    sum += i;
}
```

##### while 循环

```cs
var input = Console.ReadLine();
while(string.IsNullOrWhiteSpace(input))
{
    Console.WriteLine("Invalid input, please retry.");
    input = Console.ReadLine();
}
```

当输入的 `input` 为空的时候会进入循环一直要求输入。

##### do-while 循环

```cs
var input = string.Empty;
do
{
    Console.WriteLine("Please input.");
    input = Console.ReadLine();
}
while(string.IsNullOrWhiteSpace(input));
```

##### foreach 循环

`foreach` 循环可以迭代集合中的每一项，但是它不能改变集合中的各项。

#### 2.4.3 - 跳转语句

##### goto 语句

标签：

```cs
Label:
    Console.WriteLine("L1");
```

跳转：

```cs
goto Label;
```

> 大多数情况下不允许使用goto语句。一般情况下，使用它肯定不是面向对象编程的好方式。

##### break 语句

用于退出 `case` 和退出循环，如果放在嵌套的循环中则退出内部循环。、

##### continue 语句

只退出当前迭代，立即开始下一轮循环。

##### return 语句

退出当前方法并把控制权返回给调用者。如果方法的类型不是 `void` 则必须return相应的类型。

### 2.5 - 名称空间

名称空间是一种逻辑组合，而不是物理组合（事实上要尽量和文件结构对应好一些吧）

名称空间之间用句点隔开。名称空间与程序集无关。

一般来说 `CompanyName.ProjectName.SystemSection` 是可以接受的命名方式，也是微软所推荐的。

#### 2.5.1 - using 语句

输名称空间太长了，所以用using来把它引入，然后就能直接写了，上面说过。

很多C#代码都有 `using System` ，因为微软公司提供的许多有用的都在这个名称空间下。

如果在不同的名称空间下两个类名一样则需要写前面的名称空间。应该尽量避免这种情况的发生。

#### 2.5.2 - 名称空间的别名

`using` 关键字的另一个用途是给类和名称空间指定别名。如果名称空间很长但是还需要避免类名冲突的话可以使用别名，这样写起来爽一些。

> 注意：名称空间的别名的修饰符 `::` 

### 2.6 Main() 方法

要求一般为：

* 使用了 `static` 修饰符
* 在任意名称的类中
* 返回 `int` 或 `void` 类型

（当然，前面说过了，C#8所允许的那样脚本化的操作并不需要满足）

在调用的时候可以让CLR包含参数，将命令行参数传递给程序：

```cs
void Main(string[] args)
```

与C不同的是，`args[0]` 并不是程序的路径，它就是传递的第一个参数。

### 2.7 - 注释

注释是个好东西哦

#### 2.7.1 - 源文件中的内部注释

C#使用传统的C风格的注释：

* 单行注释使用 `//`
* 多行注释使用 `/*` 和 `*/`

不过，在写多行的时候也可以使用多个单行注释，因为使用IDE的快捷键来这么干非常方便。

#### 2.7.2 - XML文档

`///` 用于生成XML风格的注释，常见的XML标签有：

* `<summary>` - 提供类型或成员的简短小结
* `<returns>` - 说明方法的返回值
* `<param>` - 标记方法的参数（编译器要验证其语法）

### 2.8 - C#预处理器指令

#### 2.8.1 - #define 和 #undef

`#define` 本身没啥用（它不能和C/C++那样玩），和 `#if` 配合才有大用。

#### 2.8.2 - #if、#elif、#else 和 #endif

`#if` 如果条件符合那就编译中间的代码，比如：

```cs
#define DEBUG

#if DEBUG
    Console.WriteLine("DEBUG MODE ENABLED!");
#elif
    Console.WriteLine("NORMAL MODE.");
#endif
```

它还支持一组逻辑运算符：`!`、`==`、`!=` 和 `||`

#### 2.8.3 - #warning 和 #error

编译器遇到它们的时候分别产生警告和错误，它后面的文本是给用户显示的信息。

#### 2.8.4 - #region 和 #endregion

用于把一段代码视为有给定名称的一块，一些编辑器会识别并使这些代码在屏幕上更好的布局。

#### 2.8.5 - #line

改变在编译器警告和错误中的行号。

#### 2.8.6 - #pragma

关闭或者开启对某行代码的警告。比如：

```cs
#pragma warning disable 169
class MyClass
{
    int neverUsedField;
}
#pragma warning restore 169
```

上述代码可以关闭“字段未使用”的警告。

### 2.9 - C#编程准则

关于规范方面，ldqk有一篇博客，写的很全面

[全面的C#编码规范整理](https://ldqk.org/1755)

#### 2.9.1 - 关于标识符的规则

标识符是给变量、用户定义的类型和这些类型的成员的名称，准则有：

* 尽管可以包含数字字符，但它们必须以字母或者下划线开头；
* 不能把C#关键字用作标识符。

#### 2.9.2 - 用法约定

C#的约定是命名变量时不使用任何前缀（不用 `char *pszResult` 这样的）：`string Result`

微软规定了许多C#的用法准则，虽然不是强制要求，但是尽量遵守这些准则，除非有明确的理由。

##### 命名约定

###### 名称的大小写

名称空间和类，以及基类中的成员等名称应该遵循 Pascal 大小写规则。

Pascal 大小写规则就是每个单词首字母大写，比如：

```cs
public class ClassMember
{
    // ...
}

public class ListService 
{
    // ...
}
```

应该使用 Pascal 大小写规则的有：

名称空间、类名、方法名、属性或者私有字段（最近Microsoft那边在使用下划线开头命名私有字段，那样更容易区分一些）。

另外一种大小写方式是 camel 大小写形式，它就是第一个单词全部小写，其余单词首字母大写，比如：

```cs
uint itemIndex = 0;
string dataFileFullPath = @"C:\..."
```

通常来说本地变量使用这种大小写规则。

###### 名称的风格

保持一致

比如某个方法为 `ShowConfirmationDialog()` ，则另一个方法不应该叫做 `ShowDialogWarning()` 或者 `WarningDialogShow()` 而是应该命名为 `ShowWarningDialog()`。

###### 名称空间的名称

尽量避免与其它重名，推荐的格式上文说过，是：`<CompanyName>.<TechnologyName>`，例如：

```cs
namespace Microsoft.Text
{

}
```

###### 名称和关键字

名称不应该与任何关键字冲突

##### 属性和方法的使用

如果这个看起来感觉是变量，就应该用属性来表示，而不是方法。总结来说：

* 不要将只写的设置为属性，比如应该使用 `SetPassword()` 方法而不是只写的 `Password` 属性；
* 读取该值不应该花费太多时间；
* 读写该值不应该有其它明显的效果，它的作用应该仅与这个属性相关；
* 可以按照任意的顺序去设置属性，不应该因为属性没有设置完而抛出异常；
* 如果属性可能有意想不到的改变，就应该写成方法。

如果不能满足所有的条件，那么就写成方法吧。

##### 字段的使用

字段的用法非常简单。字段应该总是私有的，但是某些情况下可以设置为公有。


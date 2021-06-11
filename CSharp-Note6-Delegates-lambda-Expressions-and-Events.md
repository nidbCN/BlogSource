---
title: CSharp的委托、lambda 表达式和事件 - CSharp笔记 
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - 笔记
  - 编程
  - CSharp
  - dotNET
date: 2021-05-05 21:23:03
---

## 目录

* [CSharp基础](https://blog.gaein.cn/passages/CSharp-Basic/)
* [CSharp面向对象](https://blog.gaein.cn/passages/CSharp-OOP/)
* [CSharp泛型](https://blog.gaein.cn/passages/CSharp-Generics-Type/)
* [CSharp运算符和强制类型转换](https://blog.gaein.cn/passages/CSharp-Note4-Operators-and-TypeCasts/)
* [CSharp中数组的使用](https://blog.gaein.cn/passages/CSharp-Note5-Array/)
* [CSharp的委托、lambda 表达式和事件](https://blog.gaein.cn/passages/CSharp-Note6-Delegates-lambda-Expressions-and-Events/)

## 委托、lambda 表达式和事件

### 8 - 委托、lambda 表达式和事件

#### 8.1 - 引用方法

委托是寻址方法的 `.NET` 版本。在C++中，函数指针只不过是一个指向内存位置的指针，我们不知道它实际指向什么，参数和返回类型就更无从知晓了。而 `.NET` 的委托是安全的，它定义了返回类型和参数的类型，它不仅包含对方法的引用，也可以包含对多个方法的引用。

lambda 表达式与委托直接相关。当参数是委托类型的时候，就可以使用 lambda 表达式实现委托引用的方法。

#### 8.2 - 委托

当要把方法作为参数传递给方法的时候，就需要使用委托。

##### 8.2.1 - 声明委托

首先必须定义要使用的委托，对于委托，定义就是告诉编译器这种类型的委托表示哪种类型的方法。然后必须将委托实例化，编译器将在后台创建该委托的一个类。

语法如下：

```cs
delegate void IntMethodInvoker(int x);
```

声明了一个类型为 `IntMethodInvoker` 的委托，交给委托的方法带有一个 `int` 类型的参数，并且返回 `void`。在定义委托的时候必须指定好方法的参数和返回类型，这样才安全嘛。

委托语法类似于方法的定义，但是没有主体，而且要加上关键字 `delegate` 。因为委托基本上算是定义一个新类，所以基本上来说，在能定义类的地方就能定义委托。可以在类的内部定义委托，也可以和类平级。还可以给委托加上修饰符：`public` 、 `private` 、 `protected` 等。

##### 8.2.2 - 使用委托

可以使用委托的“构造函数”向内传递一个方法的名字来创建一个委托：

```cs
private string GetString();     // 声明委托

public static void Main()
{
    var x =40;
    var invokeMethod = new GetString(x.ToString);   // 将方法委托给 invokeMethod

    Console.WriteLine(invokeMethod());              // 执行了 invokeMethod，invokeMethod 又执行了 x.ToString 方法
}
```

在CSharp中，委托在语法上总是接受一个参数类型的构造函数，这个参数就是委托所引用的方法。这个方法必须要匹配定义委托时候的签名。

实际上，使用 `invokeMethod()` 和 `invokeMethod.Invoke()` 完全相同。

为了减少输入量，在需要委托实例的每个位置都可以只传送方法名称，这称为委托推断：

```cs
var invokeMethod = new GetString(x.ToString);
var invokeMethod2 = x.ToString;     // 等价
```

> 注意：不要输入圆括号，仅输入方法名代表了方法的地址，而使用圆括号则是调用了方法并返回一个值。

除了实例一个委托之外，还能实例委托的数组：

```cs
GetString[] methods = new[] 
{
    x.ToString,
    y.ToString,
    // 返回 string 类型并且不接受参数的方法都行
};
```

##### 8.2.4 - Action<T> 和 Func<T>

为每个参数和返回类型都定义一个委托太麻烦了，所以可以使用 `Action<T>` 和 `Func<T>` 委托。

泛型 `Action<T>` 代表引用一个 `void` 返回类型的委托。没有泛型参数的 `Action<T>` 类可以调用没有参数的方法。`Action<in T>` 调用带一个参数（类型为 `T`）的方法，以此类推，`Action<in T1, in T2>` 调用带两个参数的方法。最多有16个参数。

泛型 `Func<T>` 可以引用有返回类型的委托， `Func<out TResult>` 委托类型可以调用带返回类型且无参数的委托。而 `Func<in T, out TResult>` 委托类型可以调用带一个参数的方法，最后一个泛型是返回类型。

##### 8.2.6 - 多播委托

委托也可以包含多个方法，这种委托也可以包含多个方法。这种委托称为多播委托。如果调用多播委托，就可以按照顺序连续调用多个方法。所以，多播委托方法的返回类型必须为 `void` ；否则，只能获取最后一个调用的委托的方法结果。

多播委托重载了运算符 `+` 以及 `+=` ，可以通过 `+` 来为多播委托添加一个方法。当然，也重载了 `-` 和 `-=` ，可以使用 `-` 运算符移除一个方法。

> 注意：对同一个委托，调用其方法链的顺序并未定义，因此，应**避免编写依赖于特定顺序调用的代码**。

多播委托包含一个逐个调用的委托集合。如果通过委托调用其中一个方法抛出了异常，整个迭代就会终止，剩余委托的方法即使是正常的也不会被调用。

为了避免这种问题，应该自己迭代方法列表。 `Delegate` 类定义了 `GetInvocationList` 方法，它返回一个 `Delegate` 对象数组。

##### 8.2.7 - 匿名方法

> 注意：这种匿名方法的语法在CSharp2中引入。在新的程序中，并不需要这个语法，lambda 表达式明显比这个好用。

语法如下，大致看看就得了：

```cs
var mid = ", middle part,";

Func<string, string> annoDel = 
    delegate (string param)
    {
        param += mid;
        param += " and this was added to the string.";
        return param;
    };
// 代码的第四行到第九行是匿名方法。
```

#### 8.3 - lambda 表达式

lambda 运算符 `=>` 左边列出了需要的参数（一个参数可以省略括号），而右边定义了赋予 lambda 变量的方法的实现代码。

##### 8.3.1 - 参数

lambda 表达式有几种定义参数的方法，如果只有一个参数那就写出参数名就行了，编译器根据委托自动推断参数类型，比如：

```cs
Func<string, string> oneParam =  s => 
    $"change uppercase {s.ToUpper()}";
```

有多个参数的时候，将参数放在圆括号中，用逗号分隔。当然，为了方便起见，也可以在参数前加上类型。

##### 8.3.2 - 多行代码

如果 lambda 表达式只有一条语句，则不方法块内不需要花括号和 `return` 语句，因为编译器会添加一条隐式的 `return` 语句。但是如果 lambda 表达式的实现代码中需要多条语句，那么就要添加花括号和 `return` 了。

##### 8.3.3 - 闭包

通过 lambda 表达式可以访问表达式块外部的变量，这成为闭包。闭包是非常好用的功能，但是**使用不当也会造成大灾难**。

> 注意：如果给多个线程使用闭包，就可能遇到并发冲突。最好仅给闭包使用不可变的类型（readonly），这样就可以确保不改变值，也不需要同步。

#### 8.4 - 事件

事件基于委托，为委托提供了一种发布/订阅的机制。比如在桌面程序中：`Button` 出发了 `Click` 事件。这类事件就是委托。触发 `Click` 事件时调用的处理程序方法需要得到定义，而其参数由委托定义。

使用 `event` 关键字来定义事件：

```cs
using System;

namespace Program
{
    // 用于事件传参的参数，继承于 EventArgs
    public class CarInfoEventArgs: EventArgs
    {
        // 构造函数
        public CarInfoEventArgs(string car) => Car = car;

        public string Car { get; set; }
    }

    public class CarDealer
    {
        // 事件，需要在泛型中写出传递给它的参数
        public event EventHandler<CarInfoEventArgs> NewCarInfo;

        public void NewCar(string car)
        {
            Console.WriteLine($"new car {car}");
            // 触发事件，第一个参数是触发者，第二个参数是传递的参数
            NewCarInfo?.Invoke(this, new CarInfoEventArgs(car));
        }
    }
}
```

作为一个约定，事件通常用带两个参数的方法

1. 第一个参数是一个对象，包含事件的发送者。
2. 第二个参数提供了事件的相关信息。

`EventHandler<TEventArgs>` 定义了一个处理程序，它返回 `void` ，接受两个参数，第二个参数类型通过泛型指定，并且必须继承于 `EventArgs` （上面代码中的 `CarInfoEventArgs` 就是）。

另外，事件有自动生成一个私有字段，类似于自动生成的属性访问器吧：

```cs
public event EventHandler<CarInfoEventArgs> NewCarInfo;     // 简记

private EventHandler<CarInfoEventArgs> _newCarInfo2;        // 写全
public event EventHandler<CarInfoEventArgs> NewCarInfo2
{
    add => _newCarInfo += value;
    remove => _newCarInfo -= value;
}
```

`CarDealer` 类通过调用委托的 `Invoke` 方法来触发事件，这与多播委托一样，顺序无法保证的。

##### 8.4.2 - 使劲按侦听器

下面实现的 `Consumer` 类用作事件的侦听器。这个类订阅了 `CarDealer` 类的事件：

```cs
public class Consumer
{
    private string _name;
    // 构造函数
    public Consumer(string name) => _name = name;

    // 事件被触发时调用此方法，这个是被委托的方法
    public void NewCarIsHere(object sender, CarInfoEvent e)
    {
        Console.WriteLine($"{_name}: car {e.car} is new");
    }
}
```

使用 `CarDealer` 类的 `NewCarInfo` 事件（前面定义的那个 `EventHandler` 类型的“变量”），通过 `+=` 运算符创建一个订阅：

```cs
var dealer = new CarDealer();
var consumer = new Consumer("A");

dealer.NewCarInfo += consumer.NewCarIsHere;      // 添加订阅
dealer.NewCar("B");     // 这个方法会触发事件，然后执行委托
```

最后会输出：
> new car B
> 
> A: car B is new
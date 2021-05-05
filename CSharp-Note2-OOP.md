---
title: c#面向对象——对象和类型与继承 - C#笔记 
author: Gaein nidb
categories:
  - 学习资料
tags:
  - 笔记
  - 编程
  - CSharp
  - dotNET
date: 2021-04-27 21:52:51
---

# 目录

* [C#基础](https://blog.gaein.cn/passages/CSharp-Basic/)
* [C#面向对象](https://blog.gaein.cn/passages/CSharp-OOP/)
* [C#泛型](https://blog.gaein.cn/passages/CSharp-Generics-Type/)
* [C#运算符和强制类型转换](https://blog.gaein.cn/passages/CSharp-Note4-Operators-and-TypeCasts/)
* [C#中数组的使用](https://blog.gaein.cn/passages/CSharp-Note5-Array/)
* [C#的委托、lambda 表达式和事件](https://blog.gaein.cn/passages/CSharp-Note6-Delegates-lambda-Expressions-and-Events/)

# C# 面向对象编程

> 系统学习C#笔记，本来也想写在基础部分来着，但是上一篇博客太长了，想想还是拆出来写吧。
> 包括C#的 对象和类、继承 两章


## 3 - 对象和类型

### 3.1 - 创建和使用类

### 3.2 - 类和结构

结构不同于类，结构是值类型而类是引用类型，结构不需要在堆上分配空间，它们通常存储在栈上，另外，结构不支持继承。

较小的数据类型使用结构可以提升性能。

### 3.3 - 类

类可以包含静态成员或者实例成员。静态成员属于类而实例成员属于对象。

#### 3.3.1 - 字段(Field)

字段是与类相关的变量，前面说过，字段通常是私有的。最好不要把它设置为 `public` ，除非你有足够的理由，因为字段应该去由属性来设置，而不是直接暴露给外部。

#### 3.3.2 - 只读字段

带有 `readonly` 修饰符的字段只能在构造函数中分配它的值。它与 `const` 修饰符不同，它不需要在编译的时候就指定值。

#### 3.3.3 - 属性(property)

属性的概念是：

> 它是一个方法或一对方法，在客户端看来，它们是一个字段。

也就是说，在类的内部，属性是设置私有字段的方法。而外部看来，它们是字段，比如：

##### 具有表达式体的树形访问器

```cs
private string _name;

public string Name
{
    get => _name;
    set => _name = value;
}
```

##### 自动实现的属性

当然，上面的代码可以使用自动实现的属性替换为，也可以用属性初始化器来初始化（第二行代码）

```cs
public string Name { get; set; }

public Guid Uid { get; set; } = new Guid();
```

注意看第一段代码的命名规范：本来 Microsoft 那边的规范是使用小写字母命名私有字段，但是 .NET Core 团队转向用下划线加小写字母来命名，这样显然更容易区分。

##### 属性的访问修饰符

C#允许给属性的 `get` 和 `set` 访问器分别设置不同的修饰符。

> 注意：在 `get` 和 `set` 访问器中必须有一个具备属性的访问级别。

##### 只读属性

在属性定义中省略 `set` 访问器，就可以创建只读属性。

##### 表达式体属性

在C#6之后，用于创建只读属性，类似于表达式体方法。

比如说：

```cs
public string FirstName { get; set; }
public string LastName { get; set; }
public string FullName => $"{FirstName} {LastName}"
```
##### 不可变的类型

如果对象没有任何可以改变的成员，只有只读成员，它就是一个不可变的类型。

#### 3.3.4 - 匿名类

`var` 与 `new` 关键字一起使用时，可以创建匿名类型。匿名类型只是一个继承自 `Object` 且没有名称的类。该类的定义从初始化器中推断，类似于隐式类型化的变量，比如：

```cs
var result = new
{
    code = 200,
    message = "OK",
    server = "AspNetCore",
    data = null,
};
```

如果创建另外一个匿名类，但是它们的所有属性都匹配则它们为同一个类型，可以赋值。

#### 3.3.5 - 方法

> 注意：正式的C#术语区分函数和方法。“函数”成员不仅包含方法，也包含类或结构的一些非数据成员，比如索引器、运算符、构造函数和析构函数，甚至还有属性。字段、常量和事件才是数据成员。

##### 方法的声明

说过了

##### 表达式体方法

一行解决问题：

```cs
public static bool hasValue() =>
    data == null;
```

##### 调用方法

通过 `类名.方法名()` 来调用静态方法，静态方法属于类。

对于非静态来说，它们是属于某个对象（特定实例）的，因此需要先将其类实例化，再用 `对象.方法()` 来调用。

##### 方法的重载

C#支持方法重载——方法的几个版本有不同的签名，即同名的方法可以接受不同的参数。

```cs
// method1
string FindName(Guid id) =>
    members.FirstOrDefault().Where(it => id.Id == id)?.Name;

// method2
string FindName(string id)
{
    var guid = Guid.Parse(id);
    return FindName(guid);
}
```

不仅参数类型可以不同，参数个数也可以不同。

##### 命名的参数

任何方法都可以使用命名的参数，只需要写一个变量名在前面，然后就不管顺序传入指定的参数了。

比如：

```cs
public static void TestMethod(int n, int opt1 = 0, int opt2 = 16);

TestMethod(0, opt2: 4);
```

##### 个数可变的参数

声明数组类型的参数（比如 `int` 类型），添加 `params` 关键字，就可以使用任意数量的 `int` 类型的参数调用该方法。

```cs
public static void PrintEach(params int[] inputs)
{
    foreach(var item in inputs)
    {
        Console.WriteLine(item);
    }
}
```

调用该方法的时候可以使用任意数量个 `int` 类型的参数哦，比如：

```cs
PrintEach(0);
PrintEach(1, 1, 4, 5, 1, 4);
```

#### 3.3.6 - 构造函数(constructor/ctor)

声明一个与类同名的方法，但是不需要写返回类型（连 `void` 都不写），比如：

```cs
public class UsersServices
{
    private readonly IOptions _options;
    private readonly IUsersRepo _userRepo;

    // constructor
    public UsersServices(IOptions options, IUsersRepo usersRepo)
    {
        _options = options ??
            throw new ArgumentNullException(nameof(options));
        _usersRepo = usersRepo ??
            throw new ArgumentNullException(nameof(usersRepo));
    }
}
```

如果不写构造函数的话，编译器会默认生成一个啥也没有的构造函数。如果写了带参数的构造函数则不会默认生成默认的（没有参数什么也不做）构造函数。构造函数可以重载。

可以包含 `static` 修饰符的情况：

* 类仅用于某些静态成员或属性的容器，因此永远不会实例化它。
* 希望类仅通过某个静态成员函数来实例化

##### 表达式体和构造函数

如果CTOR由一个表达式构成，就直接用表达式体实现：

```cs
MyClass(IOptions option) => _option = option;
```

##### 从构造函数中调用其他构造函数

可以调用其它的构造函数，复用代码。C#有一个特殊的语法，成为构造函数初始化器，可以实现此目的：

```cs
class Car
{
    public Car(string description): this(description, 5)
    {

    }

    public Car(string description, int num)
    {
        // codes...
    }
}
```

也可以包含对基类的构造函数的调用（语法和上面大致相同，使用 `base` 代替 `this`）

##### 静态构造函数

给类编写无参数的静态构造函数。这种构造函数只执行一次。

### 3.4 - 结构

有时仅需要一个小的数据结构。此时，类提供的功能多于我们需要的功能，由于性能原因，最好使用结构。

结构相比于类来说：

* 不支持继承
* 如果没有默认的构造函数则所有值被初始化为默认值
* 可以指定字段如何在内存中布局
  

因为结构实际上是把数据项组合在一起，所以有时候大多数或者全部字段都声明为 `public`

#### 3.4.1 - 结构是值类型

对于结构来说，`new` 只是调用构造函数，初始化所有字段。

结构遵循其它数据类型都遵循的规则：在使用所有元素都必须进行初始化，调用 `new` 关键字就全部初始化了。

但当把结构作为参数传递给方法的时候应该把它作为 `ref` 参数，以免性能损失。

#### 3.4.2 - 只读结构

使用C#7.2时， `readonly` 修饰符可以应用于结构，因此编译器保证结构体的不变性。

#### 3.4.3 - 结构和继承

**结构不是为继承设计的**

> 要比较结构值，最好实现结构 `IEquatable<T>` 。

#### 3.4.4 - 结构的构造函数

默认构造函数把数值字段都初始化为 0，且总是隐式的给出。不能为结构创建定制的默认CTOR。

#### 3.4.5 - ref 结构

使用 `ref` 关键字将结构体分配到堆上。

### 3.5 - 按值和按引用传递参数

> 为了避免在更改成员时类和结构之间的不同行为上出现这种混淆，最好将结构设置为不可变的。

#### 3.5.1 - ref 参数

在定义和调用方法的参数前加上 `ref` 修饰符，使用引用。

#### 3.5.2 - out 参数

处理类型错误的数据，可以使用 `TryParse` 方法。无论解析成功与否，都返回一个 `bool` 类型，解析成功的结果使用 `out` 返回到另外一个参数。

调用此方法时，变量不需要预先初始化，而是在方法中初始化变量，在调用方法的时候需要提供 `out` 修饰符，比如：

```cs
if (int.TryParse(inputStr, out var result))
{
    Console.WriteLine("SUCCESS");
}
```

#### 3.5.3- in 参数

`in` 参数保证发送到方法中的数据不会更改，它将参数设置为只读变量。相比于只使用 `ref` 来说，显然使用 `in` 修饰符更安全一些。

使用值类型和 `in` 修饰符，不仅有助于确保不更改内存，编译器还可以创建更好的优化代码。

### 3.6 - 可空类型

可空类型是可以为空( `null` )的值类型，用于解决从数据库映射到代码或者类似这种值类型的可能为空的情况。

可空类型只需要在类型的后面添加“?”。

值类型隐式转换为可空类型，可空类型需要强制转换为值类型，如果强制转换的过程中可空类型变量为 `null` 则会引发一个异常。更好的解决方案是使用可空类型的 `HasValue` 和 `Value` 属性。

可以使用：

```cs
int idInt = id.HasValue ? id.Value : -1;
```

当然，使用合并运算符更好：

```cs
int idInt = id ?? -1;
```

### 3.7 - 枚举类型

枚举是一个值类型，包含一组命名的常量。

默认情况下，枚举的类型是 `int` ，也可以通过 `:[type]` 指定为其它整数类型，比如：

```cs
public enum Color : short
{

}
```

还可以使用枚举类型把多个选项分配给一个变量，分配给常量的值必须是不同的位（使用位与来把多个枚举常量赋值给变量），并且加上 `Flags` 属性 ，比如：

```cs
[Flags]
public enum DaysOfWeek
{
    Monday =    0b_0000_0000_0000_0001,
    Tuesday =   0b_0000_0000_0000_0010,
    Wednesday = 0b_0000_0000_00000_0100,
    Thursday =  0b_0000_0000_0000_1000,
    Friday =    0b_0000_0000_0001_0000,
    Saturday =  0b_0000_0000_0010_0000,
    Sunday =    0b_0000_0000_0100_0000,
    Weekend = Saturday | Sunday,
    Workday =   0b_0000_0000_0001_1111,
    AllWeek = Workday | Weekend
}
```

枚举还提供了方法来解析字符串：

```cs
if (Enum.TryParse<Color>("Red", out var red))
{

}
```

`Enum.GetNames` 方法返回一个包含所有枚举名的字符串数组。

```cs
foreach (var day in Enum.GetNames(typeof(Color)))
{


}
```

为了获取枚举的值，可以使用 `Enum.GetValues`，返回枚举值的一个数组。

### 3.8 - 部分类

`partial` 关键字允许把类、结构、方法或接口放在多个文件中。

> 注意：尽管 `partial` 关键字很容易创建跨文件的庞大的类，但是它并不应该这么用，如果类过大还是应该拆分的。

部分类中可以用 `partial` 关键字声明一个方法，然后在其它文件中实现（实现不需要加这个关键字），比如：

```cs
public partial void PartMethod();
```

在另一个文件中：

```cs
public void PartMethod()
{
    // DO SOME THING
}
```

**部分方法必须是 `void` 类型，否则编译器在没有实现代码的情况下不能将其删除。**

### 3.9 - 扩展方法

扩展方法是给对象添加功能的另一个选项，在不能使用继承时，也可能使用这个选项（例如类是密封的）。

扩展方法的定义在参数表里是这样的： `(this [类型] [标识符])` 也就是普通方法的参数表类型前面加上 `this`。会作为第一个参数类型的扩展方法。

```cs
public static class StringExt
{
    public static int GetWordCount(this string s) => s.Split().Length;
}
```

### 3.10 - Object 类

所有的 `.NET` 类都继承于 `System.Object` 。`Object` 类提供了一些方法：

| 方法               | 作用                             |
| ------------------ | -------------------------------- |
| `ToString()`       | 对象的字符串表示，可以重写       |
| `GetHashCode()`    | 在散列中的对象可以使用这个方法   |
| `Equals()`         | 比较对象的相等性，有重载可以重写 |
| `Finalize()`       | 析构函数，引用被GC的时候调用     |
| `GetType()`        | 返回一个实例提供对象所属类的信息 |
| `MemeberwiseClone` | 浅表复制对象并返回副本的引用     |

## 4 - 继承

### 4.1 - 面向对象

面向对象的三个最重要的概念是继承、封装和多态（老生常谈了）

### 4.2 - 继承的类型

* 单重继承： 一个类派可以生自一个基类，C#采用这种继承
* 多重继承： 一个类派生自多个基类，C++用，C#不允许这种继承，但是可以继承自多个接口
* 多层继承： 一个基类的派生类可以继续被继承，C#允许这种继承
* 接口继承： 定义了接口的继承，C#允许并且允许多重接口继承

#### 4.2.1 - 多重继承

C++等支持多重继承，但是会增加代码复杂度以及额外开销，（并且我觉得不是很优雅）。所以C#不支持这种继承。但是同时，C#允许继承自多个接口。

#### 4.2.2 - 结构和类

前面说过，结构不是为继承而设计的，但是

* 结构总是派生自 `System.ValueType` ，它们还可以派生自多个接口。
* 类总是派生自 `System.Object` 或者用户选择的另一个类，它们还可以派生自多个接口。

### 4.3 - 实现继承

声明类派生自另一个类（比如类 `A` 派生自 `B`），使用以下的语法：

```cs
class A : B
{

}
```

如果类也派生自接口，则用逗号分隔多个接口。

> 注意：如果派生类同时派生自类和多个接口，类放在最前面。

#### 4.3.1 - 虚方法

把一个基类方法声明为 `virtual` ，就可以在任何派生类中重写(override)该方法：

```cs
public class Shape
{
    public virtual void Draw() =>
        Console.WriteLine($"Shape with {Position} and {Size}");
}
```

也可以把属性声明为 `virtual` 。对于虚属性或重写属性，语法和非虚属性相同，但要添加关键字 `virtual`。

C#中的虚函数概念与标准的 OOP 的概念相同：可以在派生类中重写虚函数。在调用方法的时候会调用该类对象的合适方法。不同于Jvav，在C#中函数在默认情况下不是虚拟的，但可以显示的声明为 virtual。

C#要求在派生类中重写另一个函数时，使用 `override`关键字显示声明：

```cs
public class Rectangle : Shape
{
    public override void Draw() =>
        Console.WriteLine($"Rectangle with {Position} and {Size}");
}
```

可以重写 `Object` 类的某些方法，比如 `ToString` 和 `Equals`。

> 注意：成员字段和静态函数都不能声明为 `virtual` ，因为这个概念只对类中的实例函数有效。

#### 4.3.2 - 多态性

拿上面的例子来说，尽管方法声明为接收一个 `Shape` 对象，但是任何派生自 `Shape` 的类型都可以传递给这个方法。因为派生自 `Shape` 的类拥有 `Shape` 的所有内容。

#### 4.3.3 - 隐藏方法

如果签名相同的方法在基类和派生类中都进行了声明，但该方法没有分别声明为 `virtual` 和 `override` ，派生类方法就会隐藏于基类方法。

> 注意：new 方法修饰符 **不应该用于** 故意隐藏基类的成员。它的主要目的是处理版本冲突。

#### 4.3.4 - 调用方法的基类版本

为了添加派生类的功能，可以使用 `base` 关键字调用基类方法，语法为 `base.<MethodName>`

#### 4.3.5 - 抽象类和抽象方法

C#允许把类声明为 `abstract` 。抽象类不能实例化，而抽象方法不能直接实现，必须在非抽象的派生类中重写。

#### 4.3.6 - 密封类和密封方法

如果不应创建派生自某个自定义类的类，该自定义类就该密封。给类添加 `sealed` 修饰符，就不允许创建该类的子类。



#### 4.3.7 - 派生类的构造函数

CTOR 总是按照层次结构的顺序调用：先调用 `System.Object` 类的构造函数，再按照层次结构由上向下运行，直到到达编译器要实例化的类为止。

把初始化代码放在 CTOR 代码块内太迟了，所以采用 `base` 关键字传参：

```cs
public class Student : Human
{
    public Student(Sex sex) : base(sex)
    {

    }
}
```

### 4.4 - 修饰符

#### 4.4.1 - 访问修饰符

| 修饰符               | 应用于                   | 说明                         |
| -------------------- | ------------------------ | ---------------------------- |
| `public`             | 所有的类或成员           | 任何代码都可访问             |
| `protected`          | 类型和内嵌类型的所有成员 | 只有派生的类能够访问         |
| `internal`           | 所有的类或成员           | 只有在包含它的程序集才能访问 |
| `private`            | 类型和内嵌类型的所有成员 | 只能在它所属的类中访问       |
| `protected internal` | 同上                     | `protected` OR `internal`    |
| `private protected`  | 同上                     | `private` AND `protected`    |

> 注意： `public`、`protected` 和 `private` 是逻辑访问修饰符，而 `internal` 是“物理”的，它表示程序集的边界。

#### 4.4.2 - 其他修饰符

|修饰符|应用于| 说明 |
| `new`|函数成员|隐藏继承的成员|
|`static`|所有成员|静态|
|`virtual`|仅成员函数|虚函数|
|`abstract`|同上|定义签名，没有实现|
|`override`|同上|重写|
|`sealed`|类、方法和属性|封闭，不可被继承|


### 4.5 - 接口

声明接口在语法上与声明类完全相同，只是 `class` 换成了 `interface`，但是接口中不允许提供任何成员的实现。

因为接口的成员总是抽象的，所以接口不需要 `abstract` 修饰符。

#### 4.5.1 - 定义和实现接口

接口名称通常以 `I` 开头，以便知道这是一个接口。

让想实现接口的类继承于接口，然后去实现接口里面定义的成员即可。比如说 `AccountService` 派生于 `IAccountService` ，表示它获得了 `IAccountService` 中的所有成员，但接口实际上并不实现其方法，所以 `AccountService` 中必须提供其实现。如果缺少代码编译器将产生一个错误。

#### 4.5.1 - 派生的接口

接口可以彼此继承，其方式与类的继承相同。

### 4.6 is 和 as 运算符

两个与继承有关的重要运算符：`is` 和 `as`

在从基类的对象转为派生类的对象的时候不应该直接进行强制转换，而应该先检查，使用 `as` 运算符而不是强制转换 `(<type>)`：

```cs
public void Login(object service)
{
    IAccountService = service as IAccountService;
}
```

`as` 运算符会检查要进项转换的对象是否符合条件（实现了接口），然后再进行转换，如果不符合不会抛出异常而是返回一个 `null`。

`is` 运算符根据条件是否满足，返回一个 `bool` 值，如果条件为 `true` ，则将所得的对象写入后面声明的变量中：

```cs
if (o is AccountService service)
{
    // service is AccountService.
}
```

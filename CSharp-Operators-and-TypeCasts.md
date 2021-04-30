# 目录

* [C#基础](https://blog.gaein.cn/passages/CSharp-Basic/)
* [C#面向对象](https://blog.gaein.cn/passages/CSharp-OOP/)
* [C#泛型](https://blog.gaein.cn/passages/CSharp-Generics-Type/)

# 运算符和类型强制转换

## 6 - 运算符和类型强制转换

### 6.1 - 运算符和类型转换

### 6.2 - 运算符

C#的运算符类似于C++和Java的运算符，但有一些区别。

除了常见的算数运算符、逻辑运算符、比较运算符和赋值运算符等，C#还有

* 类型信息运算符 `sizeof` 、 `is` 、 `typeof` 、 `as`
* 溢出异常控制运算符 `checked` 、 `unchecked`
* 标识符的名称运算符 `nameof()`
* 空合并运算符 `??`
* 空值条件运算符 `?.` 、 `?[]`

#### 6.2.1 - 运算符的简化操作

自增(`++`)、自减(`--`)以及一些合并赋值(+=)运算符。

当自增/自减运算符用于较长的表达式内部时，把运算符放在前面(`++x`)会在计算表达式之前递增 `x` 。换而言之，在增加了 `x` 的值后再带入表达式计算。而把运算符放在后面(`x++`)则反之。

看个例子就行：

```cs
var x = 0;
Console.WriteLine(++x);     // The output is "1"

x = 0;
Console.WriteLine(x++);     // The output is "0"
Console.WriteLine(x);       // The output is "1"
```

看这三行输出。第一行是先把 `x` 进行了自增，在 `x` 传入 `WriteLine` 方法前就是 `1` 了。第二行代码是先将 `x` 的值 `0` 传给 `WriteLine` 方法，再将 `x` 的值自增为 `1` 。因此第三行代码输出为 `1`。

##### 条件运算符

**适当**的使用条件运算符可以使程序更简洁，比如：

```cs

/// <summary>
/// 获取数字为奇数或偶数
/// </summary>
/// <param name="input">数字</param>
/// <returns>字符串奇数或偶数</returns>
public static string NumType(int input) =>
    input % 2 == 0 ? "偶数" : "奇数";

// 不使用条件运算符的版本
public static string NumType(int input)
{
    if (input % 2 == 0) return "偶数";
    return "奇数";
}
```

使用条件运算符明显短了很多。

##### `checked` 和 `unchecked` 运算符

如果把一个代码块标记为 `checked` ，CLR就会执行溢出检查，如果发生了溢出就抛出 `OverflowException` 异常。

> 注意：unchecked 是默认行为。只有在 checked 标记的代码块里把几行不去检查的代码进行标记才使用。

> checked 有性能损耗

##### `is` 运算符

`is` 运算符可以检查对象是否与特定的类型兼容。短语“兼容”表示对象或者是该类型或者是派生自该类型，总之，可以安全的正常转换为该类型。

C#7 扩展了具有类型匹配的 `is` 运算符，可以在类型的右边声明变量。如果 `is` 运算符返回 `true` ，则该变量被赋值为转换后的引用。

```cs
public void PeopleReg(object o)
{
    if (o is Person p)
    {
        // convert success
        Console.WriteLine($"Welcome {p.Name}");
    }
    else
    {
        Console.WriteLine("You are not a human!");
    }
}
```

##### `as` 运算符

`as` 运算符用于执行引用类型的强制转换。如果要转换的类型与指定的类型兼容，转换就会成功，否则赋值为 `null` 。

`as` 运算符允许在一步中进行安全的类型转换，不需要先使用 `is` 运算符测试类型，再执行转换。

##### `sizeof` 运算符

使用 `sizeof` 运算符可以确定栈中值类型需要的长度（字节）。如果结构体只包含值类型，也可以使用 `sizeof` 运算符和结构。

如果对复杂类型（而非基本类型）使用 `sizeof` 运算符，就要把代码放在 `unsafe` 块中。

##### `typeof` 运算符

`typeof` 运算符返回一个表示特定类型的 `System.Type` 对象。例如 `typeof(string)` 返回表示 `System.String` 类的对象。

> Tip：在使用反射技术动态的查找对象的相关信息时，这个运算符很有用。

##### `nameof` 运算符

接受一个符号、属性或方法，并返回其名称。比如：

```cs
public void LikeArticle(Guid aid)
{
    if (aid == null)
    {
        throw new ArgumentNullException(nameof(aid));
    }
}
```

还可以使用这个运算符得到属性的名称，也可以得到方法的名称。

##### 索引运算符

类似于访问数组元素，索引运算符可以是任何类型（比如字典中使用的索引运算符）：

```cs
var title = dict["title"];
```

##### 可空类型和运算符

引用类型可以为空，每次都用 `if` 来判断空太麻烦了，所以有了可空类型和运算符。

可空的值类型使用 `?` 来定义，比如：

```cs
int? num = 6;
num = null;
```

或者

```cs
/// <summary>
/// 获取文章列表
/// </summary>
/// <param name="limit">限制数</param>
/// <returns>HTTP 200</returns>
[HttpGet]
public async Task<ActionResult<ICollection<ArticleListDto>>> GetArticleList([FromQuery] int? limit)
{

}
```

> 注意：通常可空类型与一元或二元运算符一起使用时，如果其中一个操作数或两个操作数都是 `null` 则结果也为 `null` 。

> 在比较可空类型时，有一个为 `null` 则结果就是 `false` 。

##### 空合并运算符

空合并运算符(`??`)提供了一种快捷的方式：如果运算符前的表达式不为 `null` 则返回前面的表达式，否则返回后面的：

```cs
public void Test(int? num)
{
    var numInt = num ?? -1;
    var input = Console.ReadLine();
    var path = input ?? "C:/";
}
```

##### 空值条件运算符

下面这段代码验证传递的参数 `p` 是否非空。如果它为空，方法就只是返回，而不会继续执行：

```cs
public void ShowPerson(Person p)
{
    if (p==null) return;

    var firstName = p.FirstName;
    // ...
}
```

（你看它多麻烦）

使用空值条件运算符 `?.` 访问 `FirstName` ，当 `p` 为空的时候就只返回 `null` ，而不继续执行表达式。

```cs
public void ShowPerson(Person p)
{
    var firstName = p?.FirstName;
}
```

还可以把空值运算符用于数组：

```cs
int[] arr = null;

var val = arr?[0];

// 结合 ?? 运算符给它一个默认值：

var valWithDefault = arr?[0] ?? -1;
```

#### 5.2.2 - 运算符的优先级和关联性

基本运算符 > 一元运算符 > 乘除 > 加减 > 移位运算 > 关系运算 > 比较运算 > AND > XOR > OR > 逻辑与 > 逻辑或 > 空合并 > 三元条件运算符 > 赋值与lambda

关联性的例外是赋值运算符，它们是右关联。

一个重要的、可能误导右关联是三元条件运算符：

```cs
a ? b : c ? d : e;
// 等价
a ? b : (c ? d : e);
```

> 注意：在复杂的表达式中应避免运算符优先级来生成正确的结果。优先级一般符合正常的认知，当不利于人类理解时应该使用括号指定优先级。避免潜在的问题。

### 6.3 - 使用二进制运算符

`Convert.ToString` 提供的一个重载带有两个 `int` 参数，其中第二个 `int` 值是 `toBase` 参数。使用这个方法可以传递值：

* 2 - 二进制
* 8 - 八进制
* 10 - 十进制
* 16 - 十六进制

来格式化字符串。

默认情况下，如果二进制以 `0` 开头，则这些 `0` 将会被忽略，而不会被打印出来。使用 `PadLeft` 方法来填充字符串中的这些 `0` 值。

1. 位与 AND：该位全是1的时候结果为1，否则为0，比如：

   A: 1001_0011_1001_1111  
   B: 1100_0101_0011_1011  
   R: 1000_0001_0001_1011

2. 位或 OR : 该位上任意操作数为1的时候结果为1，比如：

   A: 1001_0011_1001_1111  
   B: 1100_0101_0011_1011  
   R: 1101_0111_1011_1111

3. 位异或XOR: 该位上只有一操作数位1的时候结果为1，比如：

   A: 1001_0011_1001_1111  
   B: 1100_0101_0011_1011  
   R: 0101_0110_1010_0100
4. 位取反NOT:按位取反，比如：

   A: 1001_0011_1001_1111  
   R: 0110_1100_0110_0000

#### 6.3.1 - 位的移动

向左移动 n 位等同于原来的数字乘以 2^n ，这比乘法运算符要快得多哦。

#### 6.3.2 - 有符号数和无符号数

使用二进制时要记住的意见重要的事情是，有符号类型的数字最左边一位表示符号。

### 6.4 - 类型的安全性

#### 6.4.1 - 类型转换

##### 隐式转换

只要保证值不会发生任何变化，类型转换就可以自动进行。即：可以从“小范围”转到“大范围”的意思。

> Tips：`BigInteger` 是包含任意大小的数字的结构体。可以从较小的类型中初始化它，传递一个大数字或者解析字符串。

> 注意：
> 1. 只能从较小的整数类型隐式的转换为较大的整数类型，反过来不行。
> 2. 可以将整数隐式的转换为浮点数，尽管可能会丢失精度。
> 3. 可空类型不能隐式的转换为不可空类型。

##### 显示转换

强制类型转换，使用 `(type)identifer` 的语法来转换。强制类型转换可能是不安全的，可能会引发溢出或者异常。

C#提供了 `checked` 运算符，可以检查是否有算术溢出。当然它也检查了是否安全，如果不安全则会强制抛出一个异常。

> Tips：谨慎的使用显示的类型强制转换，就可以把简单值类型的任何实例转换为几乎任何其它类型。

如果需要在数字和字符串之间转换可以使用 `.NET` 类库提供的一些方法。

数字 -> 字符串：

```cs
var num = 8;
var str = num.ToString();
```

字符串 -> 数字：

```cs
var str = "8";
var num = 0;
if (int.TryParse(str, out num))
{
    // Successfully convert string to int.
    
    Console.WriteLine(num);     // 8
}
```

如果使用了 `Parse` 方法，将会返回转换后的值，但是如果不能转换该方法就会抛出一个异常。

#### 6.4.2 - 装箱和拆箱

前面说过了：

* 装箱：值类型   -> 引用类型，栈 -> 堆
* 拆箱：引用类型 -> 值类型，堆 -> 栈

装箱可以隐式的进行。

> Tips: 拆箱时必须非常小心，确保能拆...否则可能会引发一个异常

### 6.5 - 比较对象的相等性

#### 1. `ReferenceEquals` 方法

`ReferenceEquals` 是一个静态方法，其测试两个引用是否指向类的同一个实例，特别是两个引用是否包括内存中相同的地址。不能重写。

但是，它认为 `null` 等于 `null`。

> Tips：这个方法如果用于比较值类型则总返回 `false` 。

#### 2. `Equals` 虚方法

`Equals` 虚版本的也可以实现比较， `System.Object` 中实现的版本是比较引用。因为这是虚方法所以可以在自己的类中重写它，使用适合自己类的方法进行比较，比如值比较。

> 如果希望类的实例用作字典的键，就需要重写这个方法。

#### 3. `Equals` 静态方法

作用相同，不同的是静态方法有两个参数，它的作用是比较这两个参数（引用）的相等性。如果有一个参数为 `null` 则返回 `false` 。

#### 4. `==` 比较运算符

最好将它看作严格的值比较和严格的引用比较中间的玩意，大多数情况下，如果两边是引用类型的话是引用比较。

> 不过字符串类型通过运算符重载实现了通过比较运算符进行值比较，更符合人的直觉。

在使用上述几种方法的时候，通常来说使用 `==`，需要比较引用的时候使用 `ReferenceEquals` 方法。需要复杂类型的自定义比较时，重写 `Equals` 方法，并且，建议使用这个方法重载运算符 `==`，以便于更方便的比较。

#### 6.5.2 - 比较值类型的相等性

在比较值类型的相等性时，采用与引用类型相同的规则：`ReferenceEquals` 用于比较引用，`Equals` 用于比较值，比较运算符可以看作一个中间项。

如果调用 `sA.Equals(sB)` ，其中 `sA` 和 `sB` 是某个结构的实例，则根据它俩是否存在所有的字段并且包含相同的值返回 `true` 或者 `false`。

另一方面，在默认情况下，使用 `==` 比较结构会无法编译，除非自己重载了比较运算符。

尽管 `System.ValueType` 提供的 `Equals` 默认重写版本肯定足以应付大部分自定义的结构，但是仍然可以重写方法以适用于自己的结构、提高性能。另外，如果值类型包含作为字段的引用类型，就需要重写 `Equals` 方法，默认的版本仅比较它们的地址。

### 6.6 - 运算符重载

假如有个 `Matrix` 类，实现了矩阵相加、相乘的一些方法。但是直接调用看着太不舒服了，使用更利于理解的运算符才是该干的。

通过运算符重载可以告诉编译器，`+` 和 `*` 分别对 `Matrix` 对象执行什么操作，以便编写类似于上面的代码。

事实证明，在许多情况下，重载运算符利于生成可读性更高、更直观的代码。

#### 6.6.1 - 运算符的工作方式

* 编译器会看前后的操作数的类型，如果是相同的基础类型，则按照规定的操作进行。
* 如果是不同的，则看是否能够隐式转换并选择合适的路径进行转换。
* 如果是用户自定义的类型，则去查找重载，找到就走重载，找不到就报错。

#### 6.6.2 - 运算符重载的示例：Vector 结构

`Vector` 结构表示一个三维的数学矢量。为了简单，就算它是三个 `double` 类型数字的集合。

```cs
public struct Vector
{
    public Vector(double x, double y, double z)
    {
        X = x;
        Y = y;
        Z = z;
    }

    public Vector(Vector v)
    {
        X = v.X;
        Y = v.Y;
        Z = v.Z;
    }

    public double X { get; set; }
    public double Y { get; set; }
    public double Z { get; set; }

    public override string ToString() => $"( {X}, {Y}, {Z} )";
}
```

运算符重载的声明方式与静态方法基本相同，但 `operator` 关键字告诉编译器这是运算符重载，它后面紧跟一个运算符，比如 `+` ：
```cs
public static Vector operator +(Vector left, Vector right) =>
    new Vector(left.X + right.X, left.Y + right.Y, left.Z + right.Z);
```

> 注意：C#要求所有的运算符重载都声明为 `public` 和 `static` ，这表示它们与类或结构相关联而不是某个特定的实例。所以运算符重载的代码体不能访问非静态类成员，也不能访问 `this` 标识符。

有一个运算符重载，其参数依次是一个 `double` 和一个 `Vector` ，但是编译器不能交换参数的顺序，所以对于这种交换顺序不影响的运算，需要写两个重载。

虽然 `+=` 一般算作一个运算符，但实际上它对应的操作分为两步：相加和赋值。但是C#是不允许重载 `=` 运算符的，不过如果重载了 `+` 运算符，编译器会自动重载 `+=` 运算符。

#### 6.6.3 - 比较运算符的重载

6个比较运算符分为三对：

1. `==` 和 `!=`
2. `>` 和 `<`
3. `>=` 和 `<=`

C#语言要求成对重载比较运算符，也就是你重载了 `>=` 也必须重载 `<=` ，否则就会产生编译器错误。

> Tips：当比较时，需要考虑：如果有嵌入的类，是比较嵌入的类的引用（浅度比较）还是应该比较对象的值是否相等（深度比较）？

> 注意：不要通过调用从 `System.Object` 中继承的 `Equals` 方法的实例版本来重载比较运算符，如果这么做在双等号左值为 `null` 时（例如 `objA == objB`）系统会去调用 `null.Equals(objB)` ，这样会引发一个异常。采用其他方法（重写 `Equals` 方法以比较）比较安全。

还需要重写 `Equals` 方法和 `GetHashCode` 方法。这些方法总是应该在重载 `==` 进行重写，否则编译器会报错：

```cs
public override bool Equals(object obj) =>
    obj is Vector v ? this == v : false;

public override int GetHashCode() =>
    X.GetHashCode() ^ (Y.GetHashCode() ^ Z.GetHashCode());
```

散列代码的实现应比较快速，且总对相同的对象返回相同的值，因此对几个属性算散列是比较的河里的。

#### 6.6.4 - 可以重载的运算符

不同于C++，并不是所有的运算符可以重载。

可以重载的运算符有：

* 算术一元、二元运算符
* 按位二元运算符
* 按位一元运算符：`true` 和 `false` 运算符必须成对重载
* 比较运算符：必须成对重载
* 赋值运算符：不能显示重载，重载单个运算符就重载了
* 索引运算符：不能直接重载索引运算符，索引器成员类型允许类和结构上支持
* 类型强制转换运算符：不能直接重载，可以用用户定义的类型强制转换

### 6.7 - 实现自定义的索引运算符

自定义索引器不能使用运算符重载语法来实现，但是它们可以用与属性非常相似的语法来实现。

举个 `PersonCollection` 类的栗子：

```cs
public class PersonCollection
{
    private Person[] _people;
    
    // ctor
    public PersonCollection(params Person[] people) =>
        _people = people.ToArray();

    // indexer
    public Person this[int index]
    {
        get => _people[index];
        set => _people[index] = value;
    }
}
```

为了允许索引器语法访问 `PersonCollection` 并返回 `Person` 对象，可以创建一个索引器。索引器看起来非常类似于属性。不同之处是名称，指定索引器必须要使用 `this` 关键字，中括号中是索引的类型。

对于索引器，不仅能用 `int` 类型进行索引，任何类型都是有效的。

### 6.8 用户定义的类型强制转换

C#允许定义用户自己的数据类型，因此，需要去支持这些类型的转换。如果知道无论在源变量中存储什么值，类型强制转换总是安全的，就可以把它定义为隐式强制转换。然而，如果某些数值可能会出错，如丢失什么数据或抛出异常，就应该把数据类型转换定义为显示强制转换。

* 隐式转换关键字：`implicit`
* 显示转换关键字：`explicit`

与其他运算符重载一样，类型强制必须同时声明为 `public` 和 `static` 。

####  6.8.1 - 实现用户定义的类型强制转换

定义隐式的强制类型转换使用 `implicit` 关键字，假如我们要转 `Currency` 类为 `float` ：

```cs
public static implicit operate float (Currency value) =>
    value.Dollars + (value.Cents / 100.0f);
```

把 `float` 转换成 `Currency` 不总是成功的，应该定义一个强制转换：

```cs
public static explicit operate Currency (float value)
{
    uint dollars = (uint)value;
    ushort cents = (ushort)((value - dollars) * 100);
    return new Currency(dollars, cents);
}
```

> Tips：如果类型强制转换用于把 `float` 值转换为 `int` ，计算机会截取多余的数字，而不是执行四舍五入。

`System.Convert` 对象包含大量的静态方法来完成各种数字转换，在使用 `System.Covert` 类的方法时会造成额外的性能损失，所以只应在需要的时候使用它们。

`System.Convert` 类的方法还执行它们自己的溢出检查，因此，不需要把对它们的调用放在 `checked` 环境下面。

##### 类之间的类型强制转换
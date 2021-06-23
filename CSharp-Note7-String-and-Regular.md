## 字符串和正则表达式

### 9 - 字符串和正则表达式

#### 9.1 - System.String 类

在CSharp中，`string` 关键字的映射实际上指向 `.NET` 基类 `System.String` 。 `System.String` 是一个功能非常强大且用途广泛的基类。

使用运算符重载可以连接字符串：

```cs
var str = "Hello";
var str1 = "World";
var msg = str + str1;
Console.WriteLine(msg);     // HelloWorld
```

CSharp还允许使用索引器来提取指定的字符串：

```cs
var msg = "HelloWorld";
Console.WriteLine(msg[4]);      // o
```

`System.String` 类常见的方法有：

* `Compare`：比较字符串的内容，考虑区域原因（比如不同区域的日期格式、货币等）。
* `CompareOrdinal`：同上，不考虑区域。
* `Concat`：把多个字符串实例合并为一个实例。
* `CopyTo`：把从选定下标开始的特定数量的字符复制到一个新实例中。
* `Format`：格式化包含各种值的字符串和如何格式化的说明符。
* `IndexOf`：字符串中第一次出现某个给定子字符或字符的为止。
* `IndexOfAny`：字符串中第一次出现某个字符或一组字符的位置。
* `Insert`：把一个字符串实例插入到另外一个字符串实例指定的索引处。
* `Join`：合并字符串数组，创建一个新的字符串。
* `LastIndexOf`：与 `IndexOf` 一样，从后往前查找。
* `LastIndexOfAny`：与 `IndexOfAny` 一样，从后往前找。
* `PadLeft`：在字符串的左侧，通过添加指定的重复字符来填充字符串。
* `PadRight`：同上，在字符串右侧。
* `Replace`：用另一个字符或子字符串替换字符串中给定的字符或子字符串。
* `Split`：用给定字符作为分隔符分隔字符串。
* `SubString`：从字符串中获取指定位置的子字符串。
* `ToLower`：转换成小写。
* `ToUpper`：转换成大写。
* `Trim`：删除首尾的空白。

##### 9.1.1 - 构建字符串

`Spring` 类存在一个问题：重复修改给定的字符串，效率会很低，它实际上是一个不可变的数据类型，表面上修改字符串内容的方法和运算符实际上是创建了一个新字符串并将内容复制过去。

`StringBuilder` 类不像 `String` 类那样支持非常多的方法。它仅限于替换和追加或删除字符串中的文本。但是它的工作方式非常高效。

通常情况下，`StringBuilder` 类分配的内存比它需要的多，在默认情况下就根据初始化实例时的字符串长度来确定所用内存的大小。这个类有两个主要的属性：

* `Length`：字符串的实际长度；
* `Capacity`：在内存中分配的最大长度；

对字符串的修改就在赋予 `StringBuilder` 实例的内存中进行，这就大大提高了追加或替换单个字符的效率，插入和删除子字符（串）的效率依然不高，因为要移动后面的字符串。

> 最好把容量设置为字符串可能的最大长度，确保 `StringBuilder` 类不需要重新分配内存。

一般而言，使用 `StringBuilder` 类执行字符串的任何操作，而 `String` 类用于存储字符串或显示最后结果。

##### 9.1.2 - 字符串插值

使用 `$` 前缀创建字符串，在花括号中可以包含占位符来引用代码，比如：

```cs
var name = "Jack";
var msg = $"Hello { name }, Welcome!";
Console.WriteLine(msg);     // Hello Jack, Welcome!
```

注意，这只是个语法糖，编译器会创建 `String.Format` 方法的调用。
`StringFormat` 方法的第一个参数接受一个格式字符串，其中的占位符从0开始，比如：

```cs
var name = "Jack";
var msg = String.Format("Hello { 0 }, Welcome!", name);
Console.WriteLine(msg);  
```

###### FormattableString

把字符串赋予 `FormattableString` ，就很容易得到翻译过来的插值字符串。

```cs
var x = 3, y = 4;
FormattableString s = $"The result of { x } and { y } is { x + y }";
Console.WriteLine($"Format: { s.Format } ");
foreach(var i = 0; i < s.ArgumentCount; ++i)
{
    Console.WriteLine($"Argument { i }: { s.GetArgument(i) }");
}
```

输出为：

```sh
Format: The result of { 0 } + { 1 } is { 2 }
Argument 0: 3
Argument 1: 4
Argument 2: 7
```

###### 给字符串插值使用其他区域值

辅助方法 `Invariant` 把插值字符串改为使用不变的区域值，而不是当前的区域值。将 `CultureInfo.InvariantCulture` 传递给 `IFormatProvide` 参数，就可以使用不变的区域值：

```cs
Console.WriteLine(FormattableString.Invariant($"Date: {$day:d}"));
```

> 注：区域值就是对于某些特殊的字符串（比如时间、日期），在不同的区域有不同的格式。

###### 转义花括号

使用两个花括号来转义插值字符串中的花括号：

```cs
Console.WriteLine($"{{}}");
```

输出：

```cs
{}
```

##### 9.2.2 日期时间和数字的格式

在占位符中，格式字符串跟在表达式后面，用冒号隔开：

```cs
var day = new DateTime(1949, 10, 1);
WriteLine($"{day:D}");
WriteLine($"{day:d}");
```

用大写字母 `D` 表示长日期格式字符串，用小写字母 `d` 表示短日期字符串。

输出：

```
Saturday, October 1, 1949
10/1/1949
```

根据系统的语言设置，输出可能不同。

> 另请参阅： [Standard date and time format strings | Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/standard/base-types/standard-date-and-time-format-strings)

使用 `n` 用分隔符（逗号）表示数字， `e` 表示指数表示法， `x` 表示转换为十六进制， `c` 显示为货币。

还可以使用占位符：

1. 数字占位符 `#`：如果数字可用则显示数字，不可用则不显示；
2. 零占位符 `0`：如果数字可用则显示数字，不可用则显示 0；

> 另请参阅：[Standard numeric format strings | Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/standard/base-types/standard-numeric-format-strings)

##### 9.2.3 
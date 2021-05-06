# 字符串和正则表达式

在C#中，`string` 关键字的映射实际上指向 `.NET` 基类 `System.String` 。 `System.String` 是一个功能非常强大且用途广泛的基类。

使用运算符重载可以连接字符串：

```cs
var str = "Hello";
var str1 = "World";
var msg = str + str1;
Console.WriteLine(msg);     // HelloWorld
```

C#还允许使用索引器来提取指定的字符串：

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
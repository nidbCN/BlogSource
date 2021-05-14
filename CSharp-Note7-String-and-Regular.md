## 字符串和正则表达式

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

######## 9.1.1 - 构建字符串

`Spring` 类存在一个问题：重复修改给定的字符串，效率会很低，它实际上是一个不可变的数据类型，表面上修改字符串内容的方法和运算符实际上是创建了一个新字符串并将内容复制过去。

`StringBuilder` 类不像 `String` 类那样支持非常多的方法。它仅限于替换和追加或删除字符串中的文本。但是它的工作方式非常高效。

通常情况下，`StringBuilder` 类分配的内存比它需要的多，在默认情况下就根据初始化实例时的字符串长度来确定所用内存的大小。这个类有两个主要的属性：

* `Length`：字符串的实际长度；
* `Capacity`：在内存中分配的最大长度；

对字符串的修改就在赋予 `StringBuilder` 实例的内存中进行，这就大大提高了追加或替换单个字符的效率，插入和删除子字符（串）的效率依然不高，因为要移动后面的字符串。


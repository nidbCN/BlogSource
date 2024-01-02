---
title: C# 中集合和LINQ的排序
author: Gaein nidb
categories:
  - 学习资料
tags:
  - 笔记
  - 编程
  - CSharp
  - dotNET
date: 2021-06-11 19:39:59
---

## 集合的排序—— `Sort` 方法

`Array` 类提供了 `Sort` 静态方法，使用快排算法对集合中的元素进行排序。
`List<T>` 泛型类提供了 `Sort` 方法，使用快排。

### 不使用自定义的排序

> 注意：使用无参数重载的 `Sort` 方法，需要实现 `IComparable` 接口。简单类型 `System.String` 和 `System.Int32` 等实现 `IComparable` 接口，所以可以对包含这些类型的元素的数组进行排序。

```cs
using System;
using System.Linq;

var array = new[] { 10, 5, 7, 3, 9, -2, };
var list = array.ToList();

Array.Sort(array);
list.Sort();

foreach (var item in array)
{
    Console.Write($"{item} ");
}

Console.Write('\n');.

foreach (var item in list)
{
    Console.Write($"{item} ");
}

```
输出
```cs
-2 3 5 7 9 10 -2
-2 3 5 7 9 10 -2
```

### 自定义规则的排序

#### 传入比较器实例

`Array` 和 `List<T>` 泛型类的 `Sort` 方法都有接受实现 `IComparer` 接口的类的实例对象的重载。

##### 显示定义比较器类

定义类继承于 `Comparer<T>` 泛型类，避免拆箱装箱的额外开销，并且保证类型安全，并且实现 `Compare(T, T)` 方法：

> 注意：也可以继承于 `IComparer` 和 `IComparer<T>` 接口，但是这不是Microsoft 建议的做法：  
> “建议从 Comparer<T> 类派生，而不是实现 IComparer<T> 接口，因为 Comparer<T> 该类提供方法的显式接口实现 IComparer.Compare 和 Default 获取对象的默认比较器的属性。”

```cs
public class MyComparer : Comparer<int>
{
    public override int Compare(int x, int y)
    {
        var xPositive = Math.Abs(x);
        var yPositive = Math.Abs(y);

        return xPositive.CompareTo(yPositive);
    }
}
```

返回值的规则为：
* 当 `x` 应该在 `y` 的 **前面** 的时候（也就是 `x` **小于** `y`），返回小于0的数字（-1）
* 当 `x` 应该在 `y` 的 **后面** 的时候（也就是 `x` **大于** `y`），返回大于0的数字（1）
* 当 `null` 参与比较时不应该引发异常，且为 `null` 的对象应在其它对象 **前面**（也就是说 **小于** 任何其它对象）

###### 另请参阅：

[IComparer 接口 (System.Collections) | Microsoft Docs](https://docs.microsoft.com/zh-cn/dotnet/api/system.collections.icomparer?view=net-5.0)

[IComparer<T> 接口 (System.Collections.Generic) | Microsoft Docs](https://docs.microsoft.com/zh-cn/dotnet/api/system.collections.generic.icomparer-1?view=net-5.0)

[Comparer<T> 类 (System.Collections.Generic) | Microsoft Docs](https://docs.microsoft.com/zh-cn/dotnet/api/system.collections.generic.comparer-1?view=net-5.0)

["Comparer<T>.Compare(T, T) 方法 (System.Collections.Generic) | Microsoft Docs"](https://docs.microsoft.com/zh-cn/dotnet/api/system.collections.generic.comparer-1.compare?view=net-5.0)

##### 使用 `Comparer<T>.Create` 方法和匿名函数创建比较器

`Create` 方法用指定的比较创建一个比较器。

该方法的参数是一个 `Comparison<T>` 类型的委托。可以使用 lambda 表达式直接创建一个匿名函数委托。

```cs
var array = new[] { 10, 5, 7, 3, 9, -2, };

var comparer = Comparer<int>.Create((x, y) =>
    Math.Abs(x).CompareTo(Math.Abs(y))
);

Array.Sort(array, comparer);
```

或者直接

```cs
var array = new[] { 10, 5, 7, 3, 9, -2, };

Array.Sort(array, Comparer<int>.Create((x, y) =>
    Math.Abs(x).CompareTo(Math.Abs(y))
));
```

关于 lambda 表达式可以看我的这篇笔记：[CSharp的委托、lambda 表达式和事件 - CSharp笔记 —— 博客 | Gaein nidb 的网站](https://blog.gaein.cn/passages/CSharp-Note6-Delegates-lambda-Expressions-and-Events/)

箭头前的 `x` `y` 为两个参数，类型为自动推断的int（如果编译器不能推断可以手动加上类型来指定），只有一个参数的时候可以省略箭头前面的括号。

箭头后为表达式体，只有一行语句的时候可以在箭头后直接书写语句，该语句的返回值将作为匿名方法返回值。如果有多行语句，使用花括号框成代码块，并且使用 `return` 关键字返回值。

变量 `comparer` 为 `Comparer<int>` 的实例，作为参数传入 `Array.Sort` 方法即可。

###### 另请参阅：

[Comparer<T> 类 (System.Collections.Generic) | Microsoft Docs](https://docs.microsoft.com/zh-cn/dotnet/api/system.collections.generic.comparer-1?view=net-5.0)

[Comparer<T>.Create(Comparison<T>) 方法 (System.Collections.Generic) | Microsoft Docs](https://docs.microsoft.com/zh-cn/dotnet/api/system.collections.generic.comparer-1.create?view=net-5.0)

#### 传入委托的比较方法

`Array` 类和 `List<T>` 泛型类也都有 `Comparison<T>` 类型参数的重载，所以可以直接使用 lambda 创建匿名函数传入 `Sort` 方法。

```cs
var array = new[] { 10, 5, 7, 3, 9, -2, };
var list = array.ToList();

Array.Sort(array, (x, y) =>
    Math.Abs(x).CompareTo(Math.Abs(y))
);

list.Sort((x, y) =>
    Math.Abs(x).CompareTo(Math.Abs(y))
);
```

没错，把 `Create` 方法的参数扔这里就好了（那我前面是什么废话）。

## LINQ的排序—— `OrderBy` 方法

`OrderBy` 方法用于升序排序，`orderby` 子句会被编译器转换为对 OrderBy 方法的调用。

### 不使用自定义的排序

使用重载 `OrderBy<TSource,TKey>(IEnumerable<TSource>, Func<TSource,TKey>)` 来使用默认的比较器进行升序排序：

```cs
using System;
using System.Linq;

var arr = new[] { 10, 5, 7, 3, 9, -2, };

arr.OrderBy(it => it).ToList().ForEach(Console.WriteLine);
```

输出为
```
-2
3
5
7
9
10
```
### 自定义规则的排序

LINQ 的 `OrderBy` 方法没有像 `Array` 和 `List<T>` 一样的传入 `Comparison<T>` 类型委托的重载。它只提供了重载 `OrderBy<TSource,TKey>(IEnumerable<TSource>, Func<TSource,TKey>, IComparer<TKey>)` 。因此，我们不能在参数表里传入 lambda 表达式创建匿名方法来比较。只能创建类并继承于 `Comparer<T>` ，或者使用 `Comparer<T>.Create` 方法并传入用于比较的匿名方法。

当需要多次进行这种比较时推荐创建自定义的类，并且实例化一个对象多次使用。或者创建一个变量来存储 `Comparer<T>.Create` 创建的对象。

比如：

```cs
var files = new DirectoryInfo(Directory.GetCurrentDirectory()).GetFiles().ToList();

var comparer = new MyComparer();

// 使用创建的类的实例
var listAfterSort1 = files.OrderBy(it => it.Name, comparer).ToList();
// 使用 Comparer<T>.Create 传参委托的匿名函数创建
var listAfterSort2 = files.OrderBy(it => it.Name, Comparer<string>.Create((x, y) =>
    string.Compare(x, y, StringComparison.Ordinal);
));

public class MyComparer : Comparer<string>
{
    public override int Compare(string x, string y)
        => string.Compare(x, y, StringComparison.Ordinal);
}
```
> 注意：这种情况实际上是不需要自定义比较器的，为了演示才这么写的（因为我懒得想例子）
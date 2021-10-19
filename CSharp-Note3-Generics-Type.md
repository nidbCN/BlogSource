---
title: C#中的泛型 - C#笔记 
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - 笔记
  - 编程
  - CSharp
  - dotNET
date: 2021-04-29 12:40:44
---

## 目录

* [C#基础](https://blog.gaein.cn/passages/CSharp-Note1-Basic/)
* [C#面向对象](https://blog.gaein.cn/passages/CSharp-Note2-OOP/)
* [C#泛型](https://blog.gaein.cn/passages/CSharp-Note3-Generics-Type/)
* [C#运算符和强制类型转换](https://blog.gaein.cn/passages/CSharp-Note4-Operators-and-TypeCasts/)
* [C#中数组的使用](https://blog.gaein.cn/passages/CSharp-Note5-Array/)
* [C#的委托、lambda 表达式和事件](https://blog.gaein.cn/passages/CSharp-Note6-Delegates-lambda-Expressions-and-Events/)
* [C#字符串和正则表达式](https://blog.gaein.cn/passages/CSharp-Note7-String-and-Regular/)

## CSharp 泛型

### 5 - 泛型

#### 5.1 - 泛型概述

有了泛型，就可以创建独立于被包含类型的类和方法。比如我希望创建一个链表 `LinkedList` 类，但是存储在 `Node.value` 中的可能是 `int` 类型也可能是其它如 `double` 等类型。这样我们就要为每种类型写一个 `LinkedList` 类，但是大部分实现都是一样的（比如插入、删除等，这些操作和是什么类型几乎没有关系）。所以才有了泛型来解决类似于这样的问题。

另一个减少代码量的是 `Object` 类，但是使用这种类型转换是不安全的，比如我创建了一个 `LinkedList` 类，其中有个方法是 `Add(Object input)` ，我希望这是一个 `int` 链表，但是使用者即使传入 `string` 也不会报错，但是在使用的时候很可能引发运行时抛出异常。

##### 5.1.1 - 性能

值类型存储在栈(Stack)上，引用类型存储在堆(Heap)上。CSharp类是引用类型，结构是值类型。

* 从值类型转换为引用类型称为装箱，装箱是自动进行（隐式转换）的。
* 而从引用类型转换为值类型为拆箱，拆箱必须强制转换（而且如果类型有问题会抛出异常）。

> ValueType ===(Box)===> ReferenceType
> 
> ValueType <==(UnBox)== ReferenceType

另外，这种操作性能损失比较大，遍历多项的时候更加明显。

`List<T>` 类在使用时定义传入的对象的类型，如果泛型 `T` 定义为 `int` ，则它在JIT编译器动态生成的类中使用，不再进行装箱和拆箱操作。大幅度提高了性能。

##### 5.1.2 - 类型安全

在泛型类中，`T` 定义了允许使用的类型，只能使用限定的类，否则编译器会报错。而使用拆箱时不能确定对象是否为指定类型，容易引起程序崩溃或奇奇怪怪的bug。

##### 5.1.3 - 二进制代码的重用

泛型类可以定义一次，并且可以用许多不同的类型实例化。不需要像 C++ 模板那样访问源代码。

##### 5.1.4 - 代码的扩展

##### 5.1.5 - 命名约定

* 泛型类型的名称用字母 "T" 作为前缀。
* 如果没有特殊的要求，泛型类可以用任意类，并且如果只使用了一个泛型类型则通常将其命名为 `T`，比如 `List<T>` 。
* 如果泛型有特殊的要求（比如它要求该类型必须继承自某个接口或派生自某个基类），或者使用了两个或多个泛型，就应该给泛型类型写名称，比如：`Convert<TInput, TOutput>` 。

#### 5.2 - 创建泛型类

```cs
public class LinkedListNode<T>
{
    public LinkedListNode(T value) => Value = value;
    public T Value { get; }
    public LinkedListNode<T> Next { get; internal set; }
    public LinkedListNode<T> Prev { get; internal set; }
}
```

另外，通过实现 `GetEnumerator` 方法，可以用 foreach 语句遍历链表。 `GetEnumerator` 方法使用 `yield` 语句创建一个枚举器类型：

```cs
public class LinkedList<T> : IEnumerable<T>
{
    public LinkedListNode<T> First { get; }
    public LinkedListNode<T> Last { get; }

    public LinkedListNode<T> Add(T node)
    {
        var newNode = new LinkedList<T>(node);
        if (First == null)
        {
            First = newNode;
            Last = First;
        }
        else
        {
            LinkedListNode<T> previous = Last;
            Last.Next = newNode;
            Last = newNode;
            Last.Prev = previous;
        }

        return newNode;
    }

    public IEnumerator<T> GetEnumerator()
    {
        LinkedListNode<T> current = First;
        while (current != null)
        {
            // 使用yield返回枚举的值
            yield return current.value;
            // 迭代
            current = current.Next;
        }
    }

    IEnumerator IEnumerator.GetEnumerator => GetEnumerator();
}
```

#### 5.3 - 泛型类的功能

##### 5.3.1 - 默认值

通过 `default` 关键字，将 `null` 赋予引用类型，将 `0` 赋予值类型。

```cs
public T GetDocument()
{
    T doc = default;
    // ...
}
```

##### 5.3.2 - 约束

假如在上述例子中，我只想要接受实现了 `IDocument` 接口的泛型。

为了在泛型类型的名称中指定该要求，将例子中的 `T` 改为 `TDocument` ，`where` 子句指定了实现 `IDocument` 接口的要求：

```cs
public class DocumentManager<TDocument>
    where TDocument : IDocument
    {

    }
```

> 注意：给泛型类添加约束的时候，最好包含泛型参数名称的一些信息。对编译器而言这不重要，但是它更可读。

##### 5.3.3 - 继承

泛型类可以派生自泛型基类：
```cs
public class LinkedList<T>: IEnumerable<T>
{

}
```

于是，派生类可以是泛型类或非泛型类。例如：可以定义一个抽象的泛型基类，它在派生类中用一个具体的类实现。

##### 5.3.4 - 静态成员

泛型类的静态成员只能在类的一个实例中共享。

#### 5.4 - 泛型接口

使用泛型可以定义接口，在接口中定义的方法可以带泛型参数。

##### 5.4.1 - 协变和抗变

> 淦，这部分有点搞不懂欸（
> 以后再做研究，这部分内容可参考性不高，可能存在错误。目前人是差不多理解了，说不出来...

* 协变：返回中，不能使用子类接收返回的父类，用父类接收返回的子类可以
* 抗变：传参中，子类传入需要父类的地方

（那他妈不都是子类->父类嘛？）

假如有 `Animal` 类和 `Dog` 子类。假设我定义了一个泛型 `Foo<Animal>` 那么它实现了协变，能去接受 `Dog` 类的实例。即协变为接受从子类转换为父类。这种与原始类型转换方向（类 -> Object 基类）相同的可变性就称作协变。

反之，泛型类或方法的返回将 `Dog` 作为 `Animal` 是抗变。

##### 5.4.2 - 泛型接口的协变

如果泛型接口用 `out` 关键字标注，那么它就是协变的。

比如，接口 `IIndex` 是协变的，从一个只读索引器中返回这个类型：

```cs
public interface IIndex<out T>
{
    T this [int index] { get; }
    int Count { get; }
}
```

不使用 `out` 或者 `in` 关键字，可以把类型定义为不变的。

实现了这个接口（ `MyClass: IIndex<Dog>` ）的类可以把返回值赋给 `IIndex<Dog>` ，也可以赋值给 `IIndex<Animal>`。

```cs
IIndex<Rectangle> rectangles = /* codes ... */;
IIndex<Shape> shapes = rectangles;
```

##### 5.4.2 - 泛型接口的抗变

如果泛型类型用 `in` 关键字标记，泛型接口就是抗变的：

```cs
public interface IDisplay<in T>
{
    // codes
}

public class ShapeDisplay : IDisplay<Shape>
{
    // codes
}
```

```cs
IDisplay<Shape> shapeDisplay = new ShapeDisplay();
IDisplay<Rectangle> rectDisplay = shapeDisplay;

// ...
```

抗变更上面的协变看起来效果是相反的（但是它怎么安全的做到这一点的啊？）

#### 5.5 - 泛型结构

与类相似，结构也可以是泛型的，比如 `Nullable<T>`。它们非常类似于泛型类，但是没有继承特性。

把 `Nullable<T>` 类型强制转换成 `T` 类型的运算符重载是显示定义的，因为当 `hasValue` 为 `false` 时，它会抛出一个异常。强制转换 `T` 为 `Nullable<T>` 类型的运算符重载定义为隐式的，因为转换总是成功的。

因为可空类型使用的非常频繁，CSharp有一个 `?` 运算符，用它来定义可空类型的变量，比如：

```cs
int? num = null;
```

> 注意：在运算可空类型的时候，如果两个可空变量中任何一个值是 `null` 那么它们的和就是 `null`

非可空类型总是可以隐式转换为对应的可空类型。从可空类型转换到对应的非可空类型时需要强制转换，可能会引发异常，因此：

```cs
int? num = 18;

int numInt = num ?? -1;
```

可以使用该类合并运算符以不进行显示转换。

#### 5.6 - 泛型方法

除了定义泛型类以外还可以泛型方法。在泛型中，泛型类型用方法声明来定义，泛型方法可以在非泛型类中定义：

```cs
void Span<T>(ref T x, ref T y)
{
    T temp = x;
    x = y;
    y = temp;
}
```

CSharp编译器会通过 `Swap` 方法来获取参数的类型，因此并不需要把泛型类型传过去。只需要 `Swap(3, 6)` 即可。

##### 5.6.1 - 泛型方法示例

##### 5.6.2 - 带约束的泛型方法

类似于带约束的泛型类，使用 `where` 子句进行约束。

##### 5.6.3 - 带委托的泛型方法

可以将泛型类型传给参数中的委托中的泛型类型：

```cs
pubic static T2 Accumulate<T1, T2>(IEnumerable<T1> source, Func<T1, T2, T2> action)
{

}
```

参数表中参数类的泛型将会使用调用时候指定的泛型。

##### 5.6.4 泛型方法规范

泛型方法可以重载，为特定的类型定义规范。这也适用于带泛型参数的方法。

> 注意：所调用的方法是在编译期间而不是运行期间定义的。

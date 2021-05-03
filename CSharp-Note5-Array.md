---
title: C#中数组的使用 - C#笔记 
author: Gaein nidb
categories:
  - 学习资料
tags:
  - 笔记
  - 编程
  - CSharp
  - dotNET
date: 2021-05-03 14:31:30
---

# 目录

* [C#基础](https://blog.gaein.cn/passages/CSharp-Basic/)
* [C#面向对象](https://blog.gaein.cn/passages/CSharp-OOP/)
* [C#泛型](https://blog.gaein.cn/passages/CSharp-Generics-Type/)
* [C#运算符和强制类型转换](https://blog.gaein.cn/passages/CSharp-Note4-Operators-and-TypeCasts/)
* [C#中数组的使用](https://blog.gaein.cn/passages/CSharp-Note5-Array/)

# 数组

## 7 - 数组

### 7.1 - 相同类型的多个对象

可以使用集合和数组，对于数量固定的可以使用数组。

对于多个不同类型的对象可以使用类、结构或元组。

### 7.2 - 简单数组

数组是一种数据结构，它可以包含同一类型的多个元素。

#### 7.2.1 - 数组的声明

使用类型紧跟一对中括号来声明数组，后面跟标识符。比如，下面的代码声明了一个 `int` 类型的数组：

```cs
int[] array;
```

#### 7.2.2 - 数组的初始化

数组是引用类型，所以必须给它分配堆上的内存。应该使用 `new` 运算符，指定元素的类型和数量来初始化数组的变量：

```cs
int[] array = new int[8];
```

上面的代码中 `array` 引用了一个包含8个 `int` 类型的元素的数组，它们位于托管堆上。

> 注意：在指定了数组的大小后，如果不复制其中的所有元素就不能调整大小。而对于很大的数组来说复制数组是一场性能灾难，如果事先不知道包含多少个元素，应该使用集合。

还可以使用数组初始化器为每个元素复制，数组初始化器只能在声明数组的时候使用，不能在声明之后使用。

```cs
int[] array = new int[4] {2, 4, 6, 8};
```

如果使用了初始化器（花括号那个）来初始化数组，可以不指定数组的大小，由编译器自动计算：

```cs
int[] daysOfMonthTable = new int[] {31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
```

当然，使用花括号可以同时声明和初始化数组：

```cs
int[] daysOfMonthTable = {31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
```

#### 7.2.3 访问数组元素

通过索引器传递元素编号，就可以访问数组。索引器总是从0开始，`array[0]` 表示 `array` 数组的第一个元素。

如果不知道数组中元素的个数，又想遍历数组，可以在 `for` 使用 `array.Length` 属性：

```cs
for (var i = 0; i < array.Length; ++i)
{
    Console.WriteLine(array[i]);
}
```

当然，也可以使用 `foreach` 语句，但是 `foreach` 不能更改其中的元素：

```cs
foreach(var item in array)
{
    Console.WriteLine(item);
}
```

#### 7.2.4 - 使用引用类型

除了能声明预定义类型的数组，还可以声明自定义类型的数组，语法同上：

```cs
Student[] stu = new Student[60];
```

声明完后每个元素的引用都是 `null` ，因此，需要使用从0开始的索引器，可以为数组每个元素分配内存：

```cs
stu[0] = new Student()
{
    Name = "Jack",
    Class = "5",
};

stu[0] = new Student()
{
    Name = "John",
    Class = "2",
};

// ...
```

当然，也可以使用数组初始化器。

### 7.3 - 多维数组

正如前面所见的，一维数组使用一个整数来索引，而多维数组使用多个整数来索引（比如我们可以用两个整数分别索引行和列）。

在C#中声明一个二维数组可以在方括号中间加上一个逗号，比如：

```cs
int[,] matrix = 
{
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};
```

显而易见，适用于一维数组的初始化器也能用。

### 7.4 - 锯齿数组

锯齿数组的大小设置比较灵活，不像二维数组那样必须是“方方正正”的，锯齿数组中每一行都可以有不同的大小：

```cs
int[][] array = new int[3][];

array[0] = new int[4] {1, 2, 3, 4};
array[1] = new int[2] {16, 32};
array[2] = new int[1] {114514};
```

定义时，第一个方括号里写行数，第二个留空。

可以看作每个元素都是另外一个数组吧。

### 7.5 - Array 类

用方括号声明数组是C#中 `Array` 类的表示法。在后台使用 `C#` 语法，会创建一个派生自抽象基类 `Array` 的新类。这样，就可以使用 `Array` 类中的方法和属性了。

#### 7.5.1 - 创建数组

除了可以使用C#语法来创建数组实例之外，还可以使用静态方法 `Array.CreateInstance` 来创建数组。如果事先不知道元素的类型，该静态方法就非常有用，因为类型可以作为 `Type` 对象传递给 `CreateInstance` 方法。

还可以将用 `CreateInstance` 方法创建好的数组强制转换为 `int` 数组：

```cs
var array = Array.CreateInstance(typeof(int), 6);

var arrayAfterConvert = (int[])array;
```

#### 7.5.2 - 复制数组

因为数组是引用类型，所以将数组的一个变量赋值给另外一个变量，得到是引用同一个数组的变量。使用 `Clone` 方法可以复制数组：

```cs
var array1 = {1, 2};
var array2 = (int[])array1.Clone();
```

`Clone` 方法是浅复制，也就是说，如果数组的元素的引用类型，则仅复制引用而不深入复制值。

还可以使用 `Array.Copy` 方法创建浅表副本，与 `Clone` 的区别是， `Copy` 方法需要传递一个阶数仙童并且足够大的数组作为参数，而不是像 `Clone` 创建并返回一个新数组。

#### 7.5.3 - 排序

`Array` 类使用快排算法来对数组中的元素进行排序。`Sort` 方法需要数组中的元素实现 `IComparable` 接口。在简单类型中已经实现了该接口，所以可以直接进行排序。

如果对数组使用自定义类，就实现 `IComparable` 接口（或者它的泛型版本），这个接口定义了方法：`Compare` ，该方法接受一个要被用来比较的对象作为参数：

* 如果要比较的对象的相等，就返回0。
* 如果该实例排在参数对象的前面，就返回小于0的值。
* 如果该实例排在参数对象的后面，就返回大于0的值。

如果对象的排序方法与上述不同，或者不能修改数组元素中用作元素的类（即修改元素的类并添加继承自 `IComparable` ），就可以考虑另外一个类（比如 `Comparer` ）实现 `IComparer` 接口或者其泛型版本。这两个接口定义了方法 `Compare` ，这个方法接受两个参数：

* 如果两个参数对象相等，就返回0。
* 如果第一个参数在第二个参数前面，则返回小于0的值。
* 如果第一个参数在第二个参数后面，则返回大于0的值。

> Tips：`Array` 类提供了 `Sort` 方法，需要传递一个委托i作为参数，而不需要依赖 `IComparer` 接口或者 `IComparable` 接口。

### 7.6 - 数组作为参数

要把数组传递给方法，就按着你想象的来就行了。将参数声明为数组：

```cs
static void DisplayStudents(Student[] stu)
{
    // code
}
```

### 7.7 - 数组协变

数组支持协变，也就是说，可以把数组声明为基类，其它派生类的对象可以被赋予数组：

> 注意：数组协变只能用于引用类型不能用于值类型。

### 7.8 - 枚举

在 `foreach` 语句中使用枚举，可以迭代集合中的元素，且不需要知道集合中的元素个数。数组和集合实现了带 `GetEnumerator` 方法的 `IEnumerable` 接口。 `GetEnumerator` 方法返回一个实现 `IEnumerable` 接口的枚举。接着 `foreach` 语句就可以使用 `IEnumerable` 接口迭代集合了。

#### 7.8.1 - `IEnumerator` 接口

`foreach` 语句使用 `IEnumerator` 接口的方法和属性，迭代集合中的所有元素。为此接口定义了 `Current` 用来返回当前迭代的元素。

`MoveNext` 方法移动到下一个元素上，如果没有下一个元素则返回 `false` ，移动成功则返回 `true` 。

#### 7.8.2 - `foreach` 语句

`foreach` 首先调用 `GetEnumerator` 方法获取一个枚举器，并且当 `MoveNext` 方法返回 `true` 时进行迭代。

```cs
foreach(var person in people)
{
    Console.WriteLine(person);
}
```

相当于：

```cs
IEnumerator<Person> enumerator = people.GetEnumerator();
while(enumerator.MoveNext())
{
    var person = enumerator.Current;
    Console.WriteLine(enumerator.Current);
}
```

#### 7.8.3 `yield` 语句

`yield return` 语句返回集合的一个元素，并移动到下一个元素上。`yield break` 可停止迭代。

> Tips：包含 `yield` 语句的方法或属性也成为迭代块。迭代块必须声明为返回 `IEnumerator` 或 `IEnumerable` 接口，或者这写接口的泛型版本。这个块可以包含多条 `yield return` 语句或 `yield break` 语句，但不能包含 `return` 语句。

##### 1. 迭代集合的不同方式

##### 2. 用 `yield return` 返回枚举器

常见的使用方法是，用一个循环来迭代集合，然后每一次迭代来一个 `yield return` 语句，比如：

```cs
using System;
using System.Collections.Generic;

namespace Test
{
    public class Program
    {
        private static void Main()
        {
            var school = new School(10);

            school.Add(new Student("Jack", 1));
            school.Add(new Student("Jan", 2));
            school.Add(new Student("John", 3));
            school.Add(new Student("Mack", 4));
            school.Add(new Student("Ann", 5));

            foreach (var student in school)
            {
                Console.WriteLine(student);
            }
        }
    }

    public class Student
    {
        public Student(string name, uint grade)
        {
            Name = name ??
                   throw new ArgumentNullException(nameof(name));
            Grade = grade;
        }

        public override string ToString() =>
            $"{Name} in {Grade}";

        public string Name { get; set; }
        public uint Grade { get; set; }
    }

    public class School
    {
        public School(uint count)
        {
            Count = count;
            Students = new Student[Count];
        }

        private int _currentIndex;


        public uint Count { get; set; }
        public Student[] Students { get; set; }

        /// <summary>
        /// Add a student to school
        /// </summary>
        /// <param name="stu">Student Object</param>
        public void Add(Student stu) =>
            Students[_currentIndex++] = stu;

        public IEnumerator<Student> GetEnumerator()
        {
            for (var i = 0; i < Count; i++)
            {
                yield return Students[i];
            }
        }
    }
}
```

首先，定义了一个学生类 `Student` ，其中含有学生的基础信息和一个用于实例化的构造函数以及重写的 `ToString` 方法。

然后，定义了另外一个类 `School` ，在学校类中写了 `Add` 方法来添加学生。另外实现了 `GetEnumerator` 方法，由于在 `foreach` 中的元素是 `Student` 类的实例，因此类型为泛型的 `IEnumerator<Student>` 。`yield return` 返回每一次迭代的结果即可。

### 7.9 - 结构比较

数组和元组都实现了接口 `IStructuralEquatable` （用于进行内容比较是否相等）和 `IStructuralComparable` （用于在排序时进行比较决定顺序）。这两个接口不仅可以比较引用，还可以比较内容。

可以让需要的结构实现 `IEquatable` 接口，这个接口定义了一个强化的 `Equals` 方法。这个方法接收两个参数，第一个是 `object` ，第二个是 `IEquatableComparer` ：

```cs
if ((people as IEquatable).Equals(people2, EqualityComparer<Person>.Default))
{
    Console.WriteLine("Same");
}
```

### 7.10 - Span

为了快速访问连续内存，可以使用 `Span<T>` 结构。一个可以使用 `Span<T>` 的例子是数组； `Span<T>` 结构在后台保存在连续的内存中。

使用 `Span<T>` 可以直接访问数组元素。数组的元素没有复制，可以直接使用，这比复制要快（废话）。

`Span` 结构有个构造函数，把数组传参进去就行了，就能获得一个 `Span`。

#### 7.10.1 - 创建切片

`Span<T>` 的一个强大功特性是，可以使用它创建访问数组的一部分或者是切片。使用切片时，不会复制数组元素，它们是从 `Span` 直接访问的。

调用 `Slice` 方法，也可以从 `Span<T>` 对象中创建一个切片。该方法接受两个参数：切片开始的索引号以及切片的长度。当然，也可以使用 `Span` 的构造函数创建一个切片：第一个参数是数组，后面是切片开始的索引号和长度。

如果以后不需要修改 `Span` 引用的内容，可以使用 `ReadOnlySpan` 这个类型，转换是隐式进行的。

> 注意：`Span<T>` 是安全的，不会出界破坏其它内存中的数据。如果创建的 `Span` 超出了数组的长度，则会抛出 `ArgumentOutOfRangeException` 异常。

#### 7.10.2 - 使用 `Span` 改变值

调用 `Clear` 方法，使用 `0` 填充 `Span<int>` 。

调用 `Fill` 方法，使用传递的参数填充 `Span<T>` 。

调用 `CopyTo` 方法，将一个 `Span` 复制到另一个 `Span` 中，如果目标不够大则会抛出 `ArgumentException` 异常。调用 `TryCopyTo` 方法来避免发生异常：

```cs
if (span.TryCopyTo(newSpan))
{
    Console.WriteLine("COPY SUCCESS");
}
```

#### 7.10.3 - 只读的 `Span`

如上文所说，对于不需要修改的 `Span` 可以使用 `ReadOnlySpan<T>` 类型，这种类型没有 `Clear` 和 `Fill` 等方法，但是可以调用 `CopyTo` 复制到新的 `Span` 。

#### 7.11 - 数组池

如果一个应用程序创建、销毁了很多数组，那么GC酱就会很累。可以通过 `ArrayPool` 类创建数组池。

##### 7.11.1 - 创建数组池

通过调用静态的 `Create` 方法，就可以创建 `ArrayPool<T>` 。使用 `Create` 方法可以在创建之前定义最大的数组长度和数组的数量：

```cs
var arrPool = ArrayPool<int>.Create(maxArrayLength: 8192, maxArraysPerBucket: 4096);
```

`maxArrayLength` 的默认值是 1024x1024 字节，`maxArraysPerBucket` 的默认值是 50 。数组池使用了多个桶，以便使用多个数组时更快。只要还没有达到数组的最大数量，大小类似的数组就尽可能保存在一个桶里。

#### 1.12.2 - 从池中租借内存

调用 `Rent` 方法从池中租借内存。该方法接受应请求的最小数组长度。`Rent` 方法返回一个数组，其中至少包含所请求的元素个数。返回的数组可能有更多的可用内存：

```cs
var arrayPool = ArrayPool<int>.Create(maxArrayLength: 8192, maxArraysPerBucket: 4096);
var array = arrayPool.Rent(20);

Console.WriteLine(array.Length);    // output: 32
```

#### 1.12.3 - 将内存返回给池

不再需要数组的时候，可以把内存返回回去。数组返回后，稍后可以使用一个 `Rent` 来重用这些内存。

调用数组池的 `Return` 方法并将数组作为参数传递给它。这样返回后不会清除数组内的数据，也就是下次 `Rent` 后还可以访问其中的数据。为此该方法还有一个可选参数，指定在返回池之前是否清空它。
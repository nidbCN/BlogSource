# 对象和类型

> 系统学习C#笔记，本来也想写在基础部分来着，但是上一篇博客太长了，想想还是拆出来写吧。

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
public string Name {get; set;}

public Guid Uid {get; set;} = new Guid();
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
public string FirstName {get; set;}
public string LastName {get; set;}
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
---
title: lua语言编程基础 - lua笔记 
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - 笔记
  - 编程
  - lua
date: 2022-07-26 12:25:44
---

## 前言

> 最近实在太摸辣！
> 前景提要：收了个 小米AC2100 刷了 OpenWrt 系统，那个固件带了一个微信推送，但是我觉得不是特别好用，准备自己弄一个 基于 `linux netlink`、`ASP.NET Core` 做后端，使用 `MQTT` 或者 `Google FCM` `Mi Push` 做推送。`Luci` 用 `lua` 脚本编写插件，反正也没什么事就来看看 `lua` 吧！
> ~~为什么叫lua不叫rua~~

## 关于 lua

Lua 是一种轻量小巧的脚本语言，用标准C语言编写并以源代码形式开放， 其设计目的是为了嵌入应用程序中，从而为应用程序提供灵活的扩展和定制功能。

lua 很轻量、与 C/C++ 交互方便、支持 面向过程 or 函数式 编程。

## 环境安装

直接摁包管理器！

Scoop: `scoop install lua`

如果包管理器没有就解压扔环境变量呗

## 运行方式

### 脚本运行

lua 脚本是文本文件

script:

```lua
#!/usr/local/bin/lua

print("Hello world")
```

script.lua:

```lua
print("hello world")
```

还可以在命令行中输入 `lua -i` 来使用 lua REPL

### 数据类型

lua 语言有如下几种数据类型

* nil: null，未定义的变量为 `nil`，同样，可以给变量赋值 `nil` 来“销毁” 它
* boolean: true / false，条件表达式中只有 `nil` 和 `false` 为否，其余（包括数字 `0` 皆为是
* number: 数字类型，双精度浮点实数，所有的数字都是 `number` 类型
* string: 字符串，使用 `"` 或 `'` 包裹，`..` 连接。多行字符串可以使用 `[[` 和 `]]` 包裹
* function: C 或 lua 函数（可以类似于委托来使用）
* userdata: 用户类型
* thread: 用于协程
* table: 表，类似于 PHP 中的 “数组”，可以存储数组或键值对之类的

#### 获取类型

在 lua 中，可以使用 `type()` 函数来获取类型，比如

```lua
type("str")     -- string
type(114514)    -- number
type(unknow)    -- nil
type(0 < 1)     -- boolean
```

在上述案例中，`unknow` 未定义，所以输出为 `nil`

注意，判断 `nil` 时候应该加上引号，因为 `typeof` 的结果是字符串类型：

```lua
if typeof(x) == "nil" then
    print("x is null!")
end
```

#### string 类型

string 就是字符串，很正常的一个字符串。转义字符也都一样。

##### string 初始化

前面说过了，可以用双引号也可以用单引号：

```lua
str = "hello"
str = 'hello'
str = [[
{
    "message": "hello"
}]]
```

当然，用 `[[` 和 `]]` 包裹显示 “多行字符串” 或者 “原字符串”，类似于 C# 中的 `@""` 或者 python 中的 `r""`。

##### string 相关函数使用

1. 字符串连接：`..`；
2. 大小写转换：`string.upper(str)` 和 `string.lower(arg)`；
3. 替换：`string.gsub(str, search, replace, [times])`，次数省略为不限次数；
4. 查找：`string.find(str, search, [init, [regxp]])`，返回两个值：起始位置和结束位置；
5. 反转：`string.reverse(str)`；
6. 格式化：`string.format(...)`，格式化规则类似于 `printf`；
7. 字符与 ASCII 代转换：`string.char(nums)` 字符串中的字符转 ASCII 码返回，`string.byte(str, [index])`；
8. 计算长度：`string.len(str)` 或 `#`，例如 `#str`；
9. 重复字符串：`string.rep(str, times)`，重复 `times` 次字符串 `str`；
10. 正则字符串：`string.match(str, pregxp, init)` 只寻找源字串str中的第一个配对并返回结果，找不到时返回 `nil`；

你说 number 转 string? 使用数字运算符的时候会自动尝试将 string 转为 number，所以可以写类似于

```lua
result = "3" + 5        -- 8
result = 3 + "5"        -- 8
result = "3" + "5"      -- 8
result = "test" + 5     -- stdin:1: attempt to add a 'string' with a 'number'
```

当然，单纯需要转换的话可以使用 `tostring()` 和 `tonumber()` 函数。

更多字符串操作参考 [Lua 5.4 官方文档](https://www.lua.org/manual/5.4/manual.html#6.4)

#### table 类型

这个 `table` 很神奇，反正类比一下 PHP 的 “数组”，这玩意也是一个 “什么都能塞” 的角

##### table 的初始化

table 使用初始化器 `{}` 来初始化，如下代码初始化了一个 table:

```lua
t = {}
```

在初始化时候，如果需要指定索引的键，则可以使用 `[]` 索引器，说起来有点抽象，看看代码：

```lua
t = {["k"] = "value", ["arr"] = { 1, 2, 3, }, [3] = 0, }
print(t["k"])   -- "value"
```

没错，`[]`不仅在索引时候用，在创建的时候还可以用来表示键，虽然我不知道这时候他叫什么。不过，为了方便起见，索引 key 为字符串的时候，可以省略掉 `[]` 而使用 `.`，比如

```lua
print(t.k)
````

创建的时候也可以省略 `[]`，比如

```lua
t = {k = "value", arr = { 1, 2, 3, }, [3] = 0, }
```

哦对了，还有一个很生草的事情，在 `lua` 中，索引是从 `1` 开始的，这一点也不反人类但是反程序员。

其他类型不再赘述

##### table 相关函数使用

1. 连接各元素：`table.concat(table, [sep , [start ,[end]]])`，实际上有些像 `string.Join` 的功能，将各元素连接起来；
2. 插入元素：`table.insert(table, [pos] ,value)`，在 `pos` 位置插入 `value`，省略默认为尾部插入；
3. 删除元素：`table.remove (table, [pos])` 删除 `pos` 位置的值，并返回，省略默认为尾部删除；
4. 排序：`table.sort (table, [comp])` 默认为升序排序；

> 或许大概没准是由于 table 存的东西奇奇怪怪，Lua 并没有提供计算长度的函数，用 `#` 运算符也会有问题，那就手搓遍历罢（悲

#### function 类型

函数作为一个基础类型出现在这里，可以类比委托或者类似于 js 的 function 那样，既可以调用也可以作为一个变量/参数传递。

首先是函数定义：

```lua
function writeLine(arg)
    print(arg.."\n")
end
```

需要局部函数可以在 `function` 前面加修饰符 `local`，返回值可以有多个。

可变参数 function：

```lua
function printf(...) 
    local arg={...}
    if #arg == 1 then   -- 或用 select("#",...) 获取参数数量
        print(arg)
    else
        print(string.format(arg))
    end
end
```

将函数赋值给变量：

```lua
func = function(a) 
    print(a)
end

func(114514)
```

基础用法大概差不多这样，其它类型留到以后再说或者不再赘述。

### 运算符

#### 算术运算符

除了常规的 `+` `-` `*` `/` `%` 之外，还有 `^`（幂）和 `//`（整除）

#### 关系运算符

常规的 `==` `<=` `>=` `<` `>` 之外，不等于用 `~=`

#### 逻辑运算符

不是 `||` 这些符号，而是类似于 `python`，用 `and` `or` `not`

#### 其他运算符

一个是计算长度用的 `#`，一个是连接字符串的 `..`（其实这大概也算是算术运算符？）

### 流程控制

流程控制嘛其实说白了就是 if-else，具体使用代码如下所示：

```lua
if type(x) == "nil" then
    print("x is null")
else
    print("x not null")
end
```

有些类似于 SQL 中的判断嘛

Lua 的 if-else 可以嵌套使用 ~~废话~~

### 循环

#### while 循环

如下所示

```lua
while(true)
do
    refresh()
end
```

#### for 循环

```lua
for i=0,100,1 do
    print(i)
end
```

说说这个 `for` 吧，`for` 后面是 `var=exp1,exp2,exp3` 其中表达式1是初始值，表达式2是终止值，表达式3为步长，比如我要输出 100 以内 可以被 3 整除的数，可以这么写：

```lua
for i=3,100,3 do
    print(i)
end
```

另外很重要一个用途就是遍历 table 之类的，这玩意叫“泛型 for 循环”，实际上很类似于 `foreach`：

```lua
studentList = {"Wang", "Li", "Zhang"}
for i, name in ipairs(studentList) do
    print(i, name)
end 
```

至于这个 `ipairs()` 函数，是 Lua 的一个迭代器，后续细说怎么个回事。

#### repeat...until（类似 do-while 循环）

```lua
repeat
    refersh()
until(stop == true)
```

很类似 do-while 的一个玩意

> 果然各种语言里面最花的都是 `for` 嘛
> pythone: 喵喵喵？？

### 变量

标识符的规则和关键字不再赘述了，各家都大同小异的

另外就是 Lua 有全局变量和局部变量，默认的变量是全局变量，用 `local` 关键字修饰后才是局部变量，比如：

```lua
function homo()
    var1 = 114514
    var2 = 1919810
end

function test()
    var1 = 9
    local var2 = 10

    homo()
    print(var1)
    print(var2)
end

test()

```

写的有点乱反正就是这么回事（摆烂

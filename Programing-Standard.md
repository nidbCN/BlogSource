---
title: 编程代码规范
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - 笔记
  - 编程
date: 2021-02-16 15:44:13
---

上了半年大学，也写了很多年代码了（厚颜无耻）。总结一下编程中用到的一些代码规范，命名法则等。在这里总结一下。

<!--MORE-->

## 前言

> 上了半年大学，课余时间充裕了，学到了很多计算机的知识。虽然也是摸鱼占大多数，但是自己写的代码确实比以前规范了一些（虽然仍然是屎山）

参考学长的这篇 [C/C++ 编码规范](https://blog.dreace.top/2020/C-C-Coding-Standards/)

## 内容

#### 缩进

层次之间要有缩进，如

```cs
namespace MyProgram
{
    class Program
    {
        public static void Main(string args[])
        {
            if (args.Length > 0)
            {
                // Do something...
            }
        }
    }
}
```

#### 注释

使用单行注释后后面留一个空格。

使用 XML 注释时候写清所有参数。

#### 命名

变量名使用驼峰命名法 Camel：
第一个单词首字母小写，其余单词首字母大写，比如`connectionStr` ，`resourcesUri`，`addedNode`等
常用于变量
字段或方法、类等使用帕斯卡命名法 Pascal：
第一个单词首字母大写，其余与驼峰相同，比如`GetToken`，`InsertToList`，`UserId`等

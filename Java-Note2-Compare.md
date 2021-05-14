---
title: 比较——零基础学Java语言笔记（二）
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - 语言
  - Java
  - 笔记
date: 2020-10-04 14:43:04
cover: 'https://s1.ax1x.com/2020/10/04/0GAp7Q.md.png'
---
距离笔记1大概有两周了，由于Java、C、C++、CSharp都是C like语言，它们的有很多相似之处，因此没有再细致一点点看，而是从数组开始看起，将《零基础学习Java看完》。当然这几节只是非常基础的课程，所以笔记也比较简短。已经了解之处就不再做笔记了。
<!--MORE-->

> 终于没有忍住去跟课程进度，而是去看了上次开课的视频。下一步的计划是看进阶的Java，或者是学习一下python。最近用python写了一个QQ bot，发现很是好用。

## 下一篇笔记

[计算——零基础学Java语言笔记（一）](https://blog.gaein.cn/passages/Java-Note1-Calculation/)

## 下一篇笔记

暂无

## 2.1 比较

Java语言使用`// notes`和`/*notes*/`来作注释

Java语言的关系运算符有`>`、`<`、`==`、`>=`、`<=`、`!=` 6个

```java
System.out.println(5==3); // 输出false
System.out.println(5>=3); // 输出true
```

所有关系运算符的优先级比算数运算符的低，但比赋值运算符`=`高

## 2.2 判断

> 建议不管有几条语句在if或else后总要跟上一对花括号，这样利于人类阅读

```java
if (/*条件*/){
    // 代码
    // ...
}
// if语句

if (/*条件*/){
    // 代码
    // ...
} else {
    // 代码
    // ...
}
// if-else语句

if (/*条件*/){
    // 代码
    // ...
} else if (/*条件2*/){
    // 代码
    // ...
}
// if-elseif语句
```

## 2.3 分支

switch-case
```java
switch (/*控制表达式，通常是某个变量*/){
    case /*常量*/:
        // 语句
        // ...
        break;
    case /*常量*/:
        // 语句
        // ...
    default:
        // 语句
        // ...
}
```

执行完分支中的语句后，如果后面没有`break;`，就会进入下面的`case`里面去，直到遇到`break;`或者`switch`结束为止

## 2.4 判断语句常见错误

## 2.x 小测验


###### 1.写出以下代码段的执行结果：

```java
int num=34, max=30;
if ( num >= max*2 )
    System.out.println("zhang");
    System.out.println("huang");
System.out.println("zhu");
```

> huang\n

###### 2.写出以下代码段的执行结果：

```java
int limit = 100;
int num1 = 15;
int num2 = 40;
if ( limit <= limit)
{
    if ( num1 == num2 )
        System.out.println("lemon");
    System.out.println("lime");
}
System.out.println("grape");zhu
```

> lime\ngrape

## 第二周编程题

###### 第一题——时间换算

> 题目内容：
`UTC`是世界协调时，`BJT`是北京时间，`UTC`时间相当于`BJT`减去`8`。  
现在，你的程序要读入一个整数，表示`BJT`的时和分。  
整数的个位和十位表示分，百位和千位表示小时。  
如果小时小于`10`，则没有千位部分；如果小时是`0`，则没有百位部分；如果分小于`10`分，需要保留十位上的`0`。    
如`1124`表示`11`点`24`分，而`905`表示`9`点`5`分，`36`表示`0`点`36`分，`7`表示`0`点`7`分。  
有效的输入范围是`0`到`2359`，即你的程序不可能从测试服务器读到`0`到`2359`以外的输入数据。  
你的程序要输出这个时间对应的`UTC`时间，输出的格式和输入的相同，即输出一个整数，表示`UTC`的时和分。  
整数的个位和十位表示分，百位和千位表示小时。  
如果小时小于`10`，则没有千位部分；如果小时是`0`，则没有百位部分；如果分小于`10`分，需要保留十位上的`0`。  
提醒：**要小心跨日的换算。**  
输入格式:  
一个整数，表示`BJT`的时和分。整数的个位和十位表示分，百位和千位表示小时。如果小时小于`10`，则没有千位部分；如果小时是`0`，则没有百位部分；如果小时不是`0`而且分小于`10`分，需要保留十位上的`0`。  
输出格式：    
一个整数，表示`UTC`的时和分。整数的个位和十位表示分，百位和千位表示小时。如果小时小于`10`，则没有千位部分；如果小时是`0`，则没有百位部分；如果小时不是`0`而且分小于`10`分，需要保留十位上的`0`。  
输入样例：`933`  
输出样例：`133`  
时间限制：500ms内存限制：32000kb

需要注意的问题：
1. 是从BJT转到UTC
2. UTC = BJT - 8
3. 跨日期需要加上24小时
   
提交的代码：

```java
import java.util.Scanner;

public class Main {
    public static void main(String[] Args){
        Scanner in = new Scanner(System.in);
        
        int BJT = in.nextInt(); // 输入北京时间
        int UTC = BJT - 800;    // 转换成UTC
        
        if (UTC < 0)            // 判断是否跨日
        {
            UTC += 2400;
        }
        
        System.out.println(UTC);
    }
}
```

###### 第二题——信号报告

> 题目内容：
无线电台的RS制信号报告是由三两个部分组成的：  
`R(Readability)` 信号可辨度即清晰度.  
`S(Strength)`    信号强度即大小.  
其中`R`位于报告第一位，共分5级，用1—5数字表示.  
`1---Unreadable`  
`2---Barely readable, occasional words distinguishable`  
`3---Readable with considerable difficulty`  
`4---Readable with practically no difficulty`  
`5---Perfectly readable`  
报告第二位是`S`，共分九个级别，用1—9中的一位数字表示  
`1---Faint signals, barely perceptible`  
`2---Very weak signals`  
`3---Weak signals`  
`4---Fair signals`  
`5---Fairly good signals`  
`6---Good signals`  
`7---Moderately strong signals`  
`8---Strong signals`  
`9---Extremely strong signals`  
现在，你的程序要读入一个信号报告的数字，然后输出对应的含义。如读到59，则输出：  
`Extremely strong signals, perfectly readable.`  
输入格式:  
一个整数，信号报告。整数的十位部分表示可辨度，个位部分表示强度。输入的整数范围是`[11,59]`内有效的数字，这个范围外的数字不可能出现在测试数据中。  
输出格式：  
一句话，表示这个信号报告的意义。按照题目中的文字，先输出表示强度的文字，跟上逗号和空格，然后是表示可辨度的文字，跟上句号。注意可辨度的句子的第一个字母是小写的。注意这里的标点符号都是英文的。  
输入样例：  
`33`  
输出样例：  
`Weak signals, readable with considerable difficulty.`  
时间限制：500ms内存限制：32000kb

需要注意的问题：
1. 输出时候先输出S再输出R
2. 输出的格式为`S,□R.`（“□”代表空格）
3. R的第一个字母要小写

解决方案：
1. 获取R和S数字值：
   假设输入的是input，那么我们用input除以10可以得到十位上的数字，对它取余10可以得到个位上的数字
   ```java
   int r = input / 10;
   int s = input % 10;
   ```
2. 出题者的意图显然是用`switch-case`来解决问题，但是未免太麻烦了点，至少看起来很难受。因为懒得用map所以我的解决方案是使用字符串数组，按顺序将字符串放在单元中，然后用`s`或者`r`减去`1`的值作为索引就可以了。
   
提交的代码：
```java
import java.util.Scanner;

public class Main_2 {

    public static void main(String[] Args) {

        Scanner in = new Scanner(System.in);

        int input = in.nextInt(); // 获取输入
        int r = input / 10;       // 解析获取R
        int s = input % 10;       // 解析获取S

        String[] rDic = {
                "unreadable",
                "barely readable, occasional words distinguishable",
                "readable with considerable difficulty",
                "readable with practically no difficulty",
                "perfectly readable",
        };
        // 定义字符串数组存储R对应的字符串
        String[] sDic = {
                "Faint signals, barely perceptible",
                "Very weak signals",
                "Weak signals",
                "Fair signals",
                "Fairly good signals",
                "Good signals",
                "Moderately strong signals",
                "Strong signals",
                "Extremely strong signals",
        };
        // 定义字符串数组存储S对应的字符串

        System.out.println(sDic[s - 1] + ", " + rDic[r - 1] + ".");
        // 输出结果
    }
}
```
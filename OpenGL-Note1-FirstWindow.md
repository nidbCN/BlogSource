---
title: 'OpenGL学习笔记——第一个窗口'
author: Gaein nidb
categories:
  - 代码如诗
cover: "https://s1.ax1x.com/2020/11/10/BbBr8A.md.png"
tags:
  - 笔记
  - C语言
  - OpenGL
date: 2020-11-13 20:50:55
---
学校的C语言程序设计课设是编写一个图形化的程序，学校方面采用的是EasyX，当然也允许其它图形库，因此我准备学习OpenGL
<!-- more -->

> EasyX虽然可能比OpenGL简单一些，但是可能不如OpenGL应用面更广泛，也不如OpenGL更有挑战性。

## 基本

#### 笔记

OpenGL是一个图形API，需要一个编程语言来工作，因此我选择C/C++，在不需要用到C++特性的情况下会使用C来编写（因为毕竟是C语言程序设计课设）。我使用的教程是[LearnOpenGL CN](https://learnopengl-cn.github.io)和《OpenGL编程指南》（忘了是那一版了，实验室的书；大部分情况下会使用LearnOpenGL CN网站的教程）  

#### OpenGL

早期的OpenGL使用**立即渲染模式**（固定渲染管线），这个模式下绘制图形很方便。从OpenGL3.2开始，立即渲染模式开始被废除，鼓励使用**核心模式**。  
OpenGL自身是一个巨大的状态机：一系列的变量描述OpenGL此刻应当如何运行。OpenGL的状态通常被称为OpenGL上下文(Context)。
（OpenGL自己搓了面向对象）一般来说，我们创建一个对象，然后用一个id保存它的引用（实际数据被储存在后台）。然后我们将对象绑定至上下文的目标位置（例子中窗口对象目标的位置被定义成GL_WINDOW_TARGET）。接下来我们设置窗口的选项。最后我们将目标位置的对象id设回0，解绑这个对象。设置的选项将被保存在objectId所引用的对象中，一旦我们重新绑定这个对象到GL_WINDOW_TARGET位置，这些选项就会重新生效。

#### GLFW

GLFW是一个库，提供了渲染物体的最低限度的接口。  

###### 获取

[官方网站下载页](http://www.glfw.org/download.html)  

> 使用预编译的二进制版本时下载32位，因为64位可能有奇奇怪怪的问题。

###### 链接

1. “右键项目”->“属性”->“VC++目录”，在“包含目录”和“库目录”中添加GLFW的include文件夹；
2. 在“链接器”->“输入”->“附加依赖项”中加入"`glfw3.lib`"、"`opengl32.lib`"

#### GLAD

由于OpenGL驱动版本众多，因此大多数函数在编译时没有确定的位置，在运行时查询的工作量很大，因此使用GLAD库来解决。  

###### 配置GLAD

1. 打开[GLAD在线服务](http://glad.dav1d.de/)；
2. 设置语言为`C/C++`；
3. OpenGL版本选择`3.3`及以上；
4. 模式设置为`Core`；
5. 选中**生成加载器**(Generate a loader)；
6. 点击**生成**(Generate)按钮。

###### 使用GLAD

将生成的文件添加到`include`目录中
引用头文件

```c
##include <glad/glad.h>
```

来使用

## 你好，窗口

在`main()`函数中
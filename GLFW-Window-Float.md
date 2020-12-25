title: 'OpenGL GLFW 窗口前端显示'
author: Gaein nidb
categories:

- 代码如诗
  cover: "https://s1.ax1x.com/2020/11/10/BbBr8A.md.png"
  tags:
- 笔记
- C 语言
- OpenGL
  date: 2020-12-26 1:27:00

---

使用 GLFW 库自带的函数设置窗口置于顶层前端显示。起因：一位群友问到这个问题，在网上查找 GLFW 前端显示窗口和 OpenGL 前端显示窗口无果后翻阅 GLFW 的 docs 找到了解决方案。写个博客记录一下。

<!-- more -->

> 起因：一位群友问到这个问题，在网上查找 GLFW 前端显示窗口和 OpenGL 前端显示窗口无果后翻阅 GLFW 的 docs 找到了解决方案。写个博客记录一下。

# 起因

群友在群里问有没有会 OpenGL 的，~~虽然我不会但是肯定要凑热闹假装会啊~~。它的需求是创建一个窗口，显示在最顶层（即使是有全屏程序的情况下）大概就是类似于录屏工具的样子。

# 过程

在网上几番搜索没用结果后我干脆直接翻看了 GLFW 的官方文档（果然那玩意才是最好用的）

一开始我以为是在创建窗口`glfwCreateWindow()`时候传入参数里面设置置顶。但是并没有相关的参数，倒是发现了[全屏的方法](https://www.glfw.org/docs/latest/window_guide.html#window_windowed_full_screen)。

继续查阅函数列表，希望找到一个能置顶窗口的函数，类似于`glfwSetWindowTop()`这样名字的。但是一番查找无果。像`glfwShowWindow ( GLFWwindow * window ) `显示窗口和`void glfwFocusWindow ( GLFWwindow * window ) `聚焦窗口都不是我想要的，聚集窗口会把焦点移动到窗口上，如果你打游戏时候干了这怕是要被打死。

最后引起我注意的是函数

```c
void glfwSetWindowAttrib	(	GLFWwindow * 	window,
int 	attrib,
int 	value
)
```

它的意思是设置窗口的属性，我想，置顶不正是一种属性嘛？于是我跳转到了`glfwSetWindowAttrib()`的[页面](https://www.glfw.org/docs/latest/group__window.html#gace2afda29b4116ec012e410a6819033e)，用法很简单，和想象的一样。里面有个链接到[Window attributes](https://www.glfw.org/docs/latest/window_guide.html#window_attribs)，及窗口的所有属性列表。在这里我们可以看到一个叫做`GLFW_FLOATING`属性，[它的介绍](https://www.glfw.org/docs/latest/window_guide.html#GLFW_FLOATING_hint)是：

> GLFW_FLOATING indicates whether the specified window is floating, also called topmost or always-on-top. This can be set before creation with the GLFW_FLOATING window hint or after with glfwSetWindowAttrib.

它可以让窗口浮动，也就是置顶（always-on-top）这正是我们想要的了！

# 实施

随便打开了以前测试 OpenGL 环境的项目，我的想法是按下`T`键置顶，这样可以有一个前后对比。在监听按键的函数里面如下写道：

```c
// 按下T设置浮动
if (glfwGetKey(window, GLFW_KEY_T) == GLFW_PRESS)
{
	printf("[INFO]监测到置顶窗口");
	glfwSetWindowAttrib(window, GLFW_FLOATING, GLFW_TRUE);
}
```

启动程序，点击其它程序发现可以覆盖我们的窗口。焦点在窗口时按下`T`，然后再将其它程序拖动过去，发现不能覆盖。全屏播放视频和全屏游戏也不能将我们的窗口覆盖。至此目的达成。

# 总结

使用如下代码将窗口置顶，其中`window`是窗口指针

```c
glfwSetWindowAttrib(window, GLFW_FLOATING, GLFW_TRUE);
```

使用如下代码取消窗口置顶，其中`window`是窗口指针

```c
glfwSetWindowAttrib(window, GLFW_FLOATING, GLFW_FALSE);
```
---
title: Windows下CLion配置SDL2开发环境
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - C语言
  - CLion
  - SDL2
  - 笔记
  - 软件教程
date: 2020-12-16 23:51:22
---

SDL（Simple DirectMedia Layer）是一套开放源代码的跨平台多媒体开发库，使用 C 语言写成。SDL 提供了数种控制图像、声音、输出入的函数。简单易用。这篇文章介绍一下我在 Windows 下 CLion 配置 SDL2 开发环境的方法。

> 配 SDL2 还是由于大一的 C 语言程序设计课设。由于我始终不愿意使用 EasyX，却由于 GLFW 太过复杂而担心无法完成作业
> 
> ~~其实我的担心是多余的，以我的实力，根本不用担心，就是完不成~~
> 
> 所以采用了学 ~~姐~~ 长 [@HelloWorld](https://mzdluo123.github.io/) 的建议，使用了 SDL。不过配置的过程中遇到了很多问题，在这里记录并分享一下。

> 环境：
>
> 1. CLion 2020.3 Build 203.5981.166
> 2. Windows10 20H2 19042.685
> 3. MinGW-w64 GCC 8.1.0(x86_64-posix-seh-rev0)
> 4. SDL2-2.0.12 [DEV]

## SDL2 配置

### 下载 SDL2

1. 打开 SDL 官网的下载页 [LibSDL.org](https://www.libsdl.org/download-2.0.php)
   ![下载](https://img.cdn.gaein.cn/website_used/blog/Config-SDL2-On-CLion-Win/01.webp)

2. 下载 "Development Libraries" (开发版)中的 "Windows"->"MinGW 32/64-bit"  
   我下载的是["SDL2-devel-2.0.12-mingw.tar.gz"](https://www.libsdl.org/release/SDL2-devel-2.0.12-mingw.tar.gz)  
   (注：此链接不确保时效性，请去官网下载最新版本)

### 配置 SDL2

1. 解压缩下载的 "SDL2-devel-2.0.12-mingw.tar.gz" ，将 "x86_64-w64-mingw32" 中的所有文件复制到你 MinGW-w64 的目录中。  
   如果你的 MinGW 是 i686 则复制"i686-w64-mingw32"中的所有文件。
   (注：在终端中使用 `gcc -v` 可以查看到 MinGW-w64 的版本)
   ![解压](https://img.cdn.gaein.cn/website_used/blog/Config-SDL2-On-CLion-Win/02.webp)
   ![复制](https://img.cdn.gaein.cn/website_used/blog/Config-SDL2-On-CLion-Win/03.webp)
   ![粘贴](https://img.cdn.gaein.cn/website_used/blog/Config-SDL2-On-CLion-Win/04.webp)
2. 在项目目录下新建一个文件夹"SDL"，将"include"中的"SDL2"，复制到项目目录下"SDL"文件夹中
   ![SDL2](https://img.cdn.gaein.cn/website_used/blog/Config-SDL2-On-CLion-Win/05.webp)

## CMake 配置

### 新建

在 CLion 中点"文件"->"新建项目"，选择 C++(C 也可以)
![新建](https://img.cdn.gaein.cn/website_used/blog/Config-SDL2-On-CLion-Win/06.webp)

#### 配置

将"CMakeLists.txt"按照如下内容修改：  
(注：文件的内容根据你的项目决定，一般来说不用修改默认生成的)
![库](https://img.cdn.gaein.cn/website_used/blog/Config-SDL2-On-CLion-Win/07.webp)

```cmake
# 设置CMake需要的版本
cmake_minimum_required(VERSION 3.17)
# 设置项目名称
project(SDL2_Test)
# 设置C++版本
set(CMAKE_CXX_STANDARD 20)
# 设置includePath
include_directories(SDL)
# 生成文件
add_executable(SDL2_Test main.cpp)
# 链接库
target_link_libraries(SDL2_Test mingw32 SDL2main SDL2)
```
![完成](https://img.cdn.gaein.cn/website_used/blog/Config-SDL2-On-CLion-Win/08.webp)

## 测试

在 "main.cpp" 中写入如下内容：
```cpp
#include "SDL2/SDL.h"

int main(int argc, char *argv[])
{
    // 初始化SDL
    SDL_Init(SDL_INIT_VIDEO);
    // 创建SDL窗口指针
    SDL_Window *window = SDL_CreateWindow(
            "SDL2Test",
            SDL_WINDOWPOS_UNDEFINED,
            SDL_WINDOWPOS_UNDEFINED,
            640,
            480,
            0
    );

    // 创建渲染器
    SDL_Renderer *renderer = SDL_CreateRenderer(window, -1, SDL_RENDERER_SOFTWARE);
    // 设置渲染器
    SDL_SetRenderDrawColor(renderer, 0, 0, 0, SDL_ALPHA_OPAQUE);
    SDL_RenderClear(renderer);
    // 渲染
    SDL_RenderPresent(renderer);

    // 等待3000ms
    SDL_Delay(3000);

    // 销毁窗口&退出
    SDL_DestroyWindow(window);
    SDL_Quit();

    return 0;
}
```

![源代码](https://img.cdn.gaein.cn/website_used/blog/Config-SDL2-On-CLion-Win/09.webp)

![结果](https://img.cdn.gaein.cn/website_used/blog/Config-SDL2-On-CLion-Win/10.webp)

点击右上角的三角运行，如果出现

1. 无法找到头文件：检查是否复制"include"和设置"include_directories(SDL)"
2. 找不到WinMain：检查是否复制库。或者尝试调换"target_link_libraries(SDL2_Test mingw32 SDL2main SDL2)"中库的顺序

## 说明

1. 有些同学可能很迷惑：为什么要把SDL扔进MinGW-w64的目录里面呢？因为我在外面的目录尝试的时候各种报错，后来看了 [CSDN:CLion配置SDL2开发环境（Windows系统）](https://blog.csdn.net/ronaldinho2014/article/details/104835426)。貌似把SDL扔进mingw目录就能解决。具体原因还有待深究。
2. 其它方法：使用vcpkg可以安装，也可以使用CMake模块：
   1. [GitHub:SDL2-CMake-Scripts](https://github.com/tcbrindle/sdl2-cmake-scripts)
   2. [GitHub:CMake-SDL](https://github.com/binary132/cmake-sdl)

参考：
1. [SDL官网:SDL-WIKI](https://wiki.libsdl.org/)
2. [Trenki's Dev Blog:Using SDL2 with CMake](https://trenki2.github.io/blog/2017/06/02/using-sdl2-with-cmake/)
3. [JetBrains-Community:Configuring SDL2 with CLion](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360006864999-Configuring-SDL2-with-Clion)
4. [adolfans:SDL中文教程](https://adolfans.github.io/sdltutorialcn/)
5. [CSDN:CLion配置SDL2开发环境（Windows系统）](https://blog.csdn.net/ronaldinho2014/article/details/104835426)
6. [CMake:FindSDL](https://cmake.org/cmake/help/latest/module/FindSDL.html)
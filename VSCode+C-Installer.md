title: 'VSCode 一键配置 C/C++环境程序'
author: Gaein nidb
categories:

- 电脑软件
  tags:
- 软件介绍
- 软件教程
  cover: 'https://s1.ax1x.com/2020/09/19/wIDpMd.md.png'
  date: 2020-09-18 15:17:26

---

VSCode 是轻量级的、跨平台的编辑器。相对于使用 DEVC++来说，使用 VSCode 开发 C/C++有很大的优势（至少它好看）。鉴于环境配置对新人不是很友好，我就写了一个一键安装程序。

<!-- more -->

# 发行说明

## 平台

适用于 Windows7/8/8.1/10 64 位操作系统

> 注意：其中 Windows7 版本需要安装.NET Framework 4.5.2 及以上版本

## 开发

使用 C# .NET Core 编写（其中 Windows7 版本使用.NET Framework）

## 版本

当前版本为 1.12.0

## 安装

1. Visual Studio Code 1.48.2
2. LLVM v10.0.0
3. mingw64-w64 8.1.0-release-posix-seh-rt_v6-rev0

## 插件

1. vscode-icons-team.vscode-icons
2. ms-vscode.cpptools
3. MS-CEINTL.vscode-language-pack-zh-hans
4. formulahendry.code-runner
5. HookyQR.beautify
6. zhuangtongfa.Material-theme
7. christian-kohler.path-intellisense
8. VisualStudioExptTeam.vscodeintellicode
9. esbenp.prettier-vscode

## 下载

[码云-gitee.com](https://gitee.com/nidb/vscode--c/releases)

## 项目

项目地址：[码云-gitee.com](https://gitee.com/nidb/vscode--c)

> VSCode 配置文件来自 中北大学·AI+移动互联创新实验室

# 使用方法

1. 下载文件
2. 双击运行后点击“开始”
3. 等待进度条满后进入文件夹“VSCode+C”
4. 双击文件“VSCode+C Installer.exe”，系统弹窗提示后点击是
5. 点击“开始安装”
6. 安装完成后会自动打开 VSCode，关闭安装程序。
7. 在 C:\VSCode+C\UserCode\C-Project 文件夹下创建 c 语言源代码文件；在 C:\VSCode+C\UserCode\C++-Project 文件夹下创建 c++源代码文件
8. 在文件夹内右键>在 code 中打开，或者在 VSCode 中点击文件>打开文件夹，然后浏览选择 C:\VSCode+C\UserCode\C-Project 或 C++的

# 开发过程

本来想打包 msi，后来无论怎么尝试都不行，干脆就使用 C#写一个安装器

## 安装实现

### VSCode

从官网下载安装包
VSCode 使用 Inno Setup，通过 Process 类中的方法调用其安装程序并传递参数，查阅 Inno Setup 的文档后，我使用的安装的命令行参数如下

```
/SP-                # 不显示“是否安装”
/VERYSILENT         # 超级静默安装（不弹窗）
/NOCANCEL           # 禁止取消安装
/NORESTART          # 不重启电脑
/CLOSEAPPLICATIONS  # 关闭影响安装的程序
/ALLUSERS           # 为所有用户安装
/DIR="C:\VSCode+C\Microsoft Visual Studio Code\"  #设置安装目录
/MERGETASKS="!runcode,desktopicon,quicklaunchicon,addcontextmenufiles,addcontextmenufolders,associatewithfiles,addtopath"
# 安装的任务：
# !runcode      安装完成后不启动vscode
# desktopicon   桌面图标
# quicklaunchicon     开始菜单
# addcontextmenufiles   文件右键菜单
# addcontextmenufolders 文件夹右键菜单
# associatewithfiles  关联文件
# addtopath     添加到环境变量
```

附：[Inno Setup 官方文档](https://jrsoftware.org/ishelp/topic_setupcmdline.htm)

MERGETASKS 的内容我一直不知道该怎么写（毕竟这个是编译 Inno Setup 前 VSCode 的开发者写的），但是我突然想到 VSCode 是开源的！于是我查阅了[GitHub 仓库](https://github.com/microsoft/vscode)。在分支 [joao/innosetup6](https://github.com/microsoft/vscode/tree/joao/innosetup6)中的 [build/win32/code.iss](https://github.com/microsoft/vscode/blob/joao/innosetup6/build/win32/code.iss)中找到了如下内容：

```
[Tasks]
Name: "desktopicon"; Description: "{cm:CreateDesktopIcon}"; GroupDescription: "{cm:AdditionalIcons}"; Flags: unchecked
Name: "quicklaunchicon"; Description: "{cm:CreateQuickLaunchIcon}"; GroupDescription: "{cm:AdditionalIcons}"; Flags: unchecked; OnlyBelowVersion: 0,6.1
Name: "addcontextmenufiles"; Description: "{cm:AddContextMenuFiles,{#NameShort}}"; GroupDescription: "{cm:Other}"; Flags: unchecked
Name: "addcontextmenufolders"; Description: "{cm:AddContextMenuFolders,{#NameShort}}"; GroupDescription: "{cm:Other}"; Flags: unchecked
Name: "associatewithfiles"; Description: "{cm:AssociateWithFiles,{#NameShort}}"; GroupDescription: "{cm:Other}"; Flags: unchecked
Name: "addtopath"; Description: "{cm:AddToPath}"; GroupDescription: "{cm:Other}"
Name: "runcode"; Description: "{cm:RunAfter,{#NameShort}}"; GroupDescription: "{cm:Other}"; Check: WizardSilent
```

顺利解决问题

### LLVM

从[官网](https://releases.llvm.org/)下载安装包，访问速度可能有些慢，可以考虑使用代理
LLVM 使用 NSIS（问了中北的学姐），通过 Process 类中的方法调用其安装程序并传递参数，查阅 NSIS 的文档后，我使用的安装的命令行参数如下

```
/S # 静默安装
/D="C:\VSCode+C\LLVM" # 设置安装目录
```

附：[NSIS 官方文档](https://nsis.sourceforge.io/Docs/Chapter3.html#installerusage)

### mingw64-w64

mingw64-w64 下载下来是一个压缩包，因此我们直接去解压文件就好
从[sourceforge](https://sourceforge.net/projects/mingw-w64/files/)下载 mingw64-w64
![sourceforge文件列表](https://s1.ax1x.com/2020/09/18/whTJL8.png)
在这么多文件里面下载 Toolchains targetting Win64>Personal Builds>mingw-builds>8.1.0>threads-posix>seh>x86_64-8.1.0-release-posix-seh-rt_v6-rev0.7z
（找对版本可真不容易）

### 代码文件夹和.vscode 配置

我使用的是中北大学 AI+移动互联创新实验室的配置文件
[GitHub 项目地址](https://github.com/android-nuc/VSCode-C-Environment)
下载、解压、提取需要的即可

### 安装方案

我的方案是：VSCode、LLVM 使用安装包安装，mingw64-w64 和代码文件夹配置等直接打包成 data.zip 然后解压缩。VSCode 插件调用命令行使用
`C:\VSCode+C\Microsoft Visual Studio Code\bin\code.cmd --install-extension 插件
由于国内网络原因某些插件安装的很慢或者根本无法安装

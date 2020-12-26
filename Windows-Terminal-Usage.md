title: '个性化你的Windows Terminal终端与PowerShell 7'
author: Gaein nidb
categories:
  - 电脑软件
tags:
  - 软件介绍
  - 软件教程
cover: 'https://s3.ax1x.com/2020/12/27/r4qygP.md.png'
date: 2020-12-27 1:43:18

---

使用Windows Terminal + PowerShell 7 打造超强终端，Windows Terminal 美化与 PowerShell 7 个性化教程。

<!--more-->

# 前言

> 总感觉 cmd、PowerShell 太丑。以前折腾过一阵子的 cmder 感觉也就一般。听学姐们吹了挺长时间的 Windows Terminal。今天试一下，确实效果非常好，美观好用。不得不说~~巨硬~~Microsoft 正经去做某个东西还是非常好的。

# 安装

这次主要有以下几个目的：

1. 使用 PowerShell Core 代替系统自带的 PowerShell 5；
2. 优化 PowerShell 字体、配色等；
3. 在 Windows Terminal 里面添加 python 控制台；
4. 合并 PowerShell、Git Bash、cmd 和 python 到 Windows Terminal 中，并取消其它的右键菜单，为 Windows Terminal 添加右键菜单。

## 安装 Windows Terminal

### 介绍

是一款新式、快速、高效且强大的终端应用程序。通过它可以使用命令提示符、PowerShell、WSL 等诸多 shell。Windows Terminal 支持多选项卡、窗格、Unicode 和 GPU 加速文本渲染引擎，同时也支持丰富多彩的用户自定义主题。
![wt宣传片](https://s3.ax1x.com/2020/12/27/r4qygP.png)

### 安装

1. 打开系统的 Microsoft Store；
2. 搜索"Windows Terminal"；
   ![商店界面](https://s3.ax1x.com/2020/12/27/r4qUBD.png)
3. 点击"安装"。

> 由于网络环境不同等待时间也可能不一样

菜单里出现 Windows Terminal，打开后如图所示即为安装完成。
![WT打开](https://s3.ax1x.com/2020/12/27/r4qQAJ.png)

## 安装 PowerShell Core

### 介绍

最初，Windows PowerShell 是在 .NET Framework 基础之上构建而成，仅适用于 Windows 系统。 在最新版本中，PowerShell 使用 .NET Core 3.1 作为运行时。 PowerShell 在 Windows、macOS 和 Linux 平台上运行。
新的 PowerShell 拥有更高的效率和诸多优点。
PowerShell Core 是 [GitHub](https://github.com/PowerShell/PowerShell) 上面的开源项目，使用 MIT 许可证。

### 安装

> 官方有详细的安装文档，见 [Microsoft DOCS](https://docs.microsoft.com/zh-cn/powershell/scripting/install/installing-powershell)

我使用下载-安装的方式而没有使用包管理器。

1. 从[GitHub 的 Release](https://github.com/PowerShell/PowerShell/releases/)中下载`PowerShell-7.1.0-win-x64.msi`；
   ![Release页面](https://s3.ax1x.com/2020/12/27/r4q1hR.png)
2. 下载完成后找到下载的文件夹，双击运行；

   > 如果提示 “不是有效的 WIN32 应用程序” 请返回刚才的 Release 页面下载`PowerShell-7.1.0-win-x86.msi`

3. 一直点击"Next"，直到安装完成，点击"Finish"。
   ![安装PSCore](https://s3.ax1x.com/2020/12/27/r4qlN9.png)

## 配置 PowerShell Core

### 准备

#### 安装模块

打开 PowerShell 7，输入以下命令：

```ps
Install-Module posh-git -Scope CurrentUser
```

```ps
Install-Module oh-my-posh -Scope CurrentUser
```

> 先输入一个命令，光标会移动到下一行的最左侧。这时候不要着急输入另一个，稍等一会如果出现提示那么就按`Y`。安装完成（左侧有显示"PS xxx"字样）再安装另外一个。两个不分先后。

#### 安装 PowerLine 字体

这一部分微软官方文档有写，详见 [Microsoft Docs](https://docs.microsoft.com/zh-cn/windows/terminal/tutorials/powerline-setup)

1. 打开 Cascadia Code [GitHub 发布页](https://github.com/microsoft/cascadia-code/releases)，下载最新的 Release `CascadiaCode-2009.22.zip`；
2. 下载完成后解压文件，进入`解压后的文件夹->ttf`，双击里面`.ttf`结尾的文件，安装。

### 配置

1. 从菜单栏打开安装好的 PowerShell 7。如果电脑安装有 Visual Studio Code 的话在里面输入以下内容：
   ```ps
   code $profile
   ```
   ![code效果](https://s3.ax1x.com/2020/12/27/r4q891.png)
   如果你没有安装或者不知道什么是 Visual Studio Code 的话输入以下内容：
   ```ps
   notepad $profile
   ```
   ![notepad效果](https://s3.ax1x.com/2020/12/27/r4qK74.png)
2. 输入完成后按下回车，VSCode（或记事本）将会打开一个文件（可能是空的），在里面输入以下内容后保存

   ```ps
   # by Gaein nidb , https://www.Gaein.cn

   Clear-Host                  # 清除屏幕

   $time =  Get-date -Format "现在是yyyy年MM月dd日 HH时mm分"
   $path = $pwd.path           # 获取路径
   if ( $path.split("\")[-1] -eq "Windows" || $path.split("\")[-1] -eq "System32") {
       # 默认路径为桌面
       $desktop = "C:\Users\" + $env:UserName + "\Desktop\"
       # 切换到桌面
       Set-Location $desktop
   }
   Write-Output $time
   $motdPath = "C:\Users\" + $env:UserName + "\Documents\PowerShell\motd.txt"
   Get-Content $motdPath   # 欢迎消息

   # 导入模块
   Import-Module posh-git
   Import-Module oh-my-posh

   # 设置主题
   Set-Theme PowerLine
   ```

   > 如果你想自定义的话建议先备份后修改。

3. 在`此电脑->文档->PowerShell`文件夹，点击`新建文本文档`，文件名为"motd.txt"，写入以下内容：

   ```txt
   PowerShell 7.1.0 (Windows10专业版 x64 20H2)

   * 终端: https://docs.microsoft.com/zh-cn/powershell/
   * 系统: https://support.microsoft.com/zh-cn/windows/
   * 指令: https://docs.microsoft.com/zh-cn/dotnet/api/

   欢迎使用PowerShell Core
   ```

   > 这里的内容是完全自定义的，算是欢迎消息，即打开 PowerShell 输出的一段话。你可以随便写。

## 配置 Windows Terminal

### 自定义设置

1. 打开 Windows Terminal，点击 `下箭头->设置` 或按下 `Win+,` 打开设置文件
   ![打开的setting.json](https://s3.ax1x.com/2020/12/27/r4qG1x.png)
2. 向设置文件写入以下内容：

   ```json
   // 文档: https://aka.ms/terminal-documentation
   // 默认设置在点击设置按钮时按住"Alt"键

   // by Gaein nidb, https://www.gaein.cn
   // rev 1.0.0

   {
     // 全局设置
     "$schema": "https://aka.ms/terminal-profiles-schema",

     "defaultProfile": "{574e775e-4f2a-5b96-ac1e-a2962a402336}", // 默认打开的
     "copyOnSelect": false, // 自动复制选中的内容到剪切板
     "copyFormatting": false, // 复制格式，将选中文字的颜色、背景等也复制
     "initialCols": 120, // 初始化大小（栏）
     "initialRows": 38, // 初始化大小（行）

     // 打开的选项的设置，文档: https://aka.ms/terminal-profile-settings
     "profiles": {
       "defaults": {
         // 默认设置
         "acrylicOpacity": 0.75, // 不透明度
         "closeOnExit": true, // 退出时关闭
         "colorScheme": "Campbell", // 配色方案
         "cursorColor": "#FFFFFF", // 颜色
         "cursorShape": "bar", // bar
         "fontFace": "Cascadia Code PL", // 字体
         "fontSize": 10, // 字号
         "historySize": 9001, // 历史记录
         "padding": "0, 0, 0, 0", // 内部显示位置
         "snapOnInput": true, // ?
         "startingDirectory": "./", // 打开目录
         "useAcrylic": true // 使用亚克力效果
       },
       "list": [
         {
           // PowerShell Core
           "guid": "{574e775e-4f2a-5b96-ac1e-a2962a402336}",
           "hidden": false,
           "name": "PowerShell Core",
           "commandline": "pwsh.exe -NoLogo"
         },
         {
           // Python Console
           "guid": "{de7c0f31-aa6b-c0f6-44a8-fe162d8f31fc}", // 随机生成一个guid即可
           "hidden": false,
           "name": "Python 3.9",
           "icon": "C:\\Users\\Gaein\\Documents\\WindowsTerminal\\python-logo.png", // 自定义python图标
           "commandline": "C:\\Program Files\\Python39\\python.exe" // python路径
         },
         {
           // 自带的旧版PowerShell
           "guid": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
           "hidden": false,
           "name": "PowerShell(旧版)",
           "commandline": "powershell.exe -NoLogo"
         },
         {
           // 命令提示符
           "guid": "{0caa0dad-35be-5f56-a8ff-afceeeaa6101}",
           "hidden": false,
           "name": "命令提示符(cmd)",
           "commandline": "cmd.exe"
         },
         {
           // 默认生成的Azure Cloud Shell
           "guid": "{b453ae62-4e3d-5e58-b989-0a998ec441b8}",
           "hidden": true,
           "name": "Azure Cloud Shell",
           "source": "Windows.Terminal.Azure"
         }
       ]
     },

     // Add custom color schemes to this array.
     // To learn more about color schemes, visit https://aka.ms/terminal-color-schemes
     "schemes": [],

     // Add custom actions and keybindings to this array.
     // To unbind a key combination from your defaults.json, set the command to "unbound".
     // To learn more about actions and keybindings, visit https://aka.ms/terminal-keybindings
     "actions": [
       // Copy and paste are bound to Ctrl+Shift+C and Ctrl+Shift+V in your defaults.json.
       // These two lines additionally bind them to Ctrl+C and Ctrl+V.
       // To learn more about selection, visit https://aka.ms/terminal-selection
       {
         "command": { "action": "copy", "singleLine": false },
         "keys": "ctrl+c"
       },
       { "command": "paste", "keys": "ctrl+v" },

       // Press Ctrl+Shift+F to open the search box
       { "command": "find", "keys": "ctrl+shift+f" },

       // Press Alt+Shift+D to open a new pane.
       // - "split": "auto" makes this pane open in the direction that provides the most surface area.
       // - "splitMode": "duplicate" makes the new pane use the focused pane's profile.
       // To learn more about panes, visit https://aka.ms/terminal-panes
       {
         "command": {
           "action": "splitPane",
           "split": "auto",
           "splitMode": "duplicate"
         },
         "keys": "alt+shift+d"
       }
     ]
   }
   ```

   > 可以按需进行修改

### 添加到右键菜单

1. `Win + R` 输入 `regedit` 打开注册表编辑器；
2. 转到`计算机\HKEY_CLASSES_ROOT\Directory\Background\shell`；
3. 新建项`Terminal`，将默认值修改为`在此处打开 Windows Terminal`；
   ![修改值](https://s3.ax1x.com/2020/12/27/r4qNnO.png)
4. [下载图标(Gaein.cn)](https://static.cdn.gaein.cn/files/images/WindowsTerminal.ico)，保存到某个路径；
5. 在`Terminal`里新建字符串，名字为`icon`，右键->修改，值为图标的路径；
   ![设置图标](https://s3.ax1x.com/2020/12/27/r4qaHe.png)
   ![设置图标路径](https://s3.ax1x.com/2020/12/27/r4qwAH.png)
6. 在`Terminal`新建项，名字为`command`，值为`wt.exe`
   ![命令](https://s3.ax1x.com/2020/12/27/r4qB4A.png)

# 参考

1. [Microsoft Docs:Windows Terminal](https://docs.microsoft.com/zh-cn/windows/terminal/)
2. [Microsoft Docs:从 PowerShell5 迁移到 7 ](https://docs.microsoft.com/zh-cn/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7.1)
3. [知乎 @tnnmigga:Windows Terminal 终端入门](https://zhuanlan.zhihu.com/p/144612614)
4. [简书 @码农小胖哥:Windows 命令行终端 PowerShell 美化计划](https://www.jianshu.com/p/95c5194857b7)

---
title: 在 Windows 下舒适的使用命令行
author: Gaein nidb
tags: 
  - Windows
  - 命令行
categories: [使用技巧]
date: 2022-11-09 10:26:22
---

## 前言

自从大一坐到 [AimerNeige](https://aimerneige.com/) 身边以来，就见到他的双系统 Linux。后来自己也装了一个 ubuntu server + i3wm 日常用（虽然后来因为臭打游戏的原因回 windows 了），不得不说 Linux 的命令行以及各种命令行工具用起来非常的爽啊，相比较而言，用 windows 几乎很少用到命令行，cmd 和 powershell 都很丑，而且默认字体对眼睛很不友好 :D。以及各种工具并不多导致我不怎么用。

## 美化

[个性化你的Windows Terminal终端与PowerShell](https://blog.gaein.cn/passages/Windows-Terminal-Usage/) 其实以前写过一篇文章来记录如何安装配置 Windows Terminal 和 Powershell 7（后改名 Powershell Core，现名 Powershell）（谢谢微软改名部）

至于字体方面选择了 Cascadia Code。

> 注意，oh-my-posh 需要使用 [Nerd Font](https://www.nerdfonts.com/) 字体，否则不能正常显示各种图标。

## 包管理

Linux 各种发行版通常有着一个包管理，pacman 也好、apt 也好、傻逼 snap 也好，总之有一个。Windows 这边没有一个中心化的包管理器，不管是微软的 win-get 还是第三方的 choco 和 scoop，都是直接从软件发行商那边进行下载的。不过我比较喜欢 scoop 的 portable 的思想，所以使用了 scoop 作为我的包管理器，配置各种环境非常舒适。

至于 scoop 的安装，参考其[官网](https://scoop.sh/)即可。顺带一提，scoop 的“软件源”在 Github 上，你可能需要某些技术来解决连接性问题。

当然，对于某些 scoop 内没有的软件包，可以使用 win-get 来安装。

### scoop 常用指令

`scoop help` 查看帮助
`scoop update` 更新软件源
`scoop update *` 安装所有包的更新
`scoop search <keyword>` 搜索包
`scoop info <package name>` 查看包的详细信息
`scoop cleanup *` 清理软件包

更多命令：自己看吧。

### 使用 scoop 配置环境

平时会用到各种语言的环境（虽然我是远程编译人但是本地还是有环境罢）

哦其实好像根本不用怎么配，你只需要：

`scoop install gcc`
`scoop install nodejs`
`scoop install python`

...

之类的。

怎么样，是不是非常爽？终于不用找到官网然后点 download 然后疯狂 next 了。

## CLI 工具

### 命令行美化

`oh-my-posh` —— prompt 美化
`lsd` —— 列出文件

### 网络工具

`nmap` —— 扫描
`tcping` —— 测试端口连接性
`clash` —— 代理分流
`wireshark` —— 抓包
`nexttrace` —— 路由追踪

### 系统管理

`btop` —— 资源任务管理
`neofetch` —— 让我看看（系统信息）

当然，还有很多例如 `ffmpeg` 这种多媒体处理、`7z`、 `tar` 这种压缩解压以及各种其它工具。

## 自定义脚本

bash 使用 `.bashrc` 作为用户启动 shell 时执行的脚本，而 powershell 则会执行一个 `.ps1` 脚本，这个脚本的位置可以保存在 Powershell 中名为 `$PROFILE` 的变量。

可以使用 `Write-Host $PROFILE` 来查看路径。

这个脚本在我那篇 Window Terminal 也提过。不过在那个玩意的基础上，我最近又添加了一些自己用的实用函数：

比如先对于“在 Windows Terminal 中打开” 的 “在文件资源管理器中打开”：

```pwsh
function Get-Exploere {
    param(
        [string]$Path = $PWD
    )

    explorer.exe $Path;
}

Set-Alias -Name open -Value Get-Exploere
```

用于测试命令是否存在：

```pwsh
function Test-Command {
    param(
        [string] $CommandName 
    )
    
    return $null -ne (Get-Command $CommandName -ErrorAction SilentlyContinue) 
}
```

当然还有很多别名，比如将 `ls` 改为使用 `lsd`：

```pwsh
function Get-Item-Line {
    lsd -lh
}


if (Test-Command lsd) {
    Set-Alias -Name ls -Value lsd
    Set-Alias -Name ll -Value Get-Item-Line
}
```

除此之外，为 ffmpeg 的常见操作也写了一些函数：

比如如下代码可以将图片转为 `webp` 格式并且调整大小。

```pwsh
function Get-Webp {
    param(
        [string] $FileName,
        [int] $Width = -1,
        [int] $Height = -1
    )

    $file = Get-Item $FileName;

    if (($Width -ne -1) -and ( $Height -ne -1) ) {
        ffmpeg -i $file.FullName $file.Name.Replace($file.Extension, ".webp");
    }
    else {
        ffmpeg -i $file.FullName -vf scale=${Width}:${Height} $file.Name.Replace($file.Extension, ".webp");
    }
}
```

另外，powershell 中 `mv`、`ls` 等命令的用法和 Linux 下的 shell 极为相似，上手也很快。

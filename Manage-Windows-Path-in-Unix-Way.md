---
title: 以类Unix的方法管理Windows的Path环境变量
author: Gaein nidb
categories:
  - 软件
tags:
  - Windows
  - Linux
date: 2022-02-01 23:21:28
---

首先恭祝大家新春快乐！

## 前言

事情的起因是这样的，我 surface 上装了一张 TF 卡作为存储盘，年头重装家里系统的时候莫名其妙格式化了 TF 卡（~~选错了盘的屑~~）。想到很多环境都在 TF 卡里，surface 这个系统也用了一年半了，索性就重装然后再配环境。

有很多类似于 `git` 这种命令行软件，需要在环境变量的 `PATH` 中加入它的目录（比如 `D:\Tools\Git\cmd\`）才能方便的执行。如果每个软件都这样的话，`PATH` 中势必会有一大坨，很混乱也难于管理。

类 Unix 系统中部分软件的做法是，把这些文件软链接到 `/usr/bin/` 目录下。那么我们是否可以在 Windows 系统中采取类似的方法，创建一个目录并添加到 `PATH` 中，然后将原本需要加入 `PATH` 的可执行文件软链接到该目录。

另外，这样做以后再加入别的文件时，只需要创建软链接，不需要更改环境变量也不需要重启 Terminal 了。

## .cmd 不可行

一开始我尝试使用类似于 `git.cmd` 然后里面写入 `D:\Tools\Git\cmd\git.exe` 这种方法，但是显然，我无法传递未知数量的参数给真正的 `git.exe` （我没有深入研究传递参数的相关知识，显然这不是一个优雅的方法）。

## mklink 创建软链接

> Powershell 中没有提供 `mklink` 对应的 Cmdlet 别名，也就是说当你无法在 Powershell 中使用 `mklink` 命令。你需要使用 cmd.exe 来执行这个命令。

> 关于 Powershell 创建软链接，请参考下文的 `New-Item` 。

参阅 [mklink | Microsoft Docs](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/mklink)

### 创建文件的软链接

使用

```cmd
mklink <link> <target>
```

其中，`link` 是创建的链接，`target` 是被创建链接的文件，比如：

```cmd
mklink git.exe D:\Tools\Git\cmd\git.exe
```

在当前目录下创建了一个 `git.exe` 指向了在 `D` 盘的 `git.exe` 文件。

### 创建目录的软链接

不同于 Linux 操作系统，Windows 操作系统创建指向目录的软链接需要额外添加参数，使用

```winbatch
mklink /d <link> <target>
```

## New-tem 创建软链接

既然有新的，更加好用的 Powershell ，那么当然要用 Powershell 啦。

如上文所说，Powershell 并没有设置别名为 mklink 的命令，创建软链接这一行为被 Powershell 视为**创建一个类型为 `SymbolicLink` 的值为 目标文件路径 的新文件**

因此，我们只需要使用 `New-Item` 即可，使用方法如下：

```powershell
New-Item <link> -ItemType SymbolicLink -Value <target>
```

使用 `New-Item` 创建目录无需添加额外的参数。

参阅 [New-Item (Microsoft.PowerShell.Management) - PowerShell | Microsoft Docs](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/new-item?view=powershell-7.2#example-7--create-a-symbolic-link-to-a-file-or-folder)

> 注意：Windows 无法在 TF 卡上创建软链接文件，但是你可以将文件链接到 TF 卡上的文件。


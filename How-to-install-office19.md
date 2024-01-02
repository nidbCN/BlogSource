---
title: Office 2019安装（附激活）
author: Gaein nidb
categories:
  - 软件介绍
tags:
  - Office
  - 软件教程
date: 2020-02-28 09:29:34
---

闲来无事安装了Office2019，激活软件是从 HWIDGen 作者的 Github 上面拉下来的（顺手汉化了一下），KMS激活，180天

> 2021.5.27 作者 GitHub 已经没了

> 其实网上貌似有很多类似的，但是大多数都不怎么“讲究”，再者下载的激活软件很多不能用或者有病毒，虽然我不能保证我提供的激活100%安全，但是我还是选择相信HWIDGen的开发者。

## 下载Office2019

**注意，office2019仅支持Windows10系统，如果想要知道您的操作系统是什么，可以在属性中查看，或者直接看左下角开始菜单的标志，如果是“开始”，则为Windows XP系统，原型的Windows标志是Windows7系统，四方格标志是Windows10系统（不一定准确，但是大多没问题！）**

如果您使用的是Windows7操作系统可以安装Office2016，不可以安装Office2019

如果您使用的是WindowsXP操作系统那么放弃吧，转WPS吧

下面是Office2019和Office2016的下载链接，来自 [MSDN，我告诉你](https://msdn.itellyou.cn)

* Office2019 : 
  `ed2k://|file|cn_office_professional_plus_2019_x86_x64_dvd_5e5be643.iso|3775004672|1E4FFA5240F21F60DC027F73F1C62FF4|/ `
* Office2016 : 
  `ed2k://|file|cn_office_professional_plus_2016_x86_x64_dvd_6969182.iso|2588266496|27EEA4FE4BB13CD0ECCDFC24167F9E01|/ `

可以使用迅雷等工具进行下载，复制“ed2k:.....|/”并粘贴即可

## 解压安装

![3BG4oD.png](https://s2.ax1x.com/2020/02/28/3BG4oD.png)

我将文件下载到了D:\安装包

右键下载来的文件（它通常在D:\迅雷下载里面 根据你设置的路径而定（在复制完链接下载之前有个对话框，里面有选择的）如果找不到可以打开迅雷，通常在已完成>你下载的文件边上有个文件夹的标志，点击它可以打开文件夹）

右键之后选择“解压到...”，如果没有这个选项可以选择“解压到当前文件夹”，如果都没有可以去安装压缩软件（推荐Bandizip，详见 [这一篇博客](https://blog.gaein.cn/passages/Open-File-in-right-way/)），或者右键>挂载（仅限于Windows10操作系统）

打开解压出来的文件夹（或者挂载的盘，他们通常是最后一个盘符）

![3BGhdO.png](https://s2.ax1x.com/2020/02/28/3BGhdO.png)

如果你希望自动安装，可以双击setup.exe（Office2019是默认安装64位，2016默认安装32位；希望手动安装可以进入office目录，安装相应版本，推荐64位）

**如果你听不懂小括号里面是啥就直接双击即可**

**如果你准备安装Office2016，而且觉得自己电脑配置还行的话就进入Office目录，双击“setup-64.exe”（或者类似的名字）如果无法安装再双击“setup.exe”**

![3BGfeK.png](https://s2.ax1x.com/2020/02/28/3BGfeK.png)

![3BGIFe.png](https://s2.ax1x.com/2020/02/28/3BGIFe.png)

安装过程是全自动，你可以坐下来歇会，大概5分钟左右（Microsoft surface pro3），好一点的电脑应该会更快，~~不过也不排除我电脑有微软本家加成~~（如果你听不懂我在说什么等着就好）

![3BGTWd.png](https://s2.ax1x.com/2020/02/28/3BGTWd.png)

如图即为安装成功
 
## 更改安全设置

如果您希望白嫖这款软件，后面的步骤是必须的。如果您希望支持微软可以去官方购买Office2019激活密钥或者淘宝购买

在开始下面的操作之前可以新建一个文件夹，比如G:\工具。稍后我们的激活操作将在这里面进行，请确保您能够找到此文件夹

1. 打开安全中心（如果您使用其它软件可以尝试直接进行下一步操作，再杀毒软件报告含有病毒时选择允许就可以了）
   **激活软件由于涉及到敏感操作，会被判定为病毒**
   
   ![3BGHSA.png](https://s2.ax1x.com/2020/02/28/3BGHSA.png)

2. 点击管理设置
   ![3BGbQI.png](https://s2.ax1x.com/2020/02/28/3BGbQI.png)
3. 添加或删除排除项
   ![3BGqyt.png](https://s2.ax1x.com/2020/02/28/3BGqyt.png)
4. 添加排除项>文件夹。选择我们刚才新建的文件夹G:\工具

## 激活Office
首先下载激活软件

该软件和知名的系统数字权利激活软件HWIDGen为同一人开发，我选择相信软件开发者

注意，在网上找到的激活软件仍然有含有病毒的可能性。我提供的软件是从作者的Github（源码仓库）获取的

暂时不可用 ~~点击此处下载: https://static.cdn.gaein.cn/files/tools/ActivateTools/激活office.zip~~

（我已经将它翻译为了中文，当然还是混有部分英文的。不过以下的图片是全英文的）

1. 解压到文件夹
   ![3BGLOP.png](https://s2.ax1x.com/2020/02/28/3BGLOP.png)
2. 进入“v36”
   ![3BGjw8.png](https://s2.ax1x.com/2020/02/28/3BGjw8.png)
3. 右键，以管理员身份运行
   ![3BGzFg.png](https://s2.ax1x.com/2020/02/28/3BGzFg.png)
4. 出现此窗口，然后稍等片刻（大概有一两分钟吧）
   ![3BJSYQ.png](https://s2.ax1x.com/2020/02/28/3BJSYQ.png)
   
   程序会自动检查未激活的Windows系统和Office并激活他们。此时如果杀毒软件弹出任何禁止的东西，请选择允许运行
   
   ![3BJpWj.png](https://s2.ax1x.com/2020/02/28/3BJpWj.png)

   出现到此即为成功（汉化之后的会有激活成功字样）
   
   ![3BriZj.png](https://s2.ax1x.com/2020/02/28/3BriZj.png)

（附汉化版图片）
5. 打开Office（word excel都行），弹窗，点击接受
   ![3BJCSs.png](https://s2.ax1x.com/2020/02/28/3BJCSs.png)
6. 至此Office激活完成!
![3BJPln.png](https://s2.ax1x.com/2020/02/28/3BJPln.png)

**安装的是初始版本，可以点击更新进行更新**
![3BJiyq.png](https://s2.ax1x.com/2020/02/28/3BJiyq.png)

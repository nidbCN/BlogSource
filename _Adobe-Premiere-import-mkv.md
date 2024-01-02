---
title: 【过期内容】Adobe Premiere 导入 mkv 格式的视频
author: Gaein nidb
categories:
  - 软件教程
tags:
  - 视频剪辑
  - 软件教程
  - 工具
  - 杂记
date: 2021-05-12 22:32:26
---

> 注意：此内容已过期。Adobe Premiere 不能导入的本质原因的 PR 2020 前的版本不支持 HEVC/H.265 编码。旧版本使用 ffmpeg 进行转码即可正常使用，新版本没有此问题。

## mkv

mkv格式本身是一个“容器”，它包括了视频、音频、章节等，其实，不需要转码，只需要把需要的视频和音频“拆”出来即可。

## 工具

### MKVToolNix

该工具用于操作mkv文件（虽然我们只需要其中的解包部分）

* 官网：https://mkvtoolnix.download/
* Windows下载：https://www.fosshub.com/MKVToolNix.html

### MKVExtractGUI2

图形化界面的解包mkv的程序，需要放置在 MKVToolNix 的根目录下。

目录结构如下：

![Dir](https://img.cdn.gaein.cn/website_used/blog/AdobePr-import-mkv/01.webp)

* sourceforge：https://sourceforge.net/projects/mkvextractgui-2/

## 提取音视频

可以直接双击点开 `MKVExtractGUI2` ，选择 `input` ，下面的框中将显示出来 Tracks，选择需要的然后导出即可。

> 可惜我的并不能用，它什么也不显示，只能手动来一波了。

## 提取音视频（命令行）

实际上 MKVExtractGUI2 也是调用的 MKVToolNix 命令行来提取的，而 MKVToolNix 的图形化又比较复杂，不会用。直接使用命令行甚至会方便一些。

在当前目录打开 powershell 后，输入：

`.\mkvinfo.exe "<mkv文件路径>"` ，比如我这个是 `.\mkvinfo.exe "C:\Users\Gaein_nidb\Videos\High School Fleet [01].mkv"`

![Output](https://img.cdn.gaein.cn/website_used/blog/AdobePr-import-mkv/02.webp)

以VCB-S压制的 High School Fleet/高校舰队 为例，程序会输出一大坨mkv的信息，我们需要关注的为 `Tracks` ，其中的每一个 `Track` 为一条轨道，其中包括：

* `Track number`：轨道ID（在mkvextract工具中轨道ID从0开始）
* `Track type`：轨道的类型（视频或音频等）
* `Codec ID`：编码ID

> 以上均用 "//" 做了伪注释

以及一些其它重要信息

```
|+ Tracks
| + Track   // 第一条轨道
|  + Track number: 1 (track ID for mkvmerge & mkvextract: 0)    // 在mkvextract工具中轨道ID为0
|  + Track UID: 16171542915234837125
|  + Track type: video  // 视频
|  + "Lacing" flag: 0
|  + Minimum cache: 1
|  + Codec ID: V_MPEGH/ISO/HEVC // 格式为HEVC
|  + Language: und
|  + Video track
|   + Pixel width: 1920
|   + Pixel height: 1080
|   + Display width: 1920
|   + Display height: 1080
|  + Codec's private data: size 1151 (HEVC profile: Main 10 @L4.1)
|  + Default duration: 00:00:00.041708333 (23.976 frames/fields per second for a video track)
| + Track   // 第二条轨道
|  + Track number: 2 (track ID for mkvmerge & mkvextract: 1)    // ID为1
|  + Track UID: 11899936184913956278
|  + Track type: audio  // 音频
|  + Codec ID: A_FLAC   // 格式为flac
|  + Codec's private data: size 113
|  + Default duration: 00:00:00.085333333 (11.719 frames/fields per second for a video track)
|  + Language: jpn
|  + Audio track
|   + Sampling frequency: 48000
|   + Channels: 2
|   + Bit depth: 24
| + Track   // 第三条轨道
|  + Track number: 3 (track ID for mkvmerge & mkvextract: 2)    // ID为2
|  + Track UID: 10563629169812897935
|  + Track type: audio  // 音频
|  + "Default track" flag: 0
|  + Codec ID: A_AAC    // 格式为ACC
|  + Codec's private data: size 2
|  + Default duration: 00:00:00.021333333 (46.875 frames/fields per second for a video track)
|  + Language: jpn
|  + Audio track
|   + Sampling frequency: 48000
|   + Channels: 2
```

那么我们需要提取的有：

1. HEVC视频，轨道0
2. FLAC音频，轨道1

当然你想要ACC不要FLAC也行，咋都行，看需求而来

使用 `mkvextract` 工具来解mkv文件，输入命令：

`.\mkvextract.exe "<mkv文件>" tracks 0:video.hevc 1:audio.flac`

其中，tracks后面的参数为 `<轨道ID>:<输出文件>` ，根据你想解的轨道，输入上面看信息看到的轨道ID即可。

然后，在这个文件夹中，我们得到了 `video.hevc` 和 `audio.flac` 两个文件。

## 导入PR

尽管我们已经解开了，但是仍然不能导入，PR会说不支持的文件格式。

对于视频来说，解决的方法很简单：更改后缀名为PR能识别的，比如 `mp4`

对于flac音频来说：更改后缀名为 `wav`
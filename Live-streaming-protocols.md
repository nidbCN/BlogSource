---
title: '直播推流常见协议及网页端实现 RTMP/STR/FLV/HLS/WebRTC'
author: Gaein nidb
categories:
  - 服务器运维
tags:
  - Linux
  - 运维
  - 软件教程
date: 2023-07-11 19:05:15
image: "https://img.cdn.gaein.cn/website_used/blog/Live-streaming-protocols/cover.webp"
---

## 前言

最近一个月在叔叔那直播动画，但是被封了。寻思自己随便找个服务器播着玩，搭了个开源的又对延迟什么的不满意，于是走上了折腾这一车直播协议的~~不归路。~~

## 前置：编码、编码器、格式与容器？

> 心里不是特别有底，可能有部分地方有疏漏或错误之处，还请谅解。

### 编码(encode)/解码(decode)

我们说到多媒体时候，经常说什么 `mp4` 辣，`avi` 辣，同时又经常听到什么 `H.265`、`H.264` 这种“奇怪”的词。

编码代表了如何去将视频变成二进制，解码代表了将编码出来的二进制在变成视频。

为什么要编码呢？以一个 YUV 4:2:2 像素格式的 1080p 60FPS 视频为例，每秒的大小为 `1920*1080*2*60=237Mb` ，一个俩小时的电影就是小俩TB... 这显然是不可接受的。编码在方便视频存储的同时根据相邻的帧相似的地方等进行压缩。

~~什么？你说怎么实现？他很厉害，他不是一个有形的。所以你读《心经》，《心经》里面讲什么...~~

### 编(encoder)解(decoder)码器(codec)

此前说的编码是一种规范或者说算法，比如说 `H.264` 编码，要先这样再那样再那样，诶就好了。

编解码器(codec)就是具体干这个的。

比如 `H.264` 编码常见的编解码器有 `libx264`、`nvenc` 等等。

如果你使用 `ffmpeg` 的话，不妨输入 `ffmpeg -codecs` 来看看 `ffmpeg` 支持的那些编解码和器。

### 格式(format)

现在编码器已经可以根据编码把视频变成一大堆二进制了，如何存储他们？

显然，直接将这一坨二进制写入磁盘不太合理，这样的话谁知道他用的什么编码，该如何解码？另外一个多媒体文件还可能会有音频，怎么区分开这一坨二进制里面的视频和音频？字幕怎么办？分章节的怎么办？

显然，问题太多了，所以才有了格式，以比较简单的 avi 为例：

avi 嘛，全称是音频视频交错格式，这玩意就是一截音频，一截视频，一截音频，一截视频...

而 flv 是一个 header（标识有下面的内容有多少个字节之类），然后是一个tag，一个二进制，一个tag，一个二进制...这种能够读一点、放一点的格式就很利好直播（毕竟有些格式二进制堆在一起，你放个电影得全下完才能播）

> 注意，以上举例经过简化，实际格式比起文章内缩写复杂。这样写方便大家理解，如果需要了解这些格式的细节请自行查阅资料。

### 容器(container)

很多时候“容器”与“格式”用的一个名字，比如 `flv`、`mp4` 等，容器便是你能看到的文件。容器可能会支持多种格式，比如著名的 `Matroska` ，支持几乎所有常见的格式。容器内除了多媒体内容外，还有一些 metadata（章节信息、标题等）。

## 都有那些直播用协议？

### 经典推拉流协议：RTMP/RTSP

对于如何做直播流这件事，最简单也是最好办的方法就是——

#### RTMP

RTMP 全名为 `Real Time Messaging Protocol`，开发后被 Adobe 收购。

目前在各种领域应用广泛，几乎所有的直播平台都支持 RTMP 协议推流。

RTMP 采用 TCP 连接，优点是稳定、延迟低。

RTMP 采用经典广泛的 H.264 和 AAC，格式为 flv。

缺点也很简单：现代浏览器不能使用 RTMP 拉流。也就是说浏览器不能直接播放 RTMP 直播流。

目前通常只使用 RTMP 推流，各种直播客户端&APP可以使用 RTMP 拉流播放。

#### RTSP

RTSP 虽然名字和 RTMP 很像，但并不是一家的。RTSP 的特点是：使用 TCP 发送控制请求、使用 UDP 传输媒体数据。其使用 RTP 协议分视频块传输。

有点是嘎嘎低延迟，但是在 Android 和 iOS 上似乎没有开箱即用的库。

广泛应用于监控领域。

### 基于HTTP的拉流协议：HTTP-Flv/HLS/MPEG-DASH

由于现代浏览器并没有直接办法发起 TCP 请求等（以前 RTMP 使用类似于 Java 浏览器插件等方式工作，但是由于安全原因现代浏览器并不支持这么干），所以需要将 RTMP 直播流进行简单的处理，将其变成浏览器能拿到的。

#### HTTP-Flv

前文介绍过 Flv，它文件体内除了头，存储媒体信息的是一小段、一小段的，也就是说你不需要下载整个文件，就可以解码出其中的、你已经获取到的音视频。这种特性使得 flv 非常适合做直播。

并且，由于以前 flash 插件的高流行度，使得使用 HTTP 获取 flv 文件边获取边播放的方式更加流行了。

HTTP-Flv 相比较与其它方法来说延迟不高，但是缺点是——**现代浏览器不支持 flash player 辣！**

噔噔咚，然后你就不能放 `flv` 了。HTML5 原生支持的只有 `webm` 和 `mp4` 等。

解决办法总是有的嘛，还记得在 flash player 停止支持前 bilibili 的 `HTML5 播放器` 吗？没错，叔叔给他开源出来了，而且协议是 Apache-2.0。

##### 叔叔怎么解 flv 的？

既然浏览器不支持 flv，那么叔叔怎么做的 `flv.js` 呢？难道是摁写 `js` 或者用 `wasm` 解析 flv 然后解码然后在浏览器上画？

当然不是。有一个神奇的东西叫做 `MSE` ，`video` 标签不是只支持 `mp4` 吗？那我把 `flv` 变成小 `mp4` 总行了吧！Media Source Extension 支持在代码中构建 `MediaSource` 对象并将其作为 `src` 传递给 `video` 标签。

简而言之，你只需要把东西变成 `mp4` 格式，不需要自己解码，然后交给浏览器就好了！

当然，对于 `h.265/HEVC` 这种浏览器不支持的，叔叔那真就拿 `wasm` 开了一堆线程搁用户浏览器上软解，前阵子骂的沸沸扬扬的。

flv.js 项目对于该实现的原文：

> flv.js works by transmuxing FLV file stream into ISO BMFF (Fragmented MP4) segments, followed by feeding mp4 segments into an HTML5 <video> element through Media Source Extensions API.

于是，有了叔叔的帮助，我们就可以在浏览器播放 flv 了，当然也可以看直播了。

另外，注意 flv.js 项目首页上写道的：

> For FLV live stream playback, please consider mpegts.js which is under active development.
> 
> This project will become rarely maintained.

推荐我们使用 `mpegts.js` 来播放直播流，该项目开发者也是 `flv.js` 开发者之一，其对直播进行了优化。

#### HLS

HLS 全程是 `HTTP Live Stream` ，是水果公司整的。

其具体工作原理是先获取一个列表文件，然后挨个请求里面的 `ts` 文件挨个播放。

目前来说 HLS 支持在 IOS、Safari 及安卓上最新版本的浏览器/Chrome 浏览器。通过 js 也可以在其它现代浏览器上播放。

目前可以播放 HLS 的 JS 播放器有：

1. [hls.js](https://github.com/video-dev/hls.js)

#### MPEG-DASH

DASH 的意思是 “基于 HTTP 的动态自适应流”，DASH 技术很新，设计的很棒，其原理也是 XHR 请求视频数据块，再添加入 `video` 标签。

不过据 SRS Github issue 说，DASH 的设计很棒有点“想得很美”的意思，似乎协议写的不是特别清除，实现的有些迷糊。

目前可以播放 MPEG-DASH 的播放器有：

1. [dash.js](https://github.com/Dash-Industry-Forum/dash.js)
2. [shaka-player](https://github.com/shaka-project/shaka-player)

### 新技术：WebRTC与SRT

除了以上几种经典方案外，近年来出现了 WebRTC 和 SRT 协议。

#### WebRTC

WebRTC 是一项实时通讯技术，建立点对点连接并实现任意数据传输。适用于浏览器的摄像头直播会议等场景。

WebRTC 算是一种较新的技术。目前看来其前景广阔。但是经过测试不知道是 SRS 问题抑或是 WebRTC 问题，推流直播效果不够好。

#### SRT

SRT 为了解决 RTMP 的问题（旧、延迟高等），开发出了基于UDT（UDP的传输协议）的新协议。

特点是快速连接以及按照时间发送音视频数据等。

小缺点是 URL 不太容易看懂，另外就是其拥塞控制有些简单。

## 使用 SRS 接收 RTMP/SRT 推流

### 推流工具

在b站直播，我们可以使用b站直播只因等工具。当然，大家也注意到了，有很多up主或者其它平台在使用 OBS Studio 这款软件。

#### OBS Studio

[Open Broadcaster Software | OBS](https://obsproject.com/)

OBS Studio 更合适一般在电脑上的直播（比如各种游戏直播），可以实时看到反馈，使用方便。

#### ffmpeg

在无GUI环境下，更适合用 ffmpeg 进行推流。

ffmpeg 适合无人值守直播（比如7x24的动画片之类），易于编程和调用，性能要求低。

## 浏览器端实现拉流播放 HTTP-FLV/HLS/DASH

### DPlayer

Dplayer 是 DIYGOD 开发的播放器。支持多种媒体，且可以通过自定义 type 来支持其它 MSE 播放器来实现各种协议的视频播放。为了风格统一，使用了该播放器来做直播播放。

### 编码实现

首先先在 vue 上初始化 Dplayer 播放器：

```vue
<template>
  <div ref="playerContainer"></div>
</template>

<script setup lang="ts">
import {computed, onMounted, onUpdated, ref} from "vue";
import createVideo from "@/utils/videoEngine";
import DPlayer, {DPlayerDanmaku} from "dplayer";
import {options} from "@/utils/request";

const props = defineProps<{
  streamName: string
}>();

const player = ref({} as DPlayer);
const srsAddress = ref(options.serverAddress);
const playerContainer = ref();

const videoItem = computed(() =>
  createVideo(liveType => liveType.flvJs,
    srsAddress.value,
    props.streamName)
);

onMounted(() => {
  player.value = new DPlayer({
    container: playerContainer.value as HTMLElement,
    live: true,
    autoplay: true,
    theme: "#F19EC2",
    video: videoItem.value
  });

  player.value?.play();
});

// 当源地址值更新时，切换视频。
onUpdated(() => {
  player.value?.pause();
  player.value?.switchVideo(videoItem.value, {} as DPlayerDanmaku);
  player.value?.play();
});

</script>
```

其中，`createVideo` 方法是自己编写的工具方法，用于生成含有对应 `customType` 的 `video` 对象。

```ts
import mpegts from "mpegts.js";
import Mpegts from "mpegts.js";
import FlvJs from "flv.js";
import flvjs from "flv.js";
import Hls from "hls.js";
import dashjs, {MediaPlayerClass} from "dashjs";

enum MesPlayerName {
  HttpFlvByMpegTsJs = "mpegTsJs",
  HttpFlvByFlvJs = "flvJs",
  HLSByHlsJs = "hlsJs",
  DASHByDashJs = "dashJs",
  DASHByShaka = "dashShaka"
}

interface IMsePlayerInfo {
  name: MesPlayerName;
  player: any;
  extension: string;
  title: string;

  // 创建播放器
  createPlayer(video: HTMLVideoElement): void

  // 加载直播流
  loadPlayer(video: HTMLVideoElement): void

  hasStream(): boolean

  // 卸载直播流
  unloadPlayer(): void
}

const _mesPlayerList: Array<IMsePlayerInfo> = [
  {
    name: MesPlayerName.HttpFlvByMpegTsJs,
    player: null as Mpegts.Player,
    extension: "flv",
    title: "HTTP-FLV(mpegts.js)",
    createPlayer(video: HTMLVideoElement) {
      const streamUrl = video.src;

      this.player = mpegts.createPlayer({
        type: 'flv',
        isLive: true,
        url: streamUrl,
      });
    },
    loadPlayer(video: HTMLVideoElement) {
      console.log(`[videoEngine,flvJs,unloadPlayer]detach and unload mpegts.js player.`)

      this.player.attachMediaElement(video);
      this.player.load();

      this.player.on(mpegts.Events.LOADING_COMPLETE, () => {
        this.player.play();
      });
    },
    unloadPlayer() {
      console.log(`[videoEngine,flvJs,unloadPlayer]detach and unload mpegts.js player.`)

      this.player.detachMediaElement();
      this.player.unload();
    }, hasStream() {
      return this.player.isPlaying();
    }
  },
  {
    name: MesPlayerName.HttpFlvByFlvJs,
    player: null as FlvJs.Player,
    extension: "flv",
    title: "HTTP-FLV(flv.js)",
    createPlayer(video: HTMLVideoElement) {
      const streamUrl = video.src;

      this.player = flvjs.createPlayer({
        type: 'flv',
        isLive: true,
        url: streamUrl,
      });
    },
    loadPlayer(video: HTMLVideoElement) {
      console.log(`[videoEngine,flvJs,unloadPlayer]detach and unload flv.js player.`)

      this.player.attachMediaElement(video);
      this.player.load();
    },
    unloadPlayer() {
      console.log(`[videoEngine,flvJs,unloadPlayer]detach and unload flv.js player.`)

      this.player.detachMediaElement();
      this.player.unload();
    }, hasStream() {
      return this.player.isPlaying();
    }
  },
  {
    name: MesPlayerName.HLSByHlsJs,
    player: null as Hls,
    extension: "m3u8",
    title: "HLS(hls.js)",
    createPlayer(video: HTMLVideoElement): any {
      this.player = new Hls();
    },
    loadPlayer(video: HTMLVideoElement) {
      const streamLink = video.src

      this.player.loadSource(streamLink);
      this.player.attachMedia(video);
    },
    unloadPlayer() {
      console.log(`[videoEngine,hlsJs,unloadPlayer]nothing todo with hls.js player.`)
    },
    hasStream() {
      return this.player.isLevelLoaded(0);
    }
  },
  {
    name: MesPlayerName.DASHByDashJs,
    player: null as MediaPlayerClass,
    extension: "mpd",
    title: "MPEG DASH(dash.js)",
    createPlayer(video: HTMLVideoElement): any {
      this.player = dashjs.MediaPlayer()
        .create()
    },
    loadPlayer(video: HTMLVideoElement) {
      const streamLink = video.src

      this.player.initialize(video, streamLink, false);
      this.player.updateSettings({streaming: {delay: {liveDelay: 10}}});
    },
    unloadPlayer() {
      this.player.reset();
      console.log(`[videoEngine,hlsJs,unloadPlayer]nothing todo with hls.js player.`)
    },
    hasStream() {
      return true;
    }
  }
]

export function getPlayerInfo(playerName: MesPlayerName): IMsePlayerInfo | undefined {
  const playerInfo = _mesPlayerList.find(player => player.name == playerName);
  if (!playerInfo) {
    console.log(`[videoEngine,getPlayerInfo]can not find player named ${playerName}.`);
  }

  return playerInfo;
}

export default function createVideo(playerName: MesPlayerName, srsAddress: string, streamName: string) {
  const playerInfo = _mesPlayerList.find((player) => player.name == playerName);
  if (!playerInfo) {
    console.log(`[videoEngine,createVideo]can not find player${playerName}.`);
    return;
  }

  const streamUrl = `//${srsAddress}:8080/live/${streamName}.${playerInfo.extension}`;

  return {
    url: streamUrl,
    type: "streamPlayer",
    pic: "./PM5644.webp",
    customType: {
      streamPlayer: (video: HTMLVideoElement) => {
        if (!playerInfo.player) {
          playerInfo.createPlayer(video);
        } else if (playerInfo.hasStream()) {
          playerInfo.unloadPlayer();
        }

        playerInfo.loadPlayer(video);
      }
    }
  }
}
```

完整实现见：[live_room](https://github.com/nidbCN/live_room)

## 参考

1. [指南 | DPlayer](https://dplayer.diygod.dev/zh/guide.html#mse-%E6%94%AF%E6%8C%81)
2. [实时传输 Web 音频与视频 - Web 开发者指南 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/Guide/Audio_and_video_delivery/Live_streaming_web_audio_and_video)
3. [video-dev/hls.js: HLS.js is a JavaScript library that plays HLS in browsers with support for MSE.](https://github.com/video-dev/hls.js)
4. [Dash-Industry-Forum/dash.js: A reference client implementation for the playback of MPEG DASH via Javascript and compliant browsers.](https://github.com/Dash-Industry-Forum/dash.js)
5. [shaka-project/shaka-player: JavaScript player library / DASH & HLS client / MSE-EME player](https://github.com/shaka-project/shaka-player)
6. [xqq/mpegts.js: HTML5 MPEG2-TS / FLV Stream Player](https://github.com/xqq/mpegts.js)
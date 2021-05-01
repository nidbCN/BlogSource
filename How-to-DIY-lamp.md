---
title: "用废显示器自制充电柔和吸顶灯"
author: Gaein nidb
categories:
  - DO IT YOURSELF
tags:
  - DIY
  - 手工
cover: "https://s2.ax1x.com/2019/07/16/Z7aRfJ.jpg"
date: 2019-07-16 08:22:58
---

年前呢，在 b 站上看见有牛人拆了坏电视来做一盏效果超强、超棒的吸顶灯，而且成本也很低。看着就手痒痒。加上学长刚好有一个坏的屏幕，宿舍晚上断电需要照明，所以就干脆弄一个试试。

<!-- more -->

> 制作灵感来自视频：[哔哩哔哩:DIY-Perks——把坏掉的电视改造成日光灯，效果秒杀吸顶灯！](https://www.bilibili.com/video/BV1xp4y1s7WY)

> 参照这个视频来进行制作

# 材料

1. 坏的显示器面板，不显示、碎屏、花屏都可以用，而且有面板就行。可以考虑到本地垃圾场去捡一个，单买的话有点~~败家~~。
   > 注意：必须是 LCD 屏！  
   > 注意：请使用其它视频源和连接线确保显示器是坏的，至少我不会希望把好的显示器拆开。
   > ![确实坏了](https://img.cdn.gaein.cn/blog/posts/How-to-DIY-lamp/BD33RARLZ.jpg)
2. LED 灯带/灯条
   > 注意：不是最小裁剪 1m 那种，而是裸板的 LED 灯带。这种：
   > ![LED](https://img.cdn.gaein.cn/blog/posts/How-to-DIY-lamp/BD95Q9R2T.jpg)
3. 12v 电源适配器，用于给 LED 灯带供电
4. DC 母座，方便插电用
5. 18650 电池、电池盒 x3，用于停电时给灯供电
6. 12v 供电切换模块，停电时切换供电

基本上的清单就是这样：

![物品](https://img.cdn.gaein.cn/blog/posts/How-to-DIY-lamp/BD53DHR2E.png)


当然还有一个显示屏

![屏幕](https://img.cdn.gaein.cn/blog/posts/How-to-DIY-lamp/BD33Q9R2T.jpg)

# 制作

## 拆屏

需要的是显示屏内的背光部分，具体来说就是扩散层和一些膜（其实把显示器值钱的部分全扔了）

### 拆解外壳

把显示器的外壳拆下来，一般来说背面有螺丝，侧面是卡扣的。但是因为没有找到窍门，我就暴力了一下。

我用显卡翘的，结果都弯了...

![显卡](https://img.cdn.gaein.cn/blog/posts/How-to-DIY-lamp/BD33RBNW5.jpg)

最后暴力钻孔打穿了卡扣才卸下来。

### 拆解面板

将后壳拿下来后里面大致是这样的

![内部](https://img.cdn.gaein.cn/blog/posts/How-to-DIY-lamp/BD33RCVCZ.jpg)

将上面的胶带撕开，断开连接控制按钮的电线。

![电线](https://img.cdn.gaein.cn/blog/posts/How-to-DIY-lamp/BD33RC82Q.jpg)

将鼓起来那块放 PCB 的拆下，并断开电线

![PCB](https://img.cdn.gaein.cn/blog/posts/How-to-DIY-lamp/BD33RD375.jpg)

这根电线粘在上面，要撕下来，这是给背光模组供电的。

![电线](https://img.cdn.gaein.cn/blog/posts/How-to-DIY-lamp/BD33RERGM.jpg)

拆完后就剩下一个面板

![面板](https://img.cdn.gaein.cn/blog/posts/How-to-DIY-lamp/BD33RE3WP.jpg)

### 拆解背光

面板的四周是完全用卡扣固定的，轻轻的撬开就能将它打开。由于下面还有胶带粘住，只能先掀起来。

![撬](https://img.cdn.gaein.cn/blog/posts/How-to-DIY-lamp/BD33RFF05.jpg)

![LCD](https://img.cdn.gaein.cn/blog/posts/How-to-DIY-lamp/BD33RFR2T.jpg)

黑色的是屏幕，去掉胶带后把黑色的屏幕拿下去，剩下的就是背光层。

![背光层](https://img.cdn.gaein.cn/blog/posts/How-to-DIY-lamp/BD33RGF5A.jpg)

一层一层的把薄膜拿下来，最后一层是散光层

![散光层](https://img.cdn.gaein.cn/blog/posts/How-to-DIY-lamp/BD33RGZHG.jpg)

侧面有一条LED灯带，作为光源。但是我这里不采用这个灯带，因为它的电压很高（我这个屏幕光源电压已经高于100V了），不是很安全。而且还要对供电进行魔改。所以我买了LED灯带。

## 电路

为了实现断电时仍然能供电，我买了一些模块，用于供电。
大致就是，12v适配器用于平时供电与电池充电。断电时自动切换到电池进行供电。为了电压合适使用了3节18650电池串联。

> 等待物资到货中。

> 咕咕咕
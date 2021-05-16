---
title: 三星内存序列号含义——教你区分三星内存颗粒B-Die或者C-Die
author: Gaein nidb
categories:
  - 电脑硬件
tags:
  - 内存
  - 电脑硬件
date: 2019-02-17 00:17:51
---

经常有人问怎么区分三星B-Die或者C-Die啥的，今天呢闲来无事看了一下，就写在这里啦

版权声明：标题图引用自淘宝，内部部分图片和内容引用于网络（如ChipHell），如果侵权请联系 mail@Gaein.cn

## 查询序列号

经常有人问怎么区分三星B-Die或者C-Die啥的，今天呢闲来无事看了一下，就写在这里啦

首先要确定内存是三星的，具体可以用AIDA64软件来看，在主板>SPD>DRAM制造商一栏，比如我的是镁光公司制造的颗粒

![ks5Vrq.png](https://s2.ax1x.com/2019/02/17/ks5Vrq.png)

然后拆开内存的马甲（没有更省心啊哈哈）找到内存芯片上面的序列号

![ks5QG4.md.jpg](https://s2.ax1x.com/2019/02/17/ks5QG4.md.jpg)]

从第二行起：

```
K4A8G085WB-BCPB
```

## 序列号含义

这是个B-Die芯片。我们来解释一下三星的序列号意义（部分内容为个人根据官网数据总结而来）：

### K4：

不同于ChipHell某个帖子的说法，我认为4并非代表DRAM产品。因为三星的LP-DDR3、LP-DDR4X产品中均出现了K3开头的型号，LP-DDR4X的中文名称是四代超低功耗双倍数据率同步动态随机存储器，双倍数据率同步动态随机存储器就是DRAM。而且在三星产品网页的网址"https://www.samsung.com/semiconductor/cn/dram/lpddr4x/"也能看出来其是DRAM产品，但并非K4开头。除此之外，HBM、HBM2均为KH开头。
（LP-DDR3、LP-DDR4X有K3，HBM、HBM2均为KH）

### A：

代表DDR4。（此外：F是LP-DDR4，U是LP-DDR4X，B是DDR3，Q（K3开头）或E（K4开头）是LP-DDR3，Z是G-DDR6，G是G-DDR5，A8是HBM/HBM2）

### 8G：

代表容量（整个内存容量，如果是这样的话应该是1Gx8（单面条）或者512MX16（双面）或者2GX4）（除此之外，DDR4产品用AG代表6GB，LP-DDR4产品用2代表12GB、6代表16GB、H代表24GB、B代表32GB、4和8分别代表4G和8G，LP-DDR4X产品用H6代表48GB、用H7代表64GB、12GB 16GB 24FB 32GB同LP-DDR4，DDR3用1G2G4G表示容量，LP-DDR3用F3（K3开头）或者6E（K4开头）表示16GB、HE表示24GB、F4（K3开头）或者BE（K4开头）表示32GB、用FA表示64G）

### W后面的B：

代表版本：B，也就是B-Die颗粒（如果在三星官网找到那个内存颗粒的网页，然后点相关资源>DataSheet会发现标题写着“8Gb B-die DDR4 S-DRAM”）同样的，C就是C-Die，E是E-Die或者C-Die（对于其他产品如LP-DDR4等，并不适用）

### -BCPB
我不是特别清楚，可以参考：https://www.chiphell.com/thread-1625004-1-1.html
但是原文我感觉有误，因为B表示78 FBGA的话，为什么96也写的B？

至于W前面的字母，应该是和架构有关

最稳妥的知道规格的方法还是去三星官网查询：https://www.samsung.com/semiconductor/cn/dram/ddr4/

当然，看W后面的字母就知道是否是B-Die啦

实在不信，可以点击链接进到颗粒具体的页面，然后看看相关资源>DataSheet写的是什么就好啦

![ksojM9.png](https://s2.ax1x.com/2019/02/17/ksojM9.png)

那么如何根据已经知道的信息在不拆开马甲的情况下确定大概的序列号呢（这样无法判断是否为B-Die）

首先观察单面或者双面，侧面看看颗粒数目，根据容量确定架构

比如8G单面条，8个颗粒就是1G x 8啦，速率根据AIDA64>主板>存取速度（不带SPD的）确定。

然后在三星官网：https://www.samsung.com/semiconductor/cn/dram/ 选择响应的产品（如DDR4）在网页下面的筛选器筛选即可。只能缩小范围，无法确定具体型号的
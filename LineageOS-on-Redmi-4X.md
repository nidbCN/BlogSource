---
title: '【弃坑】在红米4X上安装LineageOS'
author: 'Gaein nidb'
categories:
  - 杂记
tags:
  - Android
  - 软件教程
date: 2021-10-04 18:43:15
---

> 本来就想顺便玩玩结果用了4部手机了.jpg

> 设备：红米4X(santoni)  
> 系统：MIUI开发版 9.10.11
> 架构：ARM64

> 咕咕咕中，问题真的好多啊

## 准备工作

> 另请参阅：[Install LineageOS on santoni | LineageOS Wiki](https://wiki.lineageos.org/devices/santoni/install)

### 安装ADB和FastBoot

1. 从 [Google](https://dl.google.com/android/repository/platform-tools-latest-windows.zip) 下载Windows版的压缩包；
2. 解压缩下载的压缩包到你习惯的文件夹（比如 `C:\Users\YourName\Tools\PlatformTools\`）；
3. 将解压缩的文件夹添加到系统 `Path` 环境变量。

### 解锁 bootloader

1. 在 `设置>我的设备>全部参数` 中点击3次MIUI版本，再点击3次MIUI版本，开启开发者模式；
2. 下载 [小米解锁工具](https://www.miui.com/unlock/index.html)，确保账号有权限解锁；
3. 在 `设置>更多设置>开发者选项>设备解锁状态` 中，插入SIM卡并绑定账号。具体步骤可以参考[小米社区](https://www.xiaomi.cn/post/4378807)；
4. 在 `设置>更多设置>开发者选项>USB调试` 中启用USB调试。
5. 下载 [小米刷机工具](http://www.miui.com/shuaji-393.html) ，不用下载任何包，在 `fastboot` 下解压运行后按提示安装驱动，然后这玩意就没用了；
   > 似乎只有在 `fastboot` 模式下用小米来装驱动才行，此前不知道怎么回事一直没驱动。
6. 在电脑终端内输入 `adb shell reboot -p` ，手机弹窗允许调试点击“确定”，手机关机；
7. 关机状态下，按住 `音量-` 再按 `电源键` ，开机进入 fastboot；
8. 运行小米解锁工具，登录账户并确认解锁。

## 刷入第三方 Recovery

这里选择使用 [PitchBlack Recovery](https://pitchblackrecovery.com/) ~~因为好看~~

> 另请参阅：[How to Install - PitchBlack Recovery Project](https://pitchblackrecovery.com/docs/how-to-install/)

### 准备

1. 确保你的设备已经解锁；
2. 确保PC端有 `adb` 和 `fastboot`；
3. 从 [PitchBlack Recovery](https://pitchblackrecovery.com/santoni/) 下载适用于红米4X的 Recovery；
4. 从 [TeamWin Recovery](https://twrp.me/xiaomi/xiaomiredmi4x.html) 翻到“Download Links:” 处，下载适用于红米4X的 Recovery。（我这边是欧洲的镜像比较快，自行选择）

### 从TWRP刷入PBRP

因为以前是小米的 Recovery ，直接安装 PitchBlack 没有搞太明白，所以先刷入 TWRP。

1. 到下载TWEP的目录，在PC终端执行 `fastboot boot twrp-<version>-santoni.img` ，从TWRP中启动；
2. 在手机进入TWRP后，点击 `Wipe>Format Data` 并输入 `yes` 后执行；
3. 点击 `Back` 返回到主界面，点击 `Advanced>ADB Sideload` 启动 ADB 旁加载；
4. 到下载PBRP的目录，从PC终端执行 `adb sideload .\PBRP-santoni-<version>-OFFICIAL.zip `；
5. 手机终端出现 `---Installation finished!---` 字样即为刷入完成；
6. 按下 `音量-` 重启至 PitchBlack Recovery。

## 编译 LineageOS

> 另请参阅：[Build for santoni | LineageOS Wiki](https://wiki.lineageos.org/devices/santoni/build)

### 准备工作

准备一台装有 64位的 **Ubuntu** 20.04 （或Ubuntu Server 20.04）的电脑，可以考虑使用虚拟机或云服务器，为了避免麻烦，建议使用Ubuntu Server完成。编译的时间取决于性能。LineageOS官方的要求是200G存储空间，并且内存越小编译越慢。

为了速度我就直接拿服务器编译了。

> DELL PowerEdge R620  
> Intel® Xeon® Processor E5-2670 v2 X2  
> MT DDR3 REG-ECC PC8500R 16GB X4  
> HGST 600GB 15K SAS X3 (Raid5)  
> Ubuntu server 20.04.02

> Ubuntu 发行版的光盘可以从 [清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-cdimage/) 下载。

> 安装服务器过程略过，由于我在tty下使用所以语言选择了英语。

> 建议在安装时候配置国内源，比如清华源（`https://mirrors.tuna.tsinghua.edu.cn/ubuntu/`）。

### 环境配置

通过SSH连接上服务器之后就可以进一步进行环境的配置了（当然你在tty内配置也不是不行）。

#### 安装platform Tools

1. 【可能需要代理】使用 wget 下载 Linux 版的 platform Tools: `wget https://dl.google.com/android/repository/platform-tools-latest-linux.zip`；
2. 使用 apt 安装 zip: `apt install zip`；
4. 解压缩 platform Tools :`unzip platform-tools_<version>-linux.zip`；
5. 编辑 `~/.profile`，添加:
   ```
   # add Android SDK platform tools to path
   if [ -d "$HOME/platform-tools" ] ; then
      PATH="$HOME/platform-tools:$PATH"
   fi
   ```
   添加完成后运行 `source ~/.profile` 更新环境。

#### 安装依赖
1. 使用 apt 安装依赖: `apt install zip bc bison build-essential ccache curl flex g++-multilib gcc-multilib git gnupg gperf imagemagick lib32ncurses5-dev lib32readline-dev lib32z1-dev liblz4-tool libncurses5 libncurses5-dev libsdl1.2-dev libssl-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools xsltproc zip zlib1g-dev`；
   > 对于 Ubuntu 20.04 以前的操作系统来说，还需要安装 libwxgtk3.0-dev: `apt install libwxgtk3.0-dev`
2. 如果希望构建 lineageOS 15，则需要安装OpenJDK8: `apt install openjdk-8-jdk`

#### 创建目录

1. `mkdir -p ~/bin`；
2. `mkdir -p ~/android/lineage`。

#### 安装repo命令
1. 下载repo: `curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo`；
2. 更改权限: `chmod a+x ~/bin/repo`；
3. 将 `~/bin` 添加到环境变量，在 `~/.profile` 中添加:
   ```
   # set PATH so it includes user's private bin if it exists
   if [ -d "$HOME/bin" ] ; then
       PATH="$HOME/bin:$PATH"
   fi
   ```
4. 运行 `source ~/.profile` 更新环境。

#### 配置Git

1. 使用 `apt install git` 安装 Git；
2. 配置你的邮箱和用户名：
   ```
   git config --global user.email "you@example.com"
   git config --global user.name "Your Name"
   ```
#### 开启缓存

> 开启缓存可以加快构建

1. 设置环境变量
   ```
   export USE_CCACHE=1
   export CCACHE_EXEC=/usr/bin/ccache
   ```
2. 设置ccache，编辑`~/.profile`，加入:
   ```
   ccache -M 32G
   ```
3. 使用`source ~/.profile` 使配置生效
#### 配置LineageOS仓库

红米4X可以选择如下两个分支：
* lineage-15.1(需要OpenJDK-8)
* lineage-16.0

1. 进入目录: `cd ~/android/lineage`；
2. 安装python（由于Ubuntu自带python，安装 `python-is-python3`）即可；
3. 【可能需要代理】初始化项目: `repo init -u https://github.com/LineageOS/android.git -b lineage-<version>`
   > 如果无法下载可以考虑添加环境变量 `REPO_URL=https://github.com/GerritCodeReview/git-repo`；
4. 【可能需要代理】使用 `repo sync` 下载源代码；
   > 这步好艰难...要下很长时间

#### 配置设备相关

输入 
```
source build/envsetup.sh
breakfast santoni
```

下载红米4X(santoni)的配置文件和内核。

> 部分机型需要一个vendor目录。如果你收到了一个关于vendor的错误，到 [GitLab](https://gitlab.com/the-muppets/proprietary_vendor_xiaomi/-/tree/lineage-16.0/santoni) 下载。放入 `~/android/lineageos/vendor/xiaomi` 后，再次执行 `breakfast santoni` 即可。
> 经尝试上述方法在后来引起了错误，所以参考官方文档，从LineageOS的包里导出该文件，首先下载 [LineageOS 16.0 for santoni](https://lineageosroms.com/santoni/) （这个包的来历我也不知道，应该是以前镜像的官方build），使用 `unzip path/to/lineage-*.zip system/*` 解压，并将 `system` 目录移动到 `~/android/system_dump/`，到 `~/android/lineage/device/xiaomi/santoni` 目录执行 `./extract-files.sh ~/android/system_dump/` 导出。

#### 编译LineageOS

执行
```
croot
brunch santoni
```

> 报错 
> ```
> ninja: error: '/home/nidb/android/lineageos/out/target/common/obj/JAVA_LIBRARIES/WfdCommon_intermediates/javalib.jar', needed by '/home/nidb/android/lineageos/out/target/product/santoni/dex_bootjars/system/framework/boot.prof', missing and no known rule to make it
> ```
> 很是绝望。参考 [StackOverflow](https://stackoverflow.com/questions/54946271) 发现或许是缺了 Wfd，在Github找到[这个仓库](https://github.com/bionicBUG/android_device_xiaomi_santoni/tree/los16) ，克隆该项目替换，没有作用。


> 考虑使用 `make` 直接编译，因为脚本中含有 python2，所以卸载 `python-is-python3` ，然后 `apt install python2`；  
> 尝试使用 `lunch` ,`make -j10` 。  
> 报错 `ninja: error: 'prebuilts/lineage-sdk/api/9.txt'`，参考 [XDA](https://forum.xda-developers.com/t/help-by-building-lineageos-16.3887273/) 克隆 `https://github.com/LineageOS/android_prebuilts_lineage-sdk` 到 `prebuilts` 解决。  
> 编译到一大半报错 `ninja: build stopped: subcommand failed.` 原因是被Linux系统限制，使用 `ulimit -c unlimited` 解除。
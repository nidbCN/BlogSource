## 手机

红米 Note 3 (kenzo) [LineageOS wiki](https://wiki.lineageos.org/devices/kenzo)

## 解锁

使用小米官方的解锁工具完成。

> 绑定解锁账号需要一周时间

下载 [小米解锁工具](https://www.miui.com/unlock/download.html)



## 刷入 PitchBlack Recovery

从 [SourceForge](https://sourceforge.net/projects/pbrp/files/kenzo/) 下载适用于红米 Note3 的 Recovery

## 刷入 Magisk

## 编译 LineageOS

系统要求：Ubuntu x64（建议在实体机安装或开虚拟机，但是我并不在乎编译速度，所以就用云服务器了）

## 安装 LineageOS

## 刷入 OpenGApps

LineageOS
Magisk
PitchBlack Recovery

下载 小米解锁工具
下载 ADB FastBoot
下载 LineageOS.zip
下载 PitchBlack Rec.zip 解压
下载 小米线刷 ROM 包 解压

重新登陆小米账户并绑定，开启 DEV，开启 USB Debug

解锁 BL 锁

刷入 小米线刷 ROM 包中的 vbmeta.img 并关闭 AVB2.0

`fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img`

`fastboot --disable-verity flash vbmeta vbmeta.img`
`fastboot --disable-verity flash vbmeta_system vbmeta_system.img`

刷入 PitchBlack 中的 TWRP Recovery.img

刷入 lineageos 的 rec

`fastboot flash recovery <recovery_filename>.img`

`fastboot reboot recovery`

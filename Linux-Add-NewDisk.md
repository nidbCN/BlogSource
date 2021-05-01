title: 'Linux挂载新硬盘'
author: Gaein nidb
cover: 'https://s1.ax1x.com/2018/10/20/i04JfK.png'
categories:
  - 服务器运维
tags:
  - Linux
  - 运维
  - 软件教程
date: 2018-10-20 12:07:15
---
最近给Ubuntu添加了一块硬盘，下面是具体的步骤
<!-- more -->
<p>
    买的云服务器IDC给了一块10GB的数据盘，因为系统盘不大，就要把数据盘用上了
</p>
<p>
    但是Linux下数据盘并没有挂载，Linux的磁盘系统也不同于windows，需要挂载使用
</p>
<h1 label="标题居左" style="font-size: 32px; font-weight: bold; border-bottom: 2px solid rgb(204, 204, 204); padding: 0px 4px 0px 0px; text-align: left; margin: 0px 0px 10px;">
    1、新建分区并格式化<br/>
</h1>
<p>
    使用
</p>
<pre class="fy-prettyprint linenums">fdisk -l</pre>
<p>
    <img src="https://s1.ax1x.com/2018/10/20/i04JfK.png" alt="" width="516" height="328"/>
</p>
<p>
    查看当前的硬盘，可以看到有sda、sdb两块盘，第一块作为系统盘已经分区，sdb就是我们要挂载的数据盘
</p>
<p>
    首先进行分区
</p>
<pre class="fy-prettyprint linenums">fdisk /dev/sdb</pre>
<p>
    <img src="https://s1.ax1x.com/2018/10/20/i048Fx.png" alt="" width="491" height="162"/>
</p>
<p>
    进入磁盘sdb（如果你是磁盘sdc就相应的替换为sdc）相当于选中这个硬盘的意思
</p>
<p>
    首先输入m看一下帮助
</p>
<p>
    <img src="https://s1.ax1x.com/2018/10/20/i041T1.png" alt="" width="567" height="571"/>
</p>
<p>
    比较简单，应该很容易看懂，暂时把几个常用的写上了意思
</p>
<pre class="fy-prettyprint linenums">Command (m for help): p                                        #显示磁盘信息
Disk /dev/sdb: 10 GiB, 10737418240 bytes, 20971520 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: dos
Disk identifier: 0xb5106536

Command (m for help): n                                        #新建分区
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p                                          #新建逻辑分区
Partition number (1-4, default 1): 
First sector (2048-20971519, default 2048): 
Last sector, +sectors or +size{K,M,G,T,P} (2048-20971519, default 20971519): 
#直接留空，我只想把所有的空间分一个分区
Created a new partition 1 of type &#39;Linux&#39; and of size 10 GiB.

Command (m for help): w                                        #保存退出
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.</pre>
<p>
    <img src="https://s1.ax1x.com/2018/10/20/i04lwR.png" alt="" width="560" height="382"/>
</p>
<p>
    再次使用fdisk -l已经可以看到sdb有一个分区sdb1了
</p>
<p>
    <img src="https://s1.ax1x.com/2018/10/20/i04tSO.png" alt="" width="511" height="397"/>
</p>
<p>
    同Windows一样，磁盘需要格式化后才能使用
</p>
<p>
    用
</p>
<pre class="fy-prettyprint linenums">mkntfs /dev/sdb1</pre>
<p>
    来格式化为NTFS格式，其他格式可以自行百度
</p>
<p>
    <img src="https://s1.ax1x.com/2018/10/20/i04GY6.png" alt="" width="391" height="57"/>
</p>
<p>
    出现
</p>
<pre class="fy-prettyprint linenums">Cluster size has been automatically set to 4096 bytes.
Initializing device with zeroes: 100% - Done.
Creating NTFS volume structures.
mkntfs completed successfully. Have a nice day.</pre>
<p>
    即为格式化完成
</p>
<h1 label="标题居中" style="font-size: 32px; font-weight: bold; border-bottom: 2px solid rgb(204, 204, 204); padding: 0px 4px 0px 0px; text-align: center; margin: 0px 0px 20px;">
    2、挂载磁盘
</h1>
<div>
    <p>
        <br/>
    </p>
    <p>
        临时挂载可以使用mount命令，但是重启后需要重新挂载
    </p>
    <p>
        用法为
    </p>
    <pre class="fy-prettyprint linenums">mount 分区 目录</pre>
    <p>
        比如我需要使用
    </p>
    <pre class="fy-prettyprint linenums">mount /dev/sdb1 /data</pre>
    <p>
        挂载
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/10/20/i0IkGV.png" alt="" width="542" height="83"/>
    </p>
    <p>
        （当然前提是存在目录/data）
    </p>
    <p>
        编辑
    </p>
    <pre class="prettyprint" name="code">/etc/fstab</pre>
    <p>
        来永久挂载磁盘
    </p>
    <pre class="fy-prettyprint linenums"># /etc/fstab: static file system information.
# 
# Use &#39;blkid&#39; to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# &lt;file system&gt; &lt;mount point&gt;   &lt;type&gt;  &lt;options&gt;       &lt;dump&gt;  &lt;pass&gt;
# / was on /dev/sda1 during installation
UUID=22e202f3-ae04-4995-b214-de7f192f33c4 /               ext4    errors=remount-ro 0       1
# swap was on /dev/sda5 during installation
UUID=472580b3-a87b-4fe2-8bda-59b59d087ec6 none            swap    sw              0       0
/dev/fd0        /media/floppy0  auto    rw,user,noauto,exec,utf8 0       0
/dev/sdb1       /data   ntfs    defaults        0       0
磁盘           挂载目录 磁盘格式</pre>
</div>
<p>
    需要注意的是，中间的空不需要打空格，用TAB制表符更方便<br/>
</p>
<pre class="fy-prettyprint linenums">root@rootadmin:/data# mkdir blog
root@rootadmin:/data# dir
blog
root@rootadmin:/data# reboot
Connection closing...Socket close.

Connection closed by foreign host.

Disconnected from remote host(GAEIN.CN) at 12:21:20.

Type `help&#39; to learn how to use Xshell prompt.
[C:\~]$ 

Connecting to *.*.*.*:22...

Connection established.
To escape to local shell, press &#39;Ctrl+Alt+]&#39;.

Welcome to Ubuntu 18.04.1 LTS (GNU/Linux 4.15.0-38-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sat Oct 20 12:21:29 CST 2018

  System load:  0.49               Processes:           89
  Usage of /:   24.9% of 12.67GB   Users logged in:     0
  Memory usage: 7%                 IP address for eth0: *.*.*.*
  Swap usage:   0%


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

0 个可升级软件包。
0 个安全更新。

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Sat Oct 20 11:26:33 2018 from *.*.*.*
rootadmin@rootadmin:~$ cd /data
rootadmin@rootadmin:/data$ dir
blog
rootadmin@rootadmin:/data$</pre>
<p>
    重启了一下，挂载确实成功了<br/>
</p>


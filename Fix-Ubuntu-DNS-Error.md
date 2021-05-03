---
title: Ubuntu下apt “无法解析域名”解决方案
author: Gaein nidb
tags:
  - 运维
  - Linux
  - DNS
  - 服务器
categories: [服务器运维]
date: 2018-10-20 11:42:39
---
DNS莫名其妙出错，啥也干不了
<!-- more -->
<p>
    我的原因是DNS出了错误
</p>
<p>
    <img src="https://s1.ax1x.com/2018/10/20/i0h6sJ.png" alt="" data-load="full" style="" width="761" height="530"/>
</p>
<p>
    百度了一波，发现解决方案很简单，就是手动加入DNS
</p>
<pre class="fy-prettyprint linenums">sudo vim /etc/resolv.conf</pre>
<p>
    加入DNS
</p>
<pre class="fy-prettyprint linenums">nameserver 119.29.29.29</pre>
<p>
    //当然可以自己去百度其他家的DNS，我这是DNS PUBLIC+的地址
</p>
<p>
    按照百度说的我保存重启
</p>
<p>
    结果发现并没有什么卵用...
</p>
<p>
    仔细观摩一下文件
</p>
<pre class="fy-prettyprint linenums"># Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
#     DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN
# 127.0.0.53 is the systemd-resolved stub resolver.
# run &quot;systemd-resolve --status&quot; to see details about the actual nameservers.

nameserver 127.0.0.53</pre>
<p>
    被覆盖了
</p>
<p>
    而且说明也写着“DO NOT EDIT THIS FILE BY HAND”
</p>
<p>
    我按照其他的方法修改了一下编辑resolv.conf.d目录下的base文件
</p>
<p>
    不过也没有效果
</p>
<p>
    但是按照第二篇看的文章找到了问题所在：
</p>
<p>
    重启系统后DNS设置不但不会被应用还会被覆盖，应该重启网卡来解决
</p>
<pre class="fy-prettyprint linenums"> sudo /etc/init.d/networking restart</pre>
<p>
    完美解决
</p>
<p>
    <img src="https://s1.ax1x.com/2018/10/20/i0hRd1.png" alt="" data-load="full" style="" width="700" height="530"/>、
</p>
<p>
    <br/>
</p>
<p>
    参考：
</p>
<p>
    <a href="https://blog.csdn.net/u011954647/article/details/49965319" _src="https://blog.csdn.net/u011954647/article/details/49965319">https://blog.csdn.net/u011954647/article/details/49965319</a><br/>
</p>
<p>
    <a href="https://blog.csdn.net/tsq292978891/article/details/75039926" _src="https://blog.csdn.net/tsq292978891/article/details/75039926">https://blog.csdn.net/tsq292978891/article/details/75039926</a><br/>
</p>
<p>
    <a href="https://blog.csdn.net/u012207345/article/details/78339218" _src="https://blog.csdn.net/u012207345/article/details/78339218">https://blog.csdn.net/u012207345/article/details/78339218</a><br/>
</p>
<p>
    解决 <br/>
</p>
<p>
    <br/>
</p>
<p>
    不过刚刚重启了好像又被覆盖了，很是绝望
</p>
<p>
    干脆写了个脚本，然后开机自动运行脚本得了...
</p>
<pre class="fy-prettyprint linenums">#!/bin/bash
  
### BEGIN INIT INFO
# Provides:     run.sh 
# Required-start:    $local_fs $remote_fs $network $syslog 
# Required-Stop:     $local_fs $remote_fs $network $syslog 
# Default-Start:     2 3 4 5 
# Default-Stop:      0 1 6 
# Short-Description: starts the run.shh daemon 
# Description:       starts run.sh using start-stop-daemon 
### END INIT INFO

sudo echo &quot;nameserver 119.29.29.29&quot; &gt;&gt; /etc/resolv.conf
sudo /etc/init.d/networking restart
sudo echo &quot;[INFO][`date +%Y-%m-%d` `date +%H:%M:%S`]DNS设置完成&quot;</pre>
<p>
    然后复制到init.d中，再
</p>
<pre class="prettyprint" name="code">update-rc.d run.sh defaults 99</pre>
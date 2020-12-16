---
title:  Ubuntu终端更改为中文
author: Gaein nidb
tags: 
  - Linux
  - 运维
categories: [服务器运维]
date: 2018-08-17 09:36:49
---
由于本人英文渣，加之一个服务器终端是中文一个服务器终端是英文（当然两个服务器都穷的续不起了）

所以今天更改Ubuntu的语言为中文

因为是服务器所以只能通过终端更改
<!-- more -->
<p>
    由于本人英文渣，加之一个服务器终端是中文一个服务器终端是英文（当然两个服务器都穷的续不起了）
</p>
<p>
    所以今天更改Ubuntu的语言为中文
</p>
<p>
    因为是服务器所以只能通过终端更改
</p>
<p>
    <br/>
</p>
<p>
    首先安装下语言包
</p>
<pre class="fy-prettyprint linenums">sudo apt search language-pack-zh-hans</pre>
<p>
    我先使用apt搜索了一下语言包
</p>
<p>
    <img src="https://s1.ax1x.com/2018/08/17/PWu8US.png" alt="" width="637" height="125"/><br/>
</p>
<p>
    使用命令安装他们
</p>
<pre class="fy-prettyprint linenums">sudo apt install language-pack-zh-hans
sudo apt install language-pack-zh-hans-base</pre>
<p>
    安装下对于程序的语言支持<br/>
</p>
<pre class="fy-prettyprint linenums">sudo apt install `check-language-support -l zh`</pre>
<p>
    将语言设置为中文
</p>
<pre class="fy-prettyprint linenums">sudo localectl set-locale.UTF-8</pre>
<p>
    重启系统
</p>
<pre class="fy-prettyprint linenums">sudo reboot</pre>
<p>
    随便输入个什么命令，比如date
</p>
<pre class="fy-prettyprint linenums">date</pre>
<p>
    显示为中文，修改成功
</p>
<p>
    <img src="https://s1.ax1x.com/2018/08/17/PWu3E8.png" alt="" width="263" height="52"/>
</p>
<p>
    如果没有修改成功的话，使用文本编辑器编辑文件
</p>
<pre class="fy-prettyprint linenums">vim  /etc/default/locale</pre>
<p>
    <img src="https://s1.ax1x.com/2018/08/19/PfTLB8.png" alt="" width="447" height="22"/>
</p>
<p>
    将LANG LANGUAGE等后面换成
</p>
<pre class="fy-prettyprint linenums">zh_CN.UTF-8</pre>
<p>
    如图：
</p>
<p>
    <img src="https://s1.ax1x.com/2018/08/19/PfTOHS.png" alt="" data-load="full" style="" width="759" height="513"/>
</p>
<p>
    重启系统后就是中文了
</p>
<p>
    <img src="https://s1.ax1x.com/2018/08/19/PfTjAg.png" alt="" width="577" height="424"/><br/>
</p>
<p>
    <br/>
</p>
<p>
    参考：<a href="https://www.linuxdashen.com/ubuntu如何在终端下更改系统语言" _src="https://www.linuxdashen.com/ubuntu如何在终端下更改系统语言">https://www.linuxdashen.com/ubuntu如何在终端下更改系统语言</a>
</p>
<p>
    <a href="https://blog.csdn.net/cnylsy/article/details/52474433" _src="https://blog.csdn.net/cnylsy/article/details/52474433">https://blog.csdn.net/cnylsy/article/details/52474433</a><br/>
</p>

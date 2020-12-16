---
title: 在Ubuntu上搭建现代化的论坛Nodebb|NodeBB安装记录（Linux-Ubuntu篇）
author: Gaein nidb
tags: 
 - nodeBB
 - Linux
 - Ubuntu
 - 网站建设
categories: [网站建设]
date: 2018-06-22 23:47:33
---
<p>
    继上次&nbsp;<a href="https://blog-old.gaein.cn/post/nodeBB-windows-4.html" _src="https://blog-old.gaein.cn/post/nodeBB-windows-4.html">https://blog-old.gaein.cn/post/nodeBB-windows-4.html</a>&nbsp;失败后，我仍然不思悔改，NodeBB就是好看！在老家闲来无事的时候租用了一个服务器，于是，故事开始了
</p>
<p>
    NodeBB是基于nodejs的现代化论坛程序
</p>
<p>
    系统我选择了Ubuntu，再多次干到凌晨四五点后，终于让网站成功运行了！
</p>
<p>
    <br/>
</p>
<p>
    <img src="https://s1.ax1x.com/2018/06/22/Pphves.md.png"/>
</p>
<p>
    因为服务器是使用SSH链接的，所以即使有桌面，我也选择用终端（在安装NodeBB这方面桌面好像没用...）
</p>
<p>
    <img src="https://s1.ax1x.com/2018/06/22/PphqSS.png"/>
</p>
<p>
    <br/>
</p>
<p>
    相关安装文档查看：
</p>
<p>
    <a href="https://www.kancloud.cn/a632079/nodebb-cn/" _src="https://www.kancloud.cn/a632079/nodebb-cn/">https://www.kancloud.cn/a632079/nodebb-cn/</a>
</p>
<div>
    <p>
        <br/>
    </p>
    <p>
        <br/>
    </p>
    <hr/>
    <p>
        <span style="font-size: 24px; color: #FFC000;">前奏</span>
    </p>
    <p>
        第一步我选择切换到管理员账户（root）<br/>
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/Pp49YV.png"/>
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/PphHW8.png"/>
    </p>
    <p>
        貌似没有vim（一个强大的文本编辑软件）
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/PphOyQ.png"/>
    </p>
    <p>
        使用
    </p>
    <pre class="fy-prettyprint linenums">apt-get install vim</pre>
    <p>
        按y同意安装
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/Pphzoq.png"/>
    </p>
    <p>
        这网速真是伤感
    </p>
    <p>
        如果安装失败了，尝试用
    </p>
    <pre class="fy-prettyprint linenums">apt-get update</pre>
    <p>
        更新，然后再尝试
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/Pphxwn.png"/>
    </p>
    <p>
        在执行第一条命令（快速安装nodejs用）的时候，又报错了<br/>
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/Pp4pF0.png"/>
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/Pp4CWT.png"/>
    </p>
    <p>
        这系统真是纯洁，啥都没有...
    </p>
    <p>
        使用
    </p>
    <pre class="fy-prettyprint linenums">apt install curl</pre>
    <p>
        安装curl
    </p>
    <hr/>
    <p>
        <span style="font-size: 24px; color: #FFC000;">安装Node.js LTS</span><br/>
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/Pp4AOJ.png"/>
    </p>
    <p>
        使用
    </p>
    <pre class="fy-prettyprint linenums">curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -</pre>
    <p>
        来快速安装Node.js
    </p>
    <p>
        如果服务器在大陆的话<br/>
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/Pp4ky4.png"/>
    </p>
    <pre class="fy-prettyprint linenums">vim /etc/apt/sources.list.d/nodesource.list</pre>
    <p>
        将链接全部换成
    </p>
    <pre class="fy-prettyprint linenums">https://mirrors.ustc.edu.cn/nodesource/deb/node_{版本号，例如: 8}.x</pre>
    <p>
        <br/>
    </p>
    <p>
        按“i”进入编辑模式，完成后按ESC退出编辑模式，再输入:wq 保存退出
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/Pp4iSU.png"/><img src="https://s1.ax1x.com/2018/06/22/Pp4ZwR.png"/>
    </p>
    <p>
        再次更新APT源
    </p>
    <pre class="fy-prettyprint linenums">sudo apt-get update</pre>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/Pp4Vm9.png"/>
    </p>
    <p>
        然后通过APT安装Node.js<br/>
    </p>
    <pre class="fy-prettyprint linenums">sudo apt-get install -y nodejs</pre>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/Pp4eT1.png"/>
    </p>
    <p>
        使用<br/>
    </p>
    <pre class="fy-prettyprint linenums">node -v</pre>
    <p>
        检查版本，确实已经安装完成
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/Pp4uY6.png"/>
    </p>
    <hr/>
    <p>
        <span style="font-size: 24px; color: #FFC000;">数据库：Redis</span><br/>
    </p>
    <p>
        鉴于Redis体积较小等优点，数据库我选择了redis
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/Pp4KfK.png"/>
    </p>
    <pre class="fy-prettyprint linenums">sudo add-apt-repository ppa:chris-lea/redis-server 
sudo apt-get update
sudo apt-get install -y redis-server #安装redis</pre>
    <p>
        使用这三行来安装redis（当然是从上到下一行一行来）
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/22/Pp4nFx.png"/>
    </p>
    <p>
        按回车键继续
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/Pp4xje.png"/>
    </p>
    <p>
        最后一步，慢慢等待
    </p>
    <p>
        <span style="color: #8064A2;">Redis安全设置（强烈推荐进行这一步）</span><br/>
    </p>
    <p>
        使用
    </p>
    <pre class="fy-prettyprint linenums">vim /etc/redis/redis.conf</pre>
    <p>
        编辑配置文件<br/>
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/Pp4vcD.png"/>
    </p>
    <p>
        按“i”进入编辑模式，在前面插入如下三行
    </p>
    <pre class="fy-prettyprint linenums">requirepass yourpassword
bind 127.0.0.1
rename-command FLUSHALL &quot;&quot;</pre>
    <p>
        其中，“yourpassword”是你自己设置的密码，记住它，后面要用到的
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/Pp4X9K.png"/>
    </p>
    <p>
        我设置为2018password
    </p>
    <p>
        按ESC再输入:wq保存
    </p>
    <p>
        使用：
    </p>
    <pre class="fy-prettyprint linenums">service redis-server restart</pre>
    <p>
        重启redis
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/Pp4Lh6.png"/>
    </p>
    <p>
        如果没有任何报错信息就是没问题了，否则检查你的配置文件
    </p>
    <hr/>
    <p>
        <span style="font-size: 24px; color: #FFC000;">NodeBB的安装</span><br/>
    </p>
    <p>
        一切无误，进入最后步骤了（这当然不是全部的全部）
    </p>
    <p>
        如果没有git，使用：
    </p>
    <pre class="fy-prettyprint linenums">sudo apt-get install -y git build-essential</pre>
    <p>
        安装它<br/>
    </p>
    <p>
        不知道有没有，使用
    </p>
    <pre class="fy-prettyprint linenums">git</pre>
    <p>
        如果返回了一大串东西则是安装了，返回“Command &#39;git&#39; not found, but can be installed with:......”则是没有安装
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/Pp4qtx.png"/>
    </p>
    <p>
        安装完毕后进入一个你喜欢的目录<br/>
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/Pp5pBd.png"/>
    </p>
    <p>
        在新版本的Ubuntu，你需要先
    </p>
    <pre class="fy-prettyprint linenums">mkdir /www           #你喜欢的名字，带不带/有很大区别的</pre>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/Pp4j1O.png"/>
    </p>
    <p>
        然后再
    </p>
    <pre class="fy-prettyprint linenums">cd /www</pre>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/Pp59HA.png"/>
    </p>
    <p>
        使用命令，从git克隆nodebb
    </p>
    <pre class="fy-prettyprint linenums">sudo git clone -b v1.9.x https://github.com/NodeBB/NodeBB.git nodebb #这将在 /home 下面 新建一个名叫nodebb的文件夹并且会把程序从Github克隆到里面</pre>
    <p>
        在大陆，尤其是网络环境不好的情况下，这个过程可能会非常漫长（解决方案见<a href="https://blog-old.gaein.cn/post/NodeBB-Linux-CentOS.html）">https://blog-old.gaein.cn/post/NodeBB-Linux-CentOS.html）</a><br/>
    </p>
    <p>
        目前NodeBB已经更新v1.10.x，请将“v1.9.x”替换成“v1.10.x”在发送命令，以获取最新版
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/Pp5SnH.png"/>
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/Pp5PAI.png"/>
    </p>
    <p>
        在50kib/s的速度下，需要将近16分钟！
    </p>
    <p>
        妈呀，终于完成了，合个影<br/>
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/Pp5GgU.png"/>
    </p>
    <p>
        输入：<br/>
    </p>
    <pre class="fy-prettyprint linenums">cd nodebb</pre>
    <p>
        进入目录
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/Pp583T.png"/>
    </p>
    <p>
        使用：<br/>
    </p>
    <pre class="fy-prettyprint linenums">./nodebb setup #尽管我使用的是./nodebb start &lt;=习惯的锅，由于没有运行过，仍然会进行安装</pre>
    <p>
        进行安装
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/Pp53CV.png"/>
    </p>
    <p>
        可能还需要一些时间，顺便一提，在新版的NodeBB（1.7以后，也不算新啦！）中，可以使用：
    </p>
    <pre class="fy-prettyprint linenums">./nodebb install</pre>
    <p>
        进行可视化安装，也就是在服务器上面进入
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/24/P9Gyrj.png" alt="" style="" width="1078" height="827"/>
    </p>
    <p>
        <a href="http://127.0.0.1:4567">http://127.0.0.1:4567</a> 
    </p>
    <p>
        进行安装，和普通的PHP博客论坛什么的差不多，很简单！
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/PpIS2T.png"/>
    </p>
    <p>
        GOOGLE字体库全责！
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/23/PpICMF.png"/>
    </p>
    <p>
        再次成功翻车！
    </p>
    <p>
        无奈挂VPN
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/24/P9GDxg.png" alt="" style="" width="903" height="617"/>
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/24/P9G2aq.png" alt="" style="" width="1359" height="441"/>
    </p>
    <p>
        这个就是安装完成了，需要你输入一些信息
    </p>
    <p>
        第一个URL used... 如果有相要对外开放，请输入实际使用的域名:端口（4567），如果使用的不是这里设置的域名，会出现 无法连接的现象
    </p>
    <p>
        第二个是直接回车
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/24/P9G0G8.png" alt="" width="390" height="50"/>
    </p>
    <p>
        第三个我们安装的数据库是redis，熟入redis即可
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/24/P9GBRS.png" alt="" width="514" height="94"/>
    </p>
    <p>
        然后是前面设置的数据库密码（LINUX输入密码不会显示任何东西！）
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/24/P9GsMQ.png" alt="" width="320" height="132"/>
    </p>
    <p>
        管理员用户名和密码
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/24/P9GgZn.png" alt="" width="428" height="139"/>
    </p>
    <p>
        完成设置
    </p>
    <p>
        <img src="https://s1.ax1x.com/2018/06/24/P9GRI0.png" alt="" style="" width="1371" height="807"/>
    </p>
    <p>
        NODEBB基础搭建完成！<br/>
    </p>
    <p>
        <br/>
    </p>
</div>

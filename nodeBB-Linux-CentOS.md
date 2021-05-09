---
title: 在CentOS上搭建现代化的论坛Nodebb|NodeBB安装记录（Linux-CentOS篇）
author: Gaein nidb
tags: 
  - Linux
  - nodeBB
  - CentOS
  - 网站建设
categories: [网站建设]
date: 2018-07-21 19:20:03
---
<p>
    CentOS在各种服务器上使用的较多，所以捏，就来尝试一下CnetOS上安装NodeBB
</p>
<p>
    CentOS 7 on VMWare
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/21/P8H8tf.jpg" alt="" data-load="full" style="" width="1717" height="935"/>
</p>
<p>
    不过我这次选择的数据库是MongoDB
</p>
<p>
    一、准备工作
</p>
<p>
    打开终端，切换到root用户
</p>
<pre class="fy-prettyprint linenums">su root</pre>
<p>
    <img src="https://s1.ax1x.com/2018/07/21/P8H3AP.jpg" alt="https://s1.ax1x.com/2018/07/21/P8H3AP.jpg"/>
</p>
<p>
    关闭SELinux（有些可能没有，跳过）
</p>
<pre class="fy-prettyprint linenums">vim /etc/sysconfig/selinux</pre>
<p>
    如果没有vim（见<a href="https://blog-old.gaein.cn/post/Nodebb-Linux-Ubuntu.html" target="_self" textvalue="https://blog-old.gaein.cn/post/Nodebb-Linux-Ubuntu.html">https://blog-old.gaein.cn/post/Nodebb-Linux-Ubuntu.html</a>
</p>
<p>
    就用yum安装呗
</p>
<pre class="fy-prettyprint linenums">yum install vim ##如果 vim 指令未安装，那就安装它</pre>
<p>
    按下i键（当然如果你想知道有什么键可以自行百度）
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/21/P8HOud.jpg" alt="" data-load="full" style="" width="750" height="521"/>
</p>
<p>
    找到 <code>SELINUX</code> 然后修改成 <code>disabled</code>.
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/21/P8Hbge.jpg" alt="" data-load="full" style="" width="756" height="517"/>
</p>
<p>
    按下ESC退出编辑模式，输入:，再输入退出并保存命令：wq
</p>
<pre class="fy-prettyprint linenums">yum update
yum -y install epel-release
yum -y groupinstall &quot;Development Tools&quot;
yum -y install git ImageMagick ImageMagick-devel##更新系统并安装一些基础工具</pre>
<p>
    <img src="https://s1.ax1x.com/2018/07/21/P8HqjH.jpg" alt="" width="619" height="477"/>
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/21/P8bm5V.jpg" alt="" data-load="full" style="" width="769" height="547"/>
</p>
<p>
    二、安装NodeJS
</p>
<p>
    在Github可以找到最新的nvm安装命令
</p>
<p>
    <a href="https://github.com/creationix/nvm" _src="https://github.com/creationix/nvm">https://github.com/creationix/nvm</a><br/>
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/21/P8buCT.jpg" alt="" data-load="full" style="" width="1098" height="732"/>
</p>
<p>
    所以我们使用
</p>
<pre class="fy-prettyprint linenums">curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
##或者
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash</pre>
<p>
    安装nvm（这是一个快速部署的脚本）
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/21/P8beU0.jpg" alt="" data-load="full" style="" width="744" height="193"/>
</p>
<p>
    使用
</p>
<pre class="fy-prettyprint linenums">export NVM_DIR=&quot;$HOME/.nvm&quot;
[ -s &quot;$NVM_DIR/nvm.sh&quot; ] &amp;&amp; . &quot;$NVM_DIR/nvm.sh&quot; ## This loads nvm</pre>
<p>
    把nvm添加到环境变量
</p>
<p>
    使用
</p>
<pre class="fy-prettyprint linenums">source ~/.bashrc</pre>
<p>
    令环境变量生效
</p>
<p>
    最后使用<br/>
</p>
<pre class="fy-prettyprint linenums">command -v nvm</pre>
<p>
    检查是否安装成功
</p>
<p>
    安装成功之后，如果是国内的机器需要使用
</p>
<pre class="fy-prettyprint linenums">export NVM_NODEJS_ORG_MIRROR=http://npm.taobao.org/mirrors/node
export NVM_IOJS_ORG_MIRROR=http://npm.taobao.org/mirrors/iojs</pre>
<p>
    让nvm使用淘宝源<br/>
</p>
<pre class="fy-prettyprint linenums">npm config set registry http://registry.npm.taobao.org/</pre>
<p>
    让npm使用淘宝源
</p>
<p>
    使用<br/>
</p>
<pre class="fy-prettyprint linenums">nvm install --lts</pre>
<p>
    安装lts版本的nodejs
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYfSzj.jpg" alt="" data-load="full" style="" width="723" height="233"/>
</p>
<p>
    之后记得使用
</p>
<pre class="fy-prettyprint linenums">npm -v</pre>
<p>
    和
</p>
<pre class="fy-prettyprint linenums">nodeb -v</pre>
<p>
    检查版本，如果成功输出版本号，则安装成功
</p>
<p>
    三，安装数据库（MongoDB）
</p>
<p>
    使用命令<br/>
</p>
<pre class="fy-prettyprint linenums">vim /etc/yum.repos.d/mongodb-org-3.6.repo</pre>
<p>
    创建文件并且输入i进行编辑，将以下内容粘贴进去
</p>
<pre class="fy-prettyprint linenums">[mongodb-org-3.6]name=MongoDB 
Repositorybaseurl= 
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc</pre>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYWzWQ.jpg" alt="" data-load="full" style="" width="781" height="534"/>
</p>
<p>
    输入:wq保存并退出
</p>
<p>
    再使用<br/>
</p>
<pre class="fy-prettyprint linenums">yum updata</pre>
<p>
    更新
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYfkwV.jpg" alt="" width="380" height="181"/>
</p>
<p>
    使用<br/>
</p>
<pre class="fy-prettyprint linenums">yum -y install mongodb-org</pre>
<p>
    安装mongoDB
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYWvFS.jpg" alt="" data-load="full" style="" width="738" height="506"/>
</p>
<p>
    因为是国外的服务器所以速度不行，当然也可以自行在使用update之前把链接替换为国内的镜像站
</p>
<p>
    完成之后使用
</p>
<pre class="fy-prettyprint linenums">service mongod start ##启动 Mongodb
mongod --version ##查看 Mongodb 版本</pre>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYf9Qs.jpg" alt="" width="555" height="478"/>
</p>
<p>
    <span style="color: ##0070C0;">有一点需要注意：如果版本3.4.10的话需要升级，因为3.4.10以下有风险</span>
</p>
<p>
    安装完成后，为了消除 MongoDB 的性能警告，需要关闭 Linux 巨型页：
</p>
<p>
    使用
</p>
<pre class="fy-prettyprint linenums">echo never &gt; /sys/kernel/mm/transparent_hugepage/enabled
echo never &gt; /sys/kernel/mm/transparent_hugepage/defrag</pre>
<p>
    来关闭
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYfKyR.jpg" alt="" data-load="full" style="" width="744" height="63"/>
</p>
<p>
    不同于官方文档，在获取nodebb之前，我选择先进行数据库设置
</p>
<p>
    输入
</p>
<pre class="fy-prettyprint linenums">mongo</pre>
<p>
    进入mongoDB的命令行
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYfPLq.jpg" alt="" data-load="full" style="" width="777" height="373"/>
</p>
<p>
    使用
</p>
<pre class="fy-prettyprint linenums">use nodebb</pre>
<p>
    新建数据库（名为nodebb）
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYfFe0.jpg" alt="" width="224" height="64"/>
</p>
<p>
    使用
</p>
<pre class="fy-prettyprint linenums">db.createUser({user: &quot;nodebb&quot; ,pwd: &quot;替换成你的密码&quot; ,roles:[ &quot;readWrite&quot; ]})</pre>
<p>
    添加用户角色<span style="color: ##0070C0;">（牢记用户名（此处为nodebb）和密码！）</span>
</p>
<p>
    <span style="color: ##0070C0;"><img src="https://s1.ax1x.com/2018/07/24/PYfAoT.jpg" alt="" data-load="full" style="" width="767" height="113"/></span>
</p>
<p>
    如果需要在nodebb后台查看数据库数据，输入以下代码
</p>
<pre class="fy-prettyprint linenums">db.grantRolesToUser( &quot;nodebb&quot; ,[{ role: &quot;clusterMonitor&quot;, db: &quot;admin&quot; }]);</pre>
<p>
    打开权限认证，使用命令
</p>
<pre class="fy-prettyprint linenums">vim /etc/mongod.conf</pre>
<p>
    取消注释并改为
</p>
<pre class="fy-prettyprint linenums">security:   
    authorization: enabled</pre>
<p>
    如图所示
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYfeW4.jpg" alt="" data-load="full" style="" width="752" height="270"/>
</p>
<p>
    使用<br/>
</p>
<pre class="fy-prettyprint linenums">service mongod restart</pre>
<p>
    重启mongoDB
</p>
<p>
    四、安装NodeBB
</p>
<p>
    如果没有git，请使用
</p>
<pre class="fy-prettyprint linenums">yum install git</pre>
<p>
    中文社区的文档中已经说明
</p>
<blockquote>
    <p>
        <em><strong>在开始之前:</strong></em><br/><strong>① 提前切换成root用户，免得麻烦。</strong><br/><strong>② 不要直接下载 Github 上发布的 release 压缩包</strong>，那里面没有 <code>.git/config</code> 配置文件，而在安装过程中需要读取配置文件的值。正确的做法是使用 Git 从 GitHub 上克隆源代码。<br/>
    </p>
</blockquote>
<p>
    新建一个目录（如果是云服务器的话最好放在数据盘里面，默认的/root目录不会太大，不过因为我比较懒就直接这样了）
</p>
<p>
    可以使用
</p>
<pre class="fy-prettyprint linenums">mkdir 目录</pre>
<p>
    来新建文件夹（注意有&##39;/&##39;和没有的区别）
</p>
<p>
    使用
</p>
<pre class="fy-prettyprint linenums">cd 目录</pre>
<p>
    进入刚刚新建的目录（同样注意&##39;/&##39;）
</p>
<p>
    使用
</p>
<pre class="fy-prettyprint linenums">git clone -b v1.10.x https://github.com/NodeBB/NodeBB.git nodebb##把nodebb克隆进nodebb文件夹</pre>
<blockquote>
    <p>
        现在，你可以使用国内的镜像仓库(<code>https://gitee.com/NodeBB-CN/NodeBB.git</code>)来提高连接速度。<br/>
    </p>
</blockquote>
<p>
    这是中文社区文档上的话，但是...
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYfCyn.jpg" alt="" data-load="full" style="" width="754" height="76"/>
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/25/PYLpqg.jpg" alt="" width="457" height="55"/>（这个是当时没有截图后来用物理机补的:D）
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/25/PYLCZQ.jpg" alt="" width="453" height="420"/>嗯...
</p>
<p>
    所以我闲的创建了一个仓库，可以使用 <a href="https://gitee.com/nidb/NodeBB.git" _src="https://gitee.com/nidb/NodeBB.git">https://gitee.com/nidb/NodeBB.git</a><br/>
</p>
<p>
    使用
</p>
<pre class="fy-prettyprint linenums">./nodebb setup</pre>
<p>
    来安装，当然，上一篇（<a href="https://blog-old.gaein.cn/post/Nodebb-Linux-Ubuntu.html" target="_blank">在Ubuntu上搭建现代化的论坛Nodebb|NodeBB安装记录（Linux-Ubuntu篇）</a>）中说过，可以使用可视化的界面来安装，使用
</p>
<pre class="fy-prettyprint linenums">./nodebb install</pre>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYfVFU.jpg" alt="" data-load="full" style="" width="1722" height="937"/>
</p>
<p>
    和普通的博客安装差不多啦！
</p>
<p>
    说明：
</p>
<p>
    Web Address(URL)是以后访问的地址，如果是搭建的论坛记得写域名（可以不加端口，以后在nginx反代理）当然，建议先使用默认的调试。注意不要忘记“http://”，结尾不要加&##39;/&##39;，只有这个链接能够访问论坛，其他链接访问会显示链接断开（比如我有一个服务器，IP是1.2.3.4，域名www.exp.com和exp.com都A记录过去了，这里我填写www.exp.com，那么exp.com和1.2.3.4访问的时候都会显示与“NodeBB的链接断开”）<br/>
</p>
<p>
    Username，Email Address ，Password是论坛管理员的账号邮箱密码，牢记。邮箱要填写可以用的，以便以后开邮件验证功能的时候不用再去修改
</p>
<p>
    Confirm Password是确认密码
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYfZYF.jpg" alt="" data-load="full" style="" width="778" height="693"/>
</p>
<p>
    数据库选择MongoDB
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYful9.jpg" alt="" data-load="full" style="" width="1118" height="185"/>
</p>
<p>
    Redis的话自己去网上看资料吧23333
</p>
<p>
    中文社区的文档都很详细的，这个是CentOS+Redis的链接：<a href="https://www.kancloud.cn/a632079/nodebb-cn/372107" _src="https://www.kancloud.cn/a632079/nodebb-cn/372107">https://www.kancloud.cn/a632079/nodebb-cn/372107</a>&nbsp; ，至于Ubuntu，看我以前的博文
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYfnSJ.jpg" alt="" data-load="full" style="" width="726" height="685"/>
</p>
<p>
    Host IP or address of your MongoDB instance是数据库的地址，如果使用的是本机，默认即可
</p>
<p>
    Host port of your MongoDB instance是数据库端口，如果没有改过，默认即可
</p>
<p>
    MongoDB username是用户名，Password of your MongoDB database是密码，前面设置过的
</p>
<p>
    MongoDB database name是数据库名，前面use nodebb创建的
</p>
<p>
    点击Install NodeBB安装
</p>
<p>
    终端已经有反应了
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYflex.jpg" alt="" data-load="full" style="" width="1034" height="797"/>
</p>
<p>
    然后开始部署
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYfMO1.jpg" alt="" data-load="full" style="" width="1014" height="637"/>
</p>
<p>
    这可能需要几分钟，部署完成后提示你使用
</p>
<pre class="fy-prettyprint linenums">./nodebb start</pre>
<p>
    开启nodebb
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYf7hF.jpg" alt="" data-load="full" style="" width="1056" height="658"/>
</p>
<p>
    NodeBB已经成功运行，并且提示你三个常用的命令：分别是停止、日志（实时）、和重启
</p>
<p>
    <img src="https://s1.ax1x.com/2018/07/24/PYfGFO.jpg" alt="" data-load="full" style="" width="1529" height="572"/>
</p>
<p>
    打开<a href="http://127.0.0.1:4567" _src="http://127.0.0.1:4567">http://127.0.0.1:4567</a><br/>
</p>
<p>
    已经可以正常访问！<br/>
</p>

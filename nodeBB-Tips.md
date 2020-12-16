---
title: NodeBB小贴士——也是所有云服务器的小贴士：记得在相关软件中开启端口
author: Gaein nidb
tags: 
  - nodeBB
  - Linux
  - tips
categories: [网站建设]
date: 2018-07-25 01:27:20
---
    因为没有在面板中开启端口，碰壁过好几次
    回来想想哭笑不得，但是这样的例子还不止我一个
 #   所以提示大家，记得在
         1.   服务器系统防火墙
         2.   服务器系统安全狗
         3.   服务器自己安装的面板
         4.   服务商提供的管理面板
         5.   服务商提供的安全策略组
        
    中开启相应端口

 #   NodeBB需要端口

            80，http
            4567，默认端口（记得添加你在config.json中定义的）
            6379，redis
            27017，mongodb
            443，SSL
       按需添加
    <img src="https://s1.ax1x.com/2018/07/25/PYOYhn.jpg" alt="" data-load="full" style="" width="1697" height="548"/>

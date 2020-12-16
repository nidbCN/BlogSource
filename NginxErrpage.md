title: 'nginX设置错误页面'
author: Gaein nidb
categories:
  - 网站建设
tags:
  - nginx
  - 网站建设
  - HTTP错误
cover: 'https://s2.ax1x.com/2019/06/07/V0Gy90.png'
date: 2019-06-07 18:40:33
---
最近几天完善了一下网站，尤其是nginx默认的丑的要死的错误页面（标题图那样的）。今天把nginx配置错误页面的方法写在这里
<!-- more -->
首先在网上搜教程，说必须要有
` fastcgi_intercept_errors on;  `
自定义的错误页面才会生效，但是我删除这句话仍然可以正常跳转。大家依据实际需求添加。
接下来就是配置错误页面的具体代码了
`  error_page 404 /404.html;
   error_page 502 /502.html;
   location = /404.html{
       root /data/errpages;
   }
   location = /502.html{
       root /data/errpages;
   } 
`
error_page的意思是设置错误页，后面接状态码和页面文件，然后使用location来跳转。由于location的存在，这部分代码只能写在server区块里面。

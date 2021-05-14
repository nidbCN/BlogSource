---
title: NodeBB设置——使用NginX进行反代理并优化
author: Gaein nidb
tags: 
  - 网站建设
  - Linux
  - nodeBB
categories: [网站建设]
date: 2018-07-25 21:16:00
---
nodeBB在默认的4567开启，通常需要nginx作为反代理才能更好的访问，并且nginx也能开启一些优化使访问更快速
<!-- More -->
默认安装nginx的配置文件在 /etc/nginx/nginx.conf 使用

vim /etc/nginx/nginx.conf

编辑配置文件，在http{}之间，追加
```
server { 
  listen 80;  
  server_name www.xxx.com; ## 你的域名

  location / 
  {        
    proxy_set_header X-Real-IP $remote_addr; 
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;  
    proxy_set_header X-Forwarded-Proto $scheme;    
    proxy_set_header Host $http_host;    
    proxy_set_header X-NginX-Proxy true;  
    proxy_pass http://127.0.0.1:4567; 
    proxy_redirect off; 
    ## Socket.IO Support
    proxy_http_version 1.1;  
    proxy_set_header Upgrade $http_upgrade;    
    proxy_set_header Connection "upgrade";
  }    
}
```
因为我网站跑的服务器在香港，配置又不行，所以只能想办法来优化一下

先上整个的配置文件，因为用了宝塔面板（SSL实在弄不好）所以配置文件可能与其他的有出入，请自行修改
```
server
{
    ##基础设置
    listen 80;
    listen 443 ssl;
    server_name www.minecrafter.cn;
    
    ##错误页面
    error_page 502  /502.html;
    location = /502.html 
    {
       root /502;
       internal;
    }    
    
    ##反代理设置
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header Host $http_host;
    proxy_set_header X-NginX-Proxy true;
    ##proxy_pass http://127.0.0.1:4567;
    ##proxy_pass http://io_nodes;
    proxy_redirect off;
    ## Socket.IO Support
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    
    ##gizip设置 
    gzip            on;
    gzip_min_length 1000;
    gzip_proxied    off;
    gzip_types      text/plain application/xml text/javascript application/javascript application/x-javascript text/css application/json;

    ##SSL-START SSL相关配置，请勿删除或修改下一行带注释的404规则
    ##error_page 404/404.html;
    ##HTTP_TO_HTTPS_START
    if ($server_port !~ 443)
    {
        rewrite ^(/.*)$ https://$host$1 permanent;
    }
    ##HTTP_TO_HTTPS_END
    ssl_certificate    /etc/letsencrypt/live/www.minecrafter.cn/fullchain.pem;
    ssl_certificate_key    /etc/letsencrypt/live/www.minecrafter.cn/privkey.pem;
    ssl_protocols TLSv1.1 TLSv1.2;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    error_page 497  https://$host$request_uri;
    ##SSL-END

    ##静态资源代理设置
    location @nodebb 
    {
        proxy_pass http://io_nodes;
    }
    location ~ ^/assets/(.*) 
    {
        root /data/nodebb/;
        try_files /build/public/$1 /public/$1 @nodebb;
    }
    location /plugins/ 
    {
        root /data/nodebb/build/public/;
        try_files $uri @nodebb;
    }
    location / 
    {
        proxy_pass http://io_nodes;
    }
}
    ##负载均衡设置
upstream io_nodes 
{
    ip_hash;
    server 127.0.0.1:526;
    server 127.0.0.1:527;
}
```
这是最终版本，有负载均衡，资源代理，压缩

不需要压缩删除 ##gzip设置的内容即可

不需要负载均衡（仅一个端口）删除 ##负载均衡的内容，将 ##静态资源代理设置的内容中
```
proxy_pass http://io_nodes;
```
改成
```
proxy_pass  ##端口依情况而定
```
不需要静态资源代理删除 ##静态资源代理内容，如果有负载均衡，删除
```
##proxy_pass http://io_nodes;
```
前的“##”

没有则删除
```
proxy_pass http://127.0.0.1:4567; ##端口依情况而定
```
前的“##”


除了nginx之外要做的

集群使用：

默认情况下，NodeBB将在一个进程上运行，某些调用可能比其他进程更长，从而导致相同资源的延迟或队列等待。要解决这个问题，可以通过添加多个端口到config.json中来指示NodeBB在多个进程上运行。

我习惯把端口数目设置为核心数，首先使用
```
cat /proc/cpuinfo| grep "cpu cores"| uniq
```
查看核心数目（我的四核）

在nodebb目录下（就是输./nodebb setup那个目录），使用
```
vim config.json
```
编辑配置文件

将与端口有关的port:4567,改成
```
{    
    "port": ["4567", "4568"]  //这将启动2个进程，分别监听这2个端口
}
```
具体为什么可以百度JSON格式，如果重启nodebb出错请检查这个文件，端口随便写，不要占用80(HTTP),22(SSH),443(SSL),6379(Redis),27017(MongoDB)等

在Nginx配置文件中加入
```
upstream io_nodes 
{
    ip_hash;
    server 127.0.0.1:4567;
    server 127.0.0.1:4568;
}
```
端口依config内容而定

然后把以前配置文件中的

proxy_pass 一行

改成
```
proxy_pass http://io_nodes;
```
即可


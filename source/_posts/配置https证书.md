---
title: 配置https证书
date: 2018-03-21 10:42:30
tags: Linux
categories: Linux
---


#### 前言
###### 买了个域名，这两天在备案解析域名，有服务器已经有两三年了，但是这个域名我是第一次配置
###### 不过也挺简单的

#### 开撸
###### 下载腾讯云证书
<img src="http://p4j7qpj9e.bkt.clouddn.com/https-1.png">

###### 我的是lnmp搭建的nginx服务器，将nginx文件夹里面的两个文件全部存入服务器/usr/local/nginx/下
###### 修改/usr/local/nginx/nginx.conf
###### 还好实习的时候一个实习前辈让我修改文档前先把原文档的内容复制出来，要不就gg了
###### 改了几遍结果如下(只需修改server内容即可)
	server
	{
	    listen 80 default_server;
	    #listen [::]:80 default_server ipv6only=on;

	    ######################外加######################
	    error_page 497  https://www.域名;             #访问http时强制其访问https
	    ssl on;
	    listen 443 ssl;
	    ssl_certificate 1_域名_bundle.crt;         #两个复制的文件
	    ssl_certificate_key 2_域名.key;
	    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
	    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
	    ssl_prefer_server_ciphers on;
	    ssl_session_timeout 5m;
	    server_name www.域名;                      #域名      
	    #################################################

	    index index.html index.htm index.php;
	    root  /home/wwwroot/default;

	    #error_page   404   /404.html;
	    include enable-php.conf;

	    location /nginx_status
	    {
	        stub_status on;
	        access_log   off;
	    }

	    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
	    {
	        expires      30d;
	    }

	    location ~ .*\.(js|css)?$
	    {
	        expires      12h;
	    }

	    location ~ /\.
	    {
	        deny all;
	    }

	    access_log  /home/wwwlogs/access.log  access;
	}
#### 重启
###### 检查是否成功
	/bin/nginx -t
###### 重启nginx
	/etc/init.d/nginx restart
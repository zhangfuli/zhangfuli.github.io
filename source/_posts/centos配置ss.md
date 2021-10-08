---
title: centos配置ss
date: 2017-05-21 22:23:11
tags: Linux
categories: Linux
---

### 安装
	pip install shadowsocks

### 配置

#### 新建/etc/shadowsocks.json,并写入

	{
    "server":"XXXX",   
    "server_port":443,    
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"XXXX", 
    "timeout":600,           
    "method":"aes-256-cfb",   
    "fast_open": false
}

### 运行

	ssserver -c /etc/shadowsocks.json -d start
	ssserver -c /etc/shadowsocks.json -d start
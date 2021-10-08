---
title: docker从入门到实战
date: 2018-05-12 17:37:41
tags: Linux
categories: Linux
---


#### 前言
###### 这次博文主要记录《Docker从入门到实战》这本书的笔记
#### Docker基本命令
###### 所有命令都可以用–help来查看详情
###### 进入某个镜像
	docker attach | docker exec 
	docker exec -it ubuntu /bin/bash
###### 列出所有在运行的镜像
	docker ps 
	docker ps -a
###### 提交容器
	docker commit
###### 从容器复制文件到宿主机
	docker cp 
###### 创建容器, 通过docker start containerID, docker 	stop containerID来停止
	docker create
###### 查看容器内部的变化
	docker diff
###### 杀死某容器
	docker kill
###### 退出某容器
	ctrl + P + Q
###### 构建镜像
	docker build
###### 查看本地镜像
	docker images
###### 删除本地镜像
	docker rmi
#### 常见问题
###### 1.Docker for Windows: error pulling image configuration: i/o timeout
###### 解决方案: 首先查看docker用的是哪个网络, 我的用的是VirtualBox Host-only #3
<img src="http://p4j7qpj9e.bkt.clouddn.com/docker%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E6%88%981.png">

###### 然后找到这个网络并将其DNS的地址改为8.8.8.8
<img src="http://p4j7qpj9e.bkt.clouddn.com/docker%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E6%88%982.png">
---
title: docker push
date: 2018-07-18 19:06:45
tags: Linux
categories: Linux
---

#### 前言
###### 本次主要介绍如何推送本地image到dockerhub

#### 步骤
###### 首先从公共仓库中pull一个镜像
	docker pull centos

###### 进入镜像进行修改
	docker run -itd centos /bin/bash
	docerk attach containerID
	修改
	ctrl+P+Q 退出

###### 保存镜像
	docker commit containerID test
	docerk tag test zhangfuli/test:latest   //修改名称, 上一步可以直接命为这个  

###### 推送
	docker push zhangfuli/test:latest //一定为docker name/仓库 name


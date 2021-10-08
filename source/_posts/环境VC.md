---
title: 环境VC++
date: 2015-06-14
tags: 环境
categories: 环境
---


### 前言
###### 今天终于有时间静下来看看ng2，写后台的哥们去陪媳妇了，吃在石大的项目暂且搁置

###### 找了基本上一上午发现了你个不错的CLI工具angular-cli，其实原先也在yeoman下载过一些gengerators感觉不怎么顺手

###### ok，执行
	npm install -g angular-cli进行安装

###### error！！

### 解决
###### 又是VC++的问题，每次都是这样，我记得上一次解决过，但是没有记下来。后来又重装了电脑，然后就再次碰见了这个error。

###### 现在解决，管理员下
	npm install --global --production windows-build-tools

###### 再不行就
	 npm update

###### 讲真的，windows。。。。。。。。不说啥了
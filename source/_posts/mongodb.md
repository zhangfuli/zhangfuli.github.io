---
title: mongodb
date: 2016-11-29 21:53:32
tags: mongodb
categories: mongodb
---
### 配置
#### 1.下载并解压缩mongodb文件
#### 把<mongo_data_location>/bin目录添加到系统路径
#### 创建数据文件目录<mongo_data_location>/data/bin
#### 在控制台启动mongodb
	mongod --dbpath <mongo_data_location>/data/db
### 启动
#### 用port和dbpath参数启动mongodb
	mongod --dbpath <mongo_data_location>/data/db
#### 在新的cmd下(这里踩了好多坑)
	mongo
### 停止
	mongo
	use admin
	db.shudownServer()
#### 这里注意,执行完上述命令后会出现 trying reconnect to 127.0.0.1:27017 (127.0.0.1) failed
#### 但是你回到启动的cmd会发现,服务已经关闭

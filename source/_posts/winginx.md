---
title: winginx--php环境搭建
date: 2016-10-09 10:04:03
tags: 环境
categories: 环境
---

### 吐槽
###### 一直想做一个更改图片大小的脚本，用nodejs导入sharp包也没有成功(可能是我系统的原因,唔··,现在从装系统了,待会儿再试试)
###### 放弃了nodejs试试php，对于一个php小白来说配置环境太难了,服务器,数据库,php,头都炸啦
###### 还好问了一下同学,下载了一个xampp,但是服务器一直打不开

### 解决
###### 峰回路转,还好有winginx,内带着服务器、数据库、php的两个版本
###### 那怎么用呢？
###### 编辑好你的php文件打开服务器后,将你的.php文件放入wingx\Winginx\home\localhost\public_html这个目录下面
###### 浏览器运行localhost就可以了
###### 这是localhost
###### localhost/文件名.php就是你的页面
###### localhost:81     ------数据库界面
###### 用户名：root
###### 密码：       ---没有密码
###### localhost:82     超级数据库管理界面
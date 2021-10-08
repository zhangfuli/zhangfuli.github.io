---
title: 利用github和hexo搭建自己的博客
date: 2015-06-01
tags: hexo
categories: hexo
---

### 步骤
###### 首先，你要先自己注册一个Github的账户。自己去github官网注册个账号就ok了。
###### 搭建环境(必须的)  ---Node和git
###### 利用choco在cmd(管理员模式)下安装很方便。choco在前面已经讲过。可以自己去chocolaty官网上自己查找。
###### 安装开始

###### 执行如下命令

	npm install -g hexo
###### 进行安装

	hexo init
###### 进行初始化

###### 在Github里新建一个仓库，名字叫XXX(你Github的昵称).github.io

###### 下载安装Souretree(我用的是Souretree，你也可以选在别的软件)

###### 把XXX.github.io克隆到生成hexo的文件的public文件下(注：如果里面有文件的话先copy出来，让public为空文件夹)。

###### OK，在网址上输入XXX.github.io，你的专属blog完成了。

### 查看
###### PS:需要运行在cmd下找到你的blog所在的文件夹运行hexo g即可，如果想在本地看看你的页面hexo s
---
title: geckodriver
date: 2017-05-16 21:35:08
tags: python
categories: python
---

### 什么是geckodriver
###### geckodriver用来驱动firefox

###### 安装Xvfb	
	yum update
	yum install Xvfb
	yum -install libXfont
	yum install xorg-x11-fonts*
###### 安装selenium、pyvirtualdisplay

	pip install selenium

	pip install pyvirtualdisplay
	
### 配置

###### geckodriver是配合selenium来用的，我在centos7服务器上把下载下来的geckodriver放在在firefox的根目录里了。并且我也把它放在了/usr/local/bin/geckodriver
###### 如果没有放它会出现
	Message: 'geckodriver' executable needs to be in PATH. 

###### 其实很早就做到这一步了，然后运行发现了这个错误
	selenium: 'geckodriver' executable may have wrong permissions

###### 这个问题确实困扰了很长时间，加上现在一直在考试，脑核疼啊...

###### 最终还是找到了解决方案，运行一下，给geckodriver执行权限
	chmod +x /usr/local/bin/geckodriver

###### 我的天，竟然没执行

### 测试代码
	import selenium
	from selenium import webdriver      
	from pyvirtualdisplay import Display #无浏览器配置
	display = Display(visible = 0,size=(800,600))
	display.start()
	print "display.start()"

	driver = webdriver.Firefox()

	driver.get("http://www.baidu.com")
	print driver.current_url

---
title: 两个js、网页之间的通信
date: 2017-04-08 22:34:58
tags: HTML CSS
categories: HTML CSS
---


### 两个js之间的通信--笔记

###### 两个js之间通信，其中一个是写了一些全局(window.XXX)的或者经常用的数据，比如后台地址，这种情况。

###### 平行文件夹下，新建一个config.js，里面定义一个数据，如：
	var url = "http://XXXXXXXXXX"
###### 在你要调用的js下，直接url就可以了,其实不用在同级文件夹下建立就可以,
只要在html中引入js时config.js在你要调用数据的js前面就可以了

###### 在angular下，我通常情况下是把变量写在rootScope根作用域下

### 两个html之间的通信

###### 网上有很多版本，比如写在session或localstorage里面，但是我试过，两个相互独立的页面是不能调用的，单页面应用才可以

###### 我是将把要传送的数据卸载链接里面，当然是小数据，如
	<a href="./discuss.html#id=123" class="read">详情&评论</a>

###### 在另外一个页面里只需要对自己的url进行字符串或者正则切割就可以切割出想要的数据
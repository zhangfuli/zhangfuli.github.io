---
title: javascript高级程序设计20-23
date: 2017-12-19 10:42:30
tags: javascript
categories: javascript
---


### JSON
###### 对象
	{
	    name: "test"
	}
###### 数据结构
	{
	    "name": "test"
	}
##### JSON对象方法
###### JSON.stringify() //将json对象转化为数据结构
###### 参数
	let obj = {
	    name: "test",
	    sex: ['male', 'female']
	}
	JSON.stringify(obj)  
	JSON.stringify(obj, name)  //只保留name
	JSON.stringify(obj,name,4) //字符缩进4个空格
#### 数据存储
##### cookie
###### cookie构成：名称、值、域、路径、失效时间、安全标志
	Set-Cookie: name=value; domain=.wrox.com; expires= Mon, 22-Jan-07; secure
	document.cookie = "name=value; ....."
###### cookie 确实非常小，它的大小限制为4KB左右，是网景公司的前雇员 Lou Montulli 在1993年3月的发明。它的主要用途有保存登录信息，比如你登录某个网站市场可以看到“记住密码”，这通常就是通过在 Cookie 中存入一段辨别用户身份的数据来实现的
#### localStorage
	localStorage.setItem()
	localStorage.getItem()
#### sessionStorage
###### 用法同上
#### 浏览器缓存
<a href="https://segmentfault.com/a/1190000006741200">浏览器缓存</a>
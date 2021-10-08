---
title: python跨域
date: 2017-01-13 10:25:34
tags: python
categories: python
---

##### 要做挑战杯，从0开始用python写后台，串口通讯

##### 本来想用python的GUI来写的，但是觉得太丑了，于是乎，就用老办法python做后台，然后写前端

##### 迎面而来的，就是跨域问题。从网上找了好长时间也没有什么办法解决

##### 看到一个还算合适的，感谢他

<a href="https://segmentfault.com/a/1190000000753690">我的参考</a>

##### 可是写上之后并没有解决问题，但是问题已经换掉了，变成了

##### Credentials flag is 'true', but the 'Access-Control-Allow-Credentials' header is ''. It must be 'true' to allow credentials

##### 原先没写过python，加上这两天写numpy头要炸了，自己试着摸索一下吧。

	rst.headers['Access-Control-Allow-Credentials'] = 'true'

##### 加上了这一句，没想到，成了！接着就是angularjs请求了。嘿嘿

##### 代码如下

	from functools import wraps
	from flask import make_response


	def allow_cross_domain(fun):
	    @wraps(fun)
	    def wrapper_fun(*args, **kwargs):
	        rst = make_response(fun(*args, **kwargs))
	        rst.headers['Access-Control-Allow-Origin'] = '*'
	        rst.headers['Access-Control-Allow-Methods'] = 'PUT,GET,POST,DELETE'
	        allow_headers = "Referer,Accept,Origin,User-Agent"
	        rst.headers['Access-Control-Allow-Headers'] = allow_headers
	        return rst
	    return wrapper_fun


	@app.route('/hosts/')
	@allow_cross_domain
	def domains():
	    pass
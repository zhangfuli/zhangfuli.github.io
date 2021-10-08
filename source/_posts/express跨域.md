---
title: express跨域
date: 2017-03-07 21:28:22
tags: nodejs
categories: nodejs
---

### cors代码库

###### 安装方法
	npm install cors --save-dev

###### 使用方法

	var express = require('express'),
		cors = require('cors'),
		app = express();
	app.use(cors());
	app.listen(80, function(){
		console.log('The serve is running');
	});
	app.get('/data' , function(req, res, next){
		res.json({msg:"enabled for all originas"});
	});

###### angular前端跨域就简单了，只需在config里加入
	$httpProvider.defaults.withCredentials = true;


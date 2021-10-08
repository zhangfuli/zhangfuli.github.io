---
title: nodejs爬虫
date: 2016-11-04 14:29:14
tags: nodejs
categories: nodejs
---

#### 一直想写一个爬虫玩玩，试过python，这个语法太难搞，对于深谙C、JS的人来说一不小心就写错。
#### 还好JS的强大，nodejs可以操作各种文件流，而且nodejs可以操作DOM节点(cheerio)，这样做一个简单的爬虫就比较简单了
#### 这里我们需要配置一下环境(当然node是必须的)
### express
#### 这是nodejs的一个框架,安装的话执行
	npm install -g express
#### 安装完成之后，执行
	express spider(项目名)
#### 它会自动给你写好配置文件,配置好了之后执行
	npm install
#### 毕竟你会需要一些包,而这些包名都会在package.json里面写好

### cheerio
#### 这是nodejs的一个包,快速、灵活和精益核心jQuery专门为服务器的实现。
#### 其实就我的理解,nodejs是不会操作DOM的。JS才是操作DOM和浏览器的。
#### cheerio只是写方法$,类似于jq而已
#### 在工程中执行
	npm install cheerio --save-dev
#### 那--save-dev是什么意思呢？就是把cheerio写入你的工程，通过这些命令，我们会得到一个新的package.json。而且这些包也会写入node_modules

### request
#### 这个就是nodejs很平常的一个包了,用于发送请求的,同样，执行
	npm install request --save-dev
#### 把request写入工程

### 开始写爬虫
#### 把express生成的工程的app.js里面的东西删掉,毕竟我们也不需要这么多的包。引入必要的request、cheerio,然后就可以写爬虫了
#### 大家如果不知道request、cheerio的用法可以去npmjs官网查看
#### 扒下来的网页用cheerio切割就可以,有些html代码用cheerio不好切,就要用到正则了

### robot协定
#### 有些网页是不能随便乱扒的
#### 在网址上输入你想扒的网页的网址的主页+robot.txt仔细看看什么不能扒,小心违反相关法律。



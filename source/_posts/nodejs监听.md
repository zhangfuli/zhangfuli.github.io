---
title: nodejs监听
date: 2016-11-18 22:55:50
tags: nodejs
categories: nodejs
---

### 将定义事件添加到js对象

#### 事件使用EventEmitter对象来发出。这个对象包含在events模块中。
	var events = require('events');
	var emitter = new events.EventEmitter();
	emitter.emit("simpleEvent");
#### emit是一个发射器,当遇见simpleEvent时发射
#### 将事件添加到js对象

	function Obj(){
		Events.EventEmitter.call(this);
	}
	Obj.prototype._proto_ = events.EventEmitter.prototype;

	var obj = new Obj();
	obj.emit("someEvent");

### 把事件监听器添加到对象

#### .addListener(eventName,callback) : 将回调函数附加到对象的监听器中。每当eventName事件被触发时，回调函数被放置在队列中

#### .on(eventName,callback) : 同 .addListener()

#### .once(eventName,callback) : 只有eventName事件第一次被触发时,才执行回调

### 从对象中删除监听器

#### .listeners(eventName) :返回一个连接到eventName事件的监听器函数的数组

#### .setMaxListeners(n) : 如果多于n的监听器都加入到EventEmitter对象,就触发警报。他的默认值是10

#### .removeListener(eventName,callback) : 将callback函数从EventEmitter对象的eventName移除

### 例子 1
	
	// 引入 events 模块
	var events = require('events');
	// 创建 eventEmitter 对象
	var eventEmitter = new events.EventEmitter();

	// 创建事件处理程序
	var connectHandler = function connected() {
	   console.log('连接成功。');
	  
	   // 触发 data_received 事件 
	   eventEmitter.emit('data_received');
	}

	// 绑定 connection 事件处理程序
	eventEmitter.on('connection', connectHandler);
	 
	// 使用匿名函数绑定 data_received 事件
	eventEmitter.on('data_received', function(){
	   console.log('数据接收成功。');
	});

	// 触发 connection 事件 
	eventEmitter.emit('connection');

	console.log("程序执行完毕。");

### 例子 2

	var events = require('events');
	var eventEmitter = new events.EventEmitter();
	function Obj(){
		this.num = 0;
		this.getNum = function(){
			eventEmitter.emit("callback");
			console.log("This is obj.getNum");
		}
	}
	Obj.prototype._proto_ = events.EventEmitter.prototype;

	function callback(){
		console.log("This is callback");
	}
	var obj = new Obj();

	eventEmitter.on("callback",callback);

	obj.getNum();
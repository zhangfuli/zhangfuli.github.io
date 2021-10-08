---
title: javascript对象
date: 2016-11-17 21:09:40
tags: javascript
categories: javascript
---

### javascript对象

###### js有很多内置的对象,比如Number、Array、String、Date、Math.调用他们的时候语法很简单
	var x = new Number("5");

### 创建js对象
###### 语法
	function User(first,last){
		this.first = first;
		this.last = last;
		this.getName = function(){
			return this.first + "," +this.last;
		};
	}
###### 调用跟上面一样
	var myuser = new User("Brad","Dayley");
	console.log(myuser.getName());

### 使用原型对象模式

###### 每一个对象中的方法并不一定在创建的时候就写全,可以写在函数的外面。这样还有一个优点,创建原型函数，只在js加载时创建一次，而不是每穿件对象是被创建

###### 例子
	function User(first,last){
		this.first = first;
		this.last = last;
	}
	User.prototype._getName = function(){
		return this.first + "," +this.last;
	}
###### 或者
	User.prototype = {
		_getName : function (){
			return this.first + "," +this.last;
		}
	}
###### 调用的时候跟写在类内部一样调用
---
title: javascript高级程序设计11-13
date: 2017-10-31 10:42:30
tags: javascript
categories: javascript
---



### SelectorsAPI
###### querySelector()
	var body = document.querySelector("body");
	var myDiv = document.querySelector("#myDiv");
	var img = document.querySelector("img.button");  //img  .button
###### querySelectorAll()
###### 该方法返回的是NodeList
###### classList–用户CRUD某节点的css
	<div class ="bd user disabled"></div>
	div.classList.add("currect")   
	div.classList.remove("currect")
	div.classList.toggle("currect")
	div.classList.contains("currect")  //有就返回true
###### 焦点管理
###### 元素获得焦点的方式有页面加载、用户输入、focus
### 自定义数据属性
###### h5规定可以为元素添加非标准属性，但要添加data-,目的是为元素提供与渲染无关的信息。或者是提供语义\
	<div id="myDiv" data-appId="123"></div>    
	var div = document.getElementById("myDiv");
	console.log(myDiv.getAttribute("data-appId"));
### DOM0级事件
<script async src="//jsfiddle.net/3g6ypdz0/embed/js,html,css,result/dark/"></script>
### DOM2
###### 通过addEventListener绑定事件，IE–attachEvent绑定
###### DOM2级的事件规定了事件流包含三个阶段包括： 1：事件捕获， 2：处于目标阶段， 3：事件冒泡阶段
###### addEventListener事件可以显示的指定是使用事件捕获还是事件冒泡。
	addEventListener('click',doSomething2,true);
	true--事件捕获   false--事件冒泡

###### 无论在DOM0还是DOM2还是DOM3中都会在事件函数中传入事件对象
	let test = document.getElementsByClassName("test");   //返回的是数组对象

	test[0].addEventListener('click', function(){
	    console.log(arguments[0]);
	},false)
### Event对象的常见应用
###### event.preventDefault() //阻止默认事件
###### event.stopPropagation() //阻止冒泡
	test[0].addEventListener('click', function(e){
	    alert(e.clientX);
	},false)
	test[1].addEventListener('keyup', function(e){
	    alert(e.keyCode);
	},false)
### 自定义事件
<script async src="//jsfiddle.net/maLkhpzq/6/embed/js,html,css,result/dark/"></script>
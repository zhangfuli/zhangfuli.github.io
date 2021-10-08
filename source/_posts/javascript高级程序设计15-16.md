---
title: javascript高级程序设计11-13
date: 2017-12-10 10:42:30
tags: javascript
categories: javascript
---


#### 使用canvas画图
	<canvas id="canvas" width="200" height="200"></canvas>
	let canvas = document.querySelector("#canvas");
	  let context = canvas.getContext("2d");

	  let image = new Image(160,160);
	  image.src = "http://p0fuclq6b.bkt.clouddn.com/P2-1507020329.JPG";
	  document.body.append(image);

	  //绘制图形
	  context.fillStyle = "#ff0000"
	  context.fillRect(10,10,500,50)  //(起点x,起点y,宽度,高度)

	  context.fillStyle = "rgba(0,0,255,0.5)"  //半透明蓝色
	context.fillRect(30,30,50,50)

	context.clearRect(40,40,10,10)   //清除一块矩形区域

	  //绘制image
	  context.drawImage(image,50,50,50,50)
#### canvas压缩图片
<a href="http://leonshi.com/2015/10/31/html5-canvas-image-compress-crop/">链接</a>
###### 这里有一个canvas.toDataURL(‘image/jpeg’, quality); //跨域未解决？？？
<a href="https://github.com/think2011/localResizeIMG">压缩图片</a>

#### 拖放
###### 拖动某元素时，依次触发下列事件
	dragstart
	drag
	dragend
###### 当元素被拖动到一个有效的目标上时，依次触发
	dragenter
	dragover
	dragleave 或 drop
##### dataTransfer对象
###### 主要用于拖放前，设定参数，拖放后取得参数
	e.dataTransfer.setData("text","");  //键值对
	e.dataTransfer.getData("text");
##### 设置元素可拖动
	<div draggable="true"></div>
###### 拖动img时不会有拖动的痕迹
<script async src="//jsfiddle.net/2ok0Ldsn/embed/js,html,css,result/dark/"></script>

#### 媒体元素
###### 嵌入视频
	<video>
	    <source src="" type=""></source>
	    <source src="" type=""></source>
	</video>
###### 嵌入音频
	<audio>
	    <source src="" type=""></source>
	    <source src="" type=""></source>
	</audio>
#### MIME 类型
###### MIME (Multipurpose Internet Mail Extensions) 是描述消息内容类型的因特网标准。
###### MIME 消息能包含文本、图像、音频、视频以及其他应用程序专用的数据。
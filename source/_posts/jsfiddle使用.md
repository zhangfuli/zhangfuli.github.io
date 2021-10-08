---
title: jsfiddle使用
date: 2017-10-18 10:42:30
tags: 工具
categories: 工具
---




### 背景
###### 这个也是在查找js闭包的时候，无意中发现别人的博客为什么可以嵌入js在线编辑比如想下边的链接
<a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures">MDN Web Docs–闭包</a>


### 干货
##### 查找的都是只是给了干巴巴的语法，这令我这个小白很苦恼
	{% jsfiddle shorttag [tabs] [skin] [width] [height] %}
###### 这边看了下源码，是用iframe引用了jsfiddle,也就是上面的代码的作用
###### 首先,你要先把你的代码在jsfiddle的网站中里写一下,然后点击Embed,它就会把你的代码生成script标签
	<script async src="//jsfiddle.net/xAFs9/3/embed/js,html,css,result/dark/"></script>
###### 你可以直接CV这个script标签进你的markdown,你也可以按照语法,替换结果如下,当然长度和宽度自选,不填的话是自适应
	{% jsfiddle xAFs9/3 js,html,css,result dark %}
结果
<script async src="//jsfiddle.net/xAFs9/3/embed/js,html,css,result/dark/"></script>

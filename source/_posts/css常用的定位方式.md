---
title: css常用的定位方式
date: 2016-10-27 16:07:37
tags: javascript
categories: javascript
---

### 浮动定位
###### 浮动元素会从普通文档流中脱离，但浮动元素影响的不仅是自己，它会影响周围的元素对齐进行环绕。
###### 浮动可以让页面元素并排显示，浮动可以让元素一个挨着一个。浮动可以创建一个自然流布局，同时充许元素设置自身尺寸和其父元素容器的尺寸大小。
###### for example
###### HTML
	<div class="father">
	  <div class="box">Box</div>
	  <div class="box">Box</div>
	  <div class="box">Box</div>
	</div>
###### CSS

	.father{
		width:150px;
		background:#e8eae9;
	}
	.box{
		width:30px;
		height:30px;
		margin:10px;
		background:#8ec63f;
		float: left;
	}
###### DEMO演示效果(这里就不插入html了，markdown编译时自动换行影响效果)
<img src="images/float_demo.png" alt="演示1">

### clear属性
###### 有的时候，我们不希望一些元素会被旁边的浮动元素影响到，这个时候就需要用到clear属性了。
###### clear属性：确保当前元素的左右两侧不会有浮动元素。
###### 使用clear属性的时候要记住：clear只对元素本身的布局起作用,clear属性只适用于块级元素
###### HTML
	<div class="father">
	  <div class="box">Box</div>
	  <div class="box cl">Box</div>
	  <div class="box">Box</div>
	</div>
###### CSS
	.father{
		width:150px;
		background:#e8eae9;
	}
	.box{
		width:30px;
		height:30px;
		margin:10px;
		background:#8ec63f;
		float: left;
	}
	.cl{
		clear:left;
	}
###### DEMO
<img src="images/clear_demo.png" alt="演示2">

###### 当然，你的第二个box也可以另写一个class没有浮动
### overflow:hidden
###### 这个方法的关键在于触发了BFC
###### BFC:BFC是一个名词，是一个独立的布局环境，我们可以理解为一个箱子（实际上是看不见摸不着的），箱子里面物品的摆放是不受外界的影响的。转换为BFC的理解则是：BFC中的元素的布局是不受外界的影响（我们往往利用这个特性来消除浮动元素对其非浮动的兄弟元素和其子元素带来的影响。）并且在一个BFC中，块盒与行盒（行盒由一行中所有的内联元素所组成）都会垂直的沿着其父元素的边框排列。
###### HTML
	<div class="top_bottom"></div>
	<div class="father">
	  <div class="box">Box</div>
	  <div class="box">Box</div>
	  <div class="box">Box</div>
	</div>
	<div class="top_bottom"></div>
###### CSS
	.top_bottom{
		width:50px;
		height:50px;
		background:#000;
	}
	.father{
		width:150px;
		background:#e8eae9;
		overflow:hidden;
	}
	.box{
		width:30px;
		height:30px;
		margin:10px;
		background:#8ec63f;
		float: left;
	}
###### DEMO
<img src="images/overflow_demo.png" alt="演示3">

###### 这里也许看来了，overflow:hidden使父元素不被折叠。(颜色为灰色，仔细看还是能看出来的)
### overflow扩展
###### overflow有四种属性分别是visible,hidden,scroll,auto
###### overflow的默认属性是visible,可见的
###### hidden是当你盒模型超出的时候会被隐藏，当然常常被用于消除浮动
###### scroll和auto有类似的用法，就是出现滚动条
### after伪元素
###### 这种方法是生成一个元素来消除浮动，只是这个元素不可见，是假的
###### HTML
	<div class="top_bottom"></div>
	<div class="father">
	  <div class="box">Box</div>
	  <div class="box">Box</div>
	  <div class="box">Box</div>
	</div>
	<div class="top_bottom"></div>
###### CSS
	.top_bottom{
		width:50px;
		height:50px;
		background:#000;
	}
	.father{
		width:150px;
		background:#e8eae9;
	}
	.box{
		width:30px;
		height:30px;
		margin:10px;
		background:#8ec63f;
		float: left;
	}
	.father:after{
		content: ""; 
		visibility: hidden; 
		display: block; 
		height: 0px; 
		clear: both;
	}
###### 当然这里实现的demo和上面的是一样的

### position定位
###### position五大属性static、absolute、fixed、relative、inherit
### static(默认)
###### 当你没有为一个元素(例如div)指定定位方式时，默认为static，也就是按照文档的流式(flow)定位。除非你想覆盖从父元素继承来的定位系统。
### absolute(绝对定位)
###### 绝对定位会使元素从文档流中被删除，结果就是该元素原本占据的空间被其它元素所填充。
###### 需要设置position:absolute(表示绝对定位)，然后使用left、right、top、bottom属性相对于其最接近的一个具有定位属性的父包含块进行绝对定位。如果不存在这样的包含块，则相对于body元素，即相对于浏览器窗口。
###### HTML
	<div class="father">
		<div class="box"></div>
	</div>
###### CSS
	.father{
		width: 50px;
		height: 50px;
		background: green;
		margin-top:10px; 
		position: relative;
	}
	.box{
		width: 50px;
		height: 50px;
		border: red 10px solid;
		position: absolute;
		top: 70px;
	}
###### DEMO
<img src="images/position_demo.png" alt="演示4">

###### 注意:相对于最近的一个父本
### relative(相对定位)
###### ，需要设置position:relative（表示相对定位），它通过left、right、top、bottom属性确定元素在正常文档流中的偏移位置。
###### 相对于以前的位置移动，移动的方向和幅度由left、right、top、bottom属性确定，偏移前的位置保留不动。还在文件流中
###### HTML
	<div class="box"></div>
###### CSS
	.box{
		width: 50px;
		height: 50px;
		border: red 10px solid;
		position: relative;
		top: 70px;
	}
###### DEMO
<img src="images/relative_demo.png" alt="演示5">

###### 注意:这里是相对自己原来的位置,若不加relative则顶着浏览器上面
### fixed(固定定位)
###### 相对于浏览器屏幕的定位,不会随滚动条的滚动而变化
###### 这里就不给DEMO了,相信你一定有体会,百度搜索出来的东西,无论怎么滚滚动条,广告怎么拉都不走

---
title: javascript高级程序设计7-8
date: 2017-10-18 10:42:30
tags: javascript
categories: javascript
---



### 函数
###### 声明函数
###### function name(){},这个存在变量提升,而var name = function(){}不存在变量提升
### 递归
	function factorial(num){
	    if(num<=1){
	        return 1
	    }else{
	        return num*arguments.callee(num-1) //arguments.callee === factorical
	    }
	}
### 闭包
###### 闭包是指有权访问另一个函数作用域中的变量的函数。创建闭包的常见的方式就是在函数内部创建另一个函数
######当函数返回了一个闭包时。这个函数的作用域将会一直在内存中保存到闭包不存在为止
#### 推荐

###### 例一
	var name = "The Window";
	var object = {
	　　name : “My Object”,
	　　getNameFunc : function(){
	　　　　return function(){
	　　　　　　return this.name;
	　　　　};
	　　}
	};
	alert(object.getNameFunc()()); //The Window

###### 例二
<script async src="//jsfiddle.net/ffd10c2h/embed/"></script>
###### 例三
###### 下面的例子中
	result[i] = function(){
	    return i
	}
###### 每一个都是返回i,这个i的作用域在f()内,而自此时f()中的i=3,所以每个都返回3
###### 而当用for–in时返回的是0,1,2,这是为什么呢？继续探究
###### 修改代码如下
	function f(){
	    var result = new Array()
	    for(var i = 0;i<3;i++){
	        result[i] = function(){
	            console.log("内:"+this)
	            return i;
	        }
	    }
	    return result
	}
	var result = f()
	console.log(result)  //Array(3)
###### 3个f(),这里的f()不是上面的f函数 
	console.log(result[0]())
	/*
	* 内:function (){
	*            console.log("内:"+this)
	*            return i;
	*        },function (){
	*            console.log("内:"+this)
	*            return i;
	*        },function (){
	*            console.log("内:"+this)
	*            return i;
	*        }
	*
	 */
	console.log(result[0])
	/*ƒ (){
	*            console.log("内:"+this)
	*            return i;
	*        }
	*/
###### 结果如上,console.log(result0)与console.log(result[0])可以合理的解释为什么全是3
###### 而且内this指向的是result数组所以会有三个function(){}
###### 疑问：
###### 为什么console.log(“内：”+this)会出来3个function(){}
##### ES6
	function f(){
	    let result = new Array()
	    for(let i = 0;i<3;i++){
	        result[i] = function(){
	            return i;
	        }
	    }
	    return result
	}
	let result = f()
	for(let i=0;i<3;i++){
	    console.log(result[i]())
	}
###### 结果是0,1,2
###### 块级作用域
	(function(){
	    //这里是块级作用域
	})()
##### 作用:块级作用域是一个独立的作用域，多人协作时每个人有每个人的块级作用域可以互不影响,防止命名冲突
<script async src="//jsfiddle.net/j10nwnvt/1/embed/js,html,css,result/dark/"></script>
### BOM
###### window对象
###### window对象是指浏览器打开的窗口
###### window.名称   //全局对象
###### window.frame['name']
###### 窗口位置
###### chrome\opera\safari—-screenTop|screenLeft
###### firefox—-screenX|screenY
	var leftPos = (typeof window.screenLeft == 'number')?window.screenLeft:window.screenX;
	var rightPos = (typeof window.screenTop == 'number')?window.screenRight:window.screenY;

	window.moveTo(0,0);    //移动到0，0
	window.moveBy(0,100);  //向下移动100像素
###### 窗口大小
	outerWidth、outerHeight----返回浏览器窗口本身的大小
	innerWidth、innerHeight----返回容器中试图区的大小
	resizeTo()、resizeBy()----可以调整浏览器窗口的大小
###### 导航和打开窗口
	window.open()----打开新的浏览器窗口
	参数:URL、窗口目标、特性字符串、boolean(新页面是否取代浏览器历史纪录中当前加载页面)

###### 第二参数可以是:_self、_parent、_top、_blank
	opener属性:保存着打开他的原始窗口对象
	
###### 延时间歇延时
	setTimeout()、setTnterval()、clearTimeout()
###### 系统对话框
	alert()、confirm()、prompt()
###### location对象
###### 即是window的对象属性，也是document的对象属性
###### console.log(window.location)   //查看属性
###### 查询字符串参数
    /* 解析查询字符串 返回包含所有参数的一个对象 */  
	function getQueryStringArgs(){  

	   //取得查询字符串并去掉开头的问号  
	   var qs = (location.search.length > 0 ?       location.search.substring(1) : '');  

	   //保存数据的对象  
	   args = {};  

	   //取得每一项  
	   var items = qs.length ? qs.split('&') : [],  
      item = null,  
      name = null,  
      //在for循环中使用  
      i = 0, len = items.length;  

	   //逐个将每一项添加到args对象中  
	   for(i = 0 ; i < len; i++){  
	      item = items[i].split('=');  
	      name = decodeURIComponent(item[0]);  
	      value = decodeURIComponent(item[1]);  

	      if(name.length){  
	         args[name] = value;  
	      }  
	   }  

	   return args;  
	}  
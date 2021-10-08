---
title: javascript高级程序设计1-3
date: 2017-09-10 10:42:30
tags: javascript
categories: javascript
---

### 前言
###### 这是javascript高级程序设计1-3章一些零碎的知识点
	javacript = ECMAscript + BOM(浏览器对象模型) + DOM
### DOM级别
###### DOM1级由两个模块组成：DOM核心(DOM Core)和DOM HTML
###### DOM核心规定的是如何映射基于XML的文档结构；DOM HTML模块则在DOM核心上扩展
###### DOM2级在DOM1级的基础上扩展了鼠标和用户界面事件、范围、遍历等细分模块，增加了对CSS的支持
###### DOM3级：引入了DOM加载和DON保存，新增DOM验证
### 标签对的使用
	<script>
	    function sayScript(){
	        alert("<\/script>")     //用转义符号否则会被当作</script>
	    }
	</script>
###### 位置在后，如果在里意味着JS代码都被下载、解析和执行完成才显示页面内容，会导致延迟
### 延迟脚本
	<head>
	    <script type="text/javascript" defer="defer" src="example.js"></script>
	</head>
######包含延迟脚本defer先下载后执行，在遇到后才执行,defer属性只适用于外部脚本
### 异步加载
	<head>
	    <script type="text/javascript" async src="example1.js"></script>
	    <script type="text/javascript" async src="example2.js"></script>
	</head>
###### 异步加载不保证执行顺序
### XHTML用法
###### 在XHTML代码解析执行的时候，<(小于) 会被认为是 <> 标签对的一个，所以用下列写法
	<script><![CDATA[
	    function(){
	        if(a < b){
	        }
	    }
	]]></script>
###### 有些浏览器不支持cdata写法，可以注释掉
	<script>
	//<![CDATA[
	    function(){
	        if(a < b){
	        }
	    }
	//]]>
	</script>
###### 还可以使用HTML实体来完成 < —> <
	<script>
	    function(){
	        if(a &lt; b){
	        }
	    }
	></script>
###### 不过到目前为止,我还没遇到XHTML解析问题出现的bug
### JS区分大小写，而且一些函数名不能与关键字和保留字相同
### typeof 的使用
###### 用于鉴别给定的变量的数据类型
###### JS有5种基本数据类型null、undefinded、Boolean、Number、String
###### 1种复杂类型Object
	function test(){
	    message = 'hi'    //未用var，为全局变量，不推荐这样写
	}
	test()
	alert(message)    //  'hi'
	alert(typeof message)  // "string"
	alert(typeof test)    // "function"
	alert(typeof test())   //undefined  无返回值
### Number数值
###### 正无穷Infinity,负无穷-Infinity
###### 能保存的最大的数值Number.MAX_VALUE—>1.7976931348623157e+308,最小的数值Number.MIN_VALUE—->5e-324
###### NaN–>非数值,not a number,NaN与任何值都不相等,包括本身
###### isNaN()函数判断是不是非数值，在接收参数后尝试把参数转化为数值，不能被转的返回true，否则为false
	isNaN(NaN)  //true
	isNaN("10")  //false-->"10"被转化为10
	isNaN(true)  //false  true-->1,false-->0
	isNaN("String")  //true
	console.log(isNaN(null))  //false   null-->0
	console.log(isNaN(undefined)) //true  undefined-->NaN
###### 数制转化
	num1 = parseInt("123blue")  //123
	num2 = parseInt("100",2)  //  100按二进制解析结果为4
	num2 = parseInt("100",8)  //  100按八进制解析结果为64  
###### 同理有parseFloat()
### Object类型
###### 常用的方法
###### constructor:保存着用于创建对象的函数
###### hasOwnProperty(propertyName):用于检查属性在当前对象中是否存在(而不是实例原型)
###### isPrototypeOf(object):用于检查传入的对象是否是当前对象的原型
###### valueOf():返回对象的字符串、数值或boolean表示与toString()相同 
### 操作符
	var num1 = 20
	var num2 = --num1  //此时num2=19  num1=19
	var num3 = num1-- //此时num3=19 num1=18  num2=19
	var num4 = num1-- + num2  //此时num4=18+19  num1=17
###### 例2
	var s1 = 'Z'
	s1--  //NaN
	var o = {
	    valueOf:function(){
	        return -1
	    }
	}
	o--  //-2
###### 位操作符，全都化成二进制做,都存的是补码
###### 非NOT ~
	var num1 = 25   //二进制 0001001
	var num2 = ~num1  //num2=1110110-->-26
###### 与AND &
	var num1 = 25 & 3    
	分析  00011001
	      00000011
	&     00000001--->结果为1
###### 或OR |
###### 异或 XOR ^ 相同为0不同为1
###### 左移 <<
###### 有符号右移 >>
###### 无符号右移 >>> 高位补0
###### 布尔操作符
###### 逻辑非
	!"blue"  false
	!0       true
	!""      true
	!123     false
	!NaN     true
	!null    true
### 逻辑与 &&
###### 如果第一个操作数是对象，返回第二个操作数
###### 如果第二个操作数是对象，只有在第一个为true的情况下才返回第二个操作数对象
###### 如果两个操作数都是对象则，返回第二个
### 逻辑或 ||
###### 看第一个如果为true就返回true
###### 加减乘除
	Infinity*0 –>NaN
###### 操作数不是number，则后台先调用Number()
	5+”5” –>55
	5-true –>4
	var num1 = 5
	var num2 = 10
	"the number is" + num1 + num2   //the number is 510
	"the number is" + (num1 + num2)  // the number is 15 
### 比较大小问题
	null == undefined  //true
	"23"<3   //false   "23"-->23
	"23"<"3"  //true
	"a" < 3   //false  "a"-->NaN   NaN与任何比较都是false
	NaN == NaN //false
	undefined == 0 //false
	null == 0 //false
### 逗号操作符
	var num = (5, 1, 4, 8, 0)  //num = 0 赋值最后一个
	label加break、continue
	label+break
	var num = 0
	outermost:
	for(var i =0;i<10;i++){
	    for(var j = 0; j<10;j++){
	        if(i==5&&j==5){
	            break coutermost
	        }
	        num++
	    }
	}
	console.log(num)   //55
	label+continue
	var num = 0
	outermost:
	for(var i =0;i<10;i++){
	    for(var j = 0; j<10;j++){
	        if(i==5&&j==5){
	            continue coutermost
	        }
	        num++
	    }
	}
	console.log(num)   //95
### with语句
	var a = 1
	var qs=localtion.search.substring(1)
	var hostname = location.hostname
	var url = localhost.href
###### 全部可以简写为
	var a = 1
	with(localtion){
	    var qs=search.substring(1)
	    var hostname = hostname
	    var url = href
	}
###### a与qs作用于是同级的
### 参数–>arguments
	function test(){
	    console.log(arguments.length)   //参数的个数
	} 
	test()        //0
	test("1")     //1
	test("1","2")   //2
	test({name:"zfl"})  //1
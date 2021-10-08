---
title: javascript高级程序设计4-5
date: 2017-09-13 10:42:30
tags: javascript
categories: javascript
---


### 传递参数
###### 基本数据类型，直接在内存中存放，引用数据类型，变量名是指针
	function setName(obj){
	    obj.name = "zfl"
	    obj = new Object()    //obj指针又指向了另外一块内存
	    obj.name = "123"
	}
	let person = new Object()
	setName(person)
	console.log(person.name)
### 检测类型
	null是object类型 typeof null –>object
###### 检测类跟对象时用 instanceof
	person instanceof Object –>person是object吗？
### 作用域
	let color = "blue"
	function change(){
	    if(color == "blue"){
	        color = "red"
	    }
	}
	change()
	console.log(color)
###### 函数内可以访问函数外的作用域，函数外的不能访问函数内的，var跟let一样
###### with作用域块跟with平级
###### var没有块级作用域
	if(true){
	    var color="blue"
	}
	console.log(color)    //--->blue
###### let存在块级作用域
	if(true){
	    let color="blue"
	}
	console.log(color)    //--->undefined
### 垃圾收集
#### 标记清除
###### 当变量进入环境时，就将这个变量标记为进入环境。从逻辑上讲，永远不能释放进入幻境的变量所占的内存，因为只要执行流进入相应的环境，就可能会用到它们。而当变量离开环境时，则将其标记为离开环境
#### 引用计数
###### 跟踪记录每个值被引用的次数。当引用次数为0时，可将内存收回。
###### 循环引用时，引用计数会崩溃，一般不用
### 基本变量类型在内存中占据固定大小的空间，被保存在栈内存中
### 引用类型的值是对象，保存在堆内存中
### 函数的局部变量可以访问其父作用域的变量
### 访问对象属性的方法
###### 1.点表示法 person.name
###### 2.方括号表示法 person[“name”],属性名有空格时可以这样访问 person[“first name”]
### 数组
#### 创建
	let colors = new Array(3) //创建length为3的数组
	let values = [1,2,,] //会创建一个包含3或4项的数组
	let values = [,,,] //会创建一个包含3或4项的数组
#### 方法
###### Array.isArray()判断是不是数组
###### valueOf()、toString()
###### valueOf()返回的还是数组、toString()返回的是字符串
###### join()
###### 使用不同的分隔符来构建字符串
	var value = ["1","2","3"]
	value.join("|")
	console.log(value.join("|"))  //  1|2|3
###### 栈方法
###### push()、pop() 数组尾
###### shift()、unshift() 数组头
###### reverse()反转排序
###### sort()
###### 按照升序排序，但是排的是字符串
	var values = [0,1,5,10,15]
	var aftersort = values.sort()
	console.log(aftersort)               //0 1 10 15 5     
	var aftercomp = values.sort(function (value1, value2){
	    if(value1 > value2){
	        return 1
	    }else if(value1 < value2){
	        return -1
	    }else{
	        return 0
	    }
	})
	or
	var aftercomp = values.sort(function (value1, value2){
	    return value2- value1
	})
	console.log(aftercomp)  // 0 1 5 10 15
###### 本来想去找找js原生库函数的源代码，只在ecmascript.js里找到了定义,实际上在v8编辑器里，在
<a href="https://developer.mozilla.org/en-US/search?q=sort">https://developer.mozilla.org/en-US/search?q=sort</a>里，给出了详细的用法，这里就不说了。有时间看看v8源码
###### concat()连接，返回连接后的数组，不影响原数组
###### slice()接受一个或者两个参数，即要返回项的起始位置和结束位置(不包括结束位)
	var values = [0,1,2,3]
	var value1 = values.slice(1)
	var value2 = values.slice(1,3)
	console.log(value1)  // 1 2 3
	console.log(value2)  // 1 2
###### splice()
###### 两个参数:要删除的第一项和要删除的项数
###### 三个参数:起始位置、要删除的项数、要插入的项数
	var values = [0,1,2,3]
	values.splice(0,1)
	console.log(values)     // [1, 2, 3]
	values.splice(0,0,"c1","c2")
	console.log(values)     // ["c1", "c2", 1, 2, 3]
	values.splice(0,1,"s1","s2")
	console.log(values)     // ["c1", "c2", 1, 2, 3]
###### 位置方法indexOf() lastIndexOf()
	var person = {name:"zfl"}
	var people = [{name:"zfl"}]
	var morepeople = [people]
	console.log(morepeople)
	console.log(people.indexOf(person))   //-1 //查找项必须严格相等  ===  没有返回-1
	console.log(morepeople.indexOf(people)) // 0
###### 迭代方法
###### every():对于每一项运行给定函数，若每一项都返回true，则返回true
###### filter():返回true项给组成的数组
###### forEach(): 每项运行给定函数,无返回值
###### map(): 返回每次调用函数返回的结果组成的数组
###### some(): 若某项返回true，则返回true
	var value = [1,2,3,4,5,6]
	var everyResult = value.every(function (item, index, array){  //值 项数 数组
	    return item>2
	}) 
	console.log(everyResult)   //false
	var filterResult = value.filter(function (item, index, array){  //值 项数 数组
	    return item>2
	})
	console.log(filterResult)  //[3, 4, 5, 6]
	var mapResult = value.map(function (item, index, array){  //值 项数 数组
	    return item = item +2
	})
	console.log(mapResult)   //[3, 4, 5, 6]
	var forEachResult = value.forEach(function (item, index, array){  //值 项数 数组
	    return item = item + 2
	})
	console.log(forEachResult)  //undefined
### Date()
###### Date.parse()
###### 接受一个表示日期的字符串参数，然后尝试根据这个字符串返回相应的日期毫秒数
###### 时间戳转日期
###### 详情可见如下链接，将此方法携程管道函数来执行
<a href="https://gitee.com/UPCmvc/front_student/blob/master/src/config/date.js">链接</a>

### 关于Function的几个要点
###### 函数实际上是个对象
###### 函数名仅仅是指向对象的指针
###### 用function name(){}定义的会存在变量提升，而let name = function(){}则不会
###### 没有重载、没有重载、没有重载
###### 函数名本身就是变量可以作为参数传递
###### 函数内部的固有属性arguments和this，arguments除了保存函数的参数，有一个callee的属性，这个属性是个指针指向拥有这个arguments对象的函数,还有一个caller保存调用这个函数的引用
###### 递归
	function f(){
	    if(num<=1){
	        return 1
	    }else{
	        return num*arguments.callee(num-1)   //为了降低耦合性
	    }
	}
###### caller
	function outer(){
	    inner()
	}
	function inner(){
	    console.log(arguments.callee.caller)
	}
	outer()   //ƒ outer(){inner()}
###### bind()用于绑定作用域，this值会被绑定到bind()函数的值
### 基本包装类型
###### Number
	toFixed(2) 保留两位小数
	toExponential(1) 10–>1.0e1返回指数形式并保留1位小数
	toPrecision() 返回某个数值最合适的格式
###### string
	trim() 去掉前后空格
	toUpperCase() toLocaleLowerCase() 返回大小写
	replace() 代替，可以匹配正则
###### 例一
	var date = "2017/09/27"
	date = date.replace(/\//g,"-")  
	console.log(date)   //2017-09-27
###### 例二
    function rep(text){
    return text.replace(/[<>"]/g, function (match, pos, originalText){
        switch(match){
            case "<":
                return "小于"
            case ">":
                return "大于"
            case "\"":
                return "引号"
        }
    })
	}
	var text = "<>\""
	text = rep(text)
	console.log(text)   //小于大于引号
###### match() 返回数组
	var date = "2017/09/27"
	var matches = date.match(/..\//g)  
	console.log(matches) //(2) ["17/", "09/"]
###### 如果要用RegExp对象的exec()要将字符串作为参数
###### search()找到某元素的位置可以为正则
###### split()分割字符串，一个参数就按参数分割，两个参数，第二个参数是个数字，返回第二个参数长度的数组
### 单体内置对象
###### encodeURI()用于整个URI，encodeURIComponent对部分URI
###### decodeURI(),decodeURIComponent分别对上述两个进行解码
###### eval() 只知道会改变作用域链就ok
### Math
###### Math.ceil() 向上四舍五入
###### Math.floor() 向下四舍五入
###### Math.round() 标准四舍五入
###### Math.random() 产生一个0——1的随机数
---
title: $interval和$timeout服务实现定时器
date: 2017-01-05 22:31:10
tags: angularjs
categories: angularjs
---

### AngularJS的$interval()和$timeout()服务，是你能把代码执行过程延迟一定的时间。这些服务与js中的setInterval和setTimeout的功能互交。但在angular的框架内进行

### 语法
	
	$interval(callback ,delay , [count] , [invokeApply]);
	$timeout(callback ,delay , [invokeApply]);

#### callback:回调函数
#### delay: 毫秒数
#### count: 时间间隔的重复次数
#### invokeApply:boolean 默认为true
#### true导致函数只在angular事件循环的$apply()块中执行 

	var myInterval = $interval(function(){$scope.seconds++;} , 1000, 10, true);
	...
	$interval.cancel(myInterval);

### 如果使用$interval或$timeout建立超时时间或时间间隔，则必须在scope或element指令被销毁时显式的使用cancel()销毁它们。

	$scope.$on('$detory' ,function (){
		$scope.cancel(myInterval);
	})
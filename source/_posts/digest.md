---
title: digest
date: 2016-12-25 22:02:20
tags: angularjs
categories: angularjs
---

### $scope.$digest()

###### 这里digest不是消化的意思。。
###### 这个当一个作用域初始化时，创建阶段产生。启动应用程序会创建根作用域。当遇到ng-controller或ng-repeat指令时，则链接创建子作用域的模板。

###### 在创建阶段，创建了一个digest循环与浏览器事件循环互动。digest循环负责把对模型的更改更新到DOM元素，并且执行任何已注册的监视函数。虽然你不需要手动执行digest循环，但你可以通过在作用域执行$digest()方法这样做。例如

	$scope.$digest()
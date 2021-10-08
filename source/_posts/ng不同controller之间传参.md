---
title: ng不同controller之间传参
date: 2017-04-10 20:38:13
tags: angularjs
categories: angularjs
---

#### 父作用域与子作用域
	<div ng-controller="parent">
	  <p>data in parent controller : {{data.name}}</p>
	  <div ng-controller="child">
	    <input type="text" ng-model="data.name">
	  </div>
	</div>

	var myApp = angular.module('myApp', []);
		myApp.controller('parent', function  ($scope) {
			 $scope.data = {
        		name: '123'
    		}
		}); 
		myApp.controller('child', function  ($scope) {
			$scope.name = '';
		}); 

#### 平行作用域

#### 方法一、使用$rootScope直接绑定，这个就比较简单了，之前我的一篇博文也讲过
#### 方法二、注入服务
	<div ng-controller="first">
	  	<input type="text" ng-model="name">	 
	  	value:{{name}} 
	  	<button ng-click="set()">set</button>
	</div>
	<div ng-controller="second">
	    value:{{name}}
	</div>

	var myApp = angular.module('myApp', []);
		myApp.factory('myService', function() {
			var savedData = '';
			function set(data) {
			   savedData = data;
			}
			function get() {
			 	return savedData;
			}
			return {
			  set: set,
			  get: get
			}
		});
		myApp.controller('first', function ($scope, myService, $rootScope){
			$scope.name = '';
			$scope.set = function (){
				myService.set($scope.name);
				console.log($scope.name);
				$rootScope.$broadcast('send');
			}
			
		});
		myApp.controller('second', function ($scope, myService, $rootScope){
			$rootScope.$on('send', function (){
				$scope.name = myService.get();	
			});		
		});

#### 方法三、基于localStorage
	<div ng-controller="first">
	  	
	</div>
	<div ng-controller="second">
	    value:{{value}}
	</div>
	
	var myApp = angular.module('myApp', []);
		myApp.controller('first', function ($scope) {
		  localStorage.setItem("key","value");
		});
		myApp.controller('second', function ($scope){
			$scope.value = localStorage.getItem('key');
		})
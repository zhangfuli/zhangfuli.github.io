---
title: angular子页面
date: 2017-04-30 22:33:17
tags: angularjs
categories: angularjs
---


### 很多时候遇到从主页到子页面的情况，子页面只是信息不一样，而格式是完全一样的

#### 方法一：通过a标签传递

##### 主页面是可以拿到一些子页面的信息的，这里比如说拿到了id
	<a href="./discuss.html#foodid={{this.food.id}}">详情&评论</a>

##### 在子页面中就可以对这个url进行字符串的切割或者正则匹配，就完全可以拿到id，可以加载进一步的操作

##### 当然这种方法是非常笨的一种方法

#### 方法二：XXXX:/id

##### 这是通过路由配置的，比如用$routeProvide配置路由
	 .when('/:id', {
        templateUrl: 'XXXX',
        controller: 'XXXX'

      })

##### 同样，调用的时候
	<a href="/#/{{food.id}}" class="noneunderline">进去看看</a>

##### 在子页面中调用的时候就可以
	$routeParams.id
##### 直接拿到id

##### 通常都是用这种方法
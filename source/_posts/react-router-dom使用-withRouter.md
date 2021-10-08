---
title: react-router-dom使用@withRouter
date: 2018-12-20 10:08:30
tags: react
categories: react
---

### 前言
###### 在React路由中, 有这样一种写法, @withRouter, 效果就是把路由相关的方法通过props传给它包裹的组件的props上;
###### 跟withRouter(component)是一个效果;

### 方法
###### 安装 babel-plugin-transform-decorators-legacy
	npm install babel-plugin-transform-decorators-legacy --save-dev
###### 在```\node_modules\babel-preset-react-app\create.js```中加入装饰器支持
	require.resolve('babel-plugin-transform-decorators-legacy')

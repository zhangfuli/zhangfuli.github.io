---
title: transform
date: 2018-02-15 10:42:30
tags: HTML CSS
categories: HTML CSS
---

#### 前言
###### 在CSS3中transform主要包括以下几种：旋转rotate、扭曲skew、缩放scale和移动translate以及矩阵变形matrix,不改变原先的文档流
###### 原文: 
<a href="https://www.w3cplus.com/content/css3-transform">https://www.w3cplus.com/content/css3-transform</a>


#### 1.改变元素基点transform-origin
	transform-origin:100% 50% 左右、上下
	transform-origin:right;
<img src="http://p4j7qpj9e.bkt.clouddn.com/transform-2.png">

	transform-origin:0% 0% 左右、上下
	transform-origin:top left;
<img src="http://p4j7qpj9e.bkt.clouddn.com/transform-1.png">

	transform-origin:25% 75% 左右、上下
<img src="http://p4j7qpj9e.bkt.clouddn.com/transform-3.png">

#### 2.旋转rotate
	transform:rotate(30deg) 以中心点为基点顺时针旋转30°
<script async src="//jsfiddle.net/Nb7rG/5/embed/js,html,css,result/dark/"></script>
#### 3.平移
	transform: translate(100px, 100px); 以中心点为右移100px,下移100px
<script async src="//jsfiddle.net/Nb7rG/6/embed/js,html,css,result/dark/"></script>
#### 4.缩放
	transform: scale(0.5, 2); 缩放（x, y）左右、上下缩放倍数
<script async src="//jsfiddle.net/Nb7rG/7/embed/js,html,css,result/dark/"></script>
#### 5.扭曲
	transform: skew(30deg,10deg); 扭曲（x, y）扭曲的角度 正表示顺时针
<img src="http://p4j7qpj9e.bkt.clouddn.com/transform-4.png">
<script async src="//jsfiddle.net/Nb7rG/8/embed/js,html,css,result/dark/"></script>

#### 6.matrix(, , , , , )
###### 以一个含六值的(a,b,c,d,e,f)变换矩阵的形式指定一个2D变换，相当于直接应用一个[a b c d e f]变换矩阵。

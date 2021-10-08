---
title: mongodb_find
date: 2016-12-12 22:46:52
tags: mongodb
categories: mongodb
---
### 前言
#### 自己也敲了几天express-mongodb了，觉得应该做一下总结

#### 最主要的是从mongodb数据库里面find数据，至于插入数据可以有图形化的界面操作，这里不详细介绍了

### query对象

#### mongodb定义了请求返回结果的query对象运算符，这个可以从晚上查文档
	words.find({first:{$in : ['a','b','c']}},function(err , cursor){
			if(err) throw err;
			displayWords("Words starting with a,b,c :" ,cursor);
	});

#### 其中，words是一个collection对象，first是json数据里的某条数据。
#### 作用是查找first内含有a、b、c的数据

### options对象

#### 同query对象一样，mongodb还包括options对象，最长用的就是limit、sort、fields等
	words.find({'last':'w'},{limit : 4},{ sort : [['word' , 1]]} ,function(err ,cursor){

    });
#### 其中，last、word是json数组的某条数据，limit限制总长数为5，sort 里面1为升序 、-1为降序

#### 当然findOne的用法跟这个一样
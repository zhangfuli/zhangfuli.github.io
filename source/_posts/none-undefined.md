---
title: none&&undefined
date: 2017-04-24 21:50:56
tags: javascript
categories: javascript
---

### 总结一下null和undefined的区别

#### JavaScript两个都表示"无"。

#### 相同点

##### 将一个变量赋值为undefined或null，几乎没区别
	var a = null;
	var a = undefined;
##### undefined和null在if语句中，都会被自动转为false，相等运算符甚至直接报告两者相等

#### 区别

##### null表示"没有对象"，即该处不应该有值
##### undefined表示"缺少值"，就是此处应该有一个值，但是还没有定义。

#### null的特殊用法

##### null被设计成可以自动转为0

	null+1

#### 结果为1

	Number(null)

#### 结果为0
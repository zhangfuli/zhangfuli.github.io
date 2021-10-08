---
title: mongodb文档
date: 2016-11-30 22:06:10
tags: mongodb
categories: mongodb
---

### 插入文档

#### 是不是觉得用命令插入很麻烦。我也有同感，介绍一个小技巧吧
#### 创建一个yourfile.js在里面写入你要插入的文档
#### 例如
	document=({
	title: 'test', 
    description: 'This is a test',
	});
#### 然后按照正常步骤打开mongo shell后
	load("yourfile.js")   
#### 注意这里路径要对
#### 这时会返回一个true，ok你就可以按照正常步插入文档了
	db.col.insert(document)
#### 显示所有文档
	db.col.find()

### 更改文档
#### 命令1
	db.col.update({'title':'test'},{'title':'MongoDB'})
#### 这样会把整个改成title:MongoDB，没有了description

#### 命令2
	db.col.update({'title':'test'},{$set:{'title':'MongoDB'}})
#### 这样只会改title，description还在

### 删除文档
#### 删除一条
	db.col.remove({'title':'test'})
#### 删除所有
	db.col.remove({})

### 查询文档
	db.col.find()
#### 当然还有易读方式
	db.col.find().pretty()


---
title: 2018小学期--hadoop
date: 2018-08-13 09:36:04
tags: 大数据
categories: 大数据
---

### spark 由scala完成

#### 1、scala 三种方式编写
###### (1) 交互式  cmd   scala->              :paste
###### (2) 脚本式  hello.scala    scala hello.scala
###### (3) ide scalac hello.scala(含有main函数)-> scala hello

#### 2、定义变量
###### val a:Int = 3    //常量     变量名:数据类型  可以不加   val a = 3
###### var a:Int = 12   //变量     var a = 12

#### 3、其他数据类型
######Unit、Null、AnyRef、Any

	def hello():Unit = { //无返回值
		
	}


### spark
###### 1、创建RDD  内存中分布式数据集    
###### 2、集群模式启动     spark-shell --master spark://192.168.1990.137:7077
	var rdd = sc.textFile("路径")
	rdd.collecct.foreach(println)
	rdd.map(line => line.split(" "))  //转换


###### 3、从内存中构造集合
	var rdd = sc.parallelize(List(1,2,3,4,5,6,7,8,9,10))
###### 4、判断是否含有某单词的行数
	rdd2 = rdd.fliter(line => line.contains("hello"))
	rdd2.count    //遇到count才真正计算
###### 5、val conf = new SparkConf().setAppName("Word Count")
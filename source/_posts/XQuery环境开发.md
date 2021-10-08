---
title: XQuery环境开发
date: 2019-03-29 12:22:35
tags: 环境
categories: 环境
---

### 前言

###### Xquery的环境配置

### 示例

#### jar包
###### idea新建maven项目，将jar包下载下来并添加入Libraries
<a href="https://sourceforge.net/projects/saxon/">https://sourceforge.net/projects/saxon/</a> 

#### xqy
###### 在项目根目录新建book.xqy文件
	for $x in doc("books.xml")/books/book
	where $x/price>30
	return $x/title

#### xml
###### 在项目根目录新建book.xml文件
	<books>

	   <book category="JAVA">
	      <title lang="en">15天搞定Java</title>
	      <author>Maxsu</author>
	      <year>2015</year>
	      <price>30.00</price>
	   </book>

	   <book category="DOTNET">
	      <title lang="en">15天搞定.Net</title>
	      <author>Susen</author>
	      <year>2018</year>
	      <price>40.50</price>
	   </book>

	   <book category="XML">
	      <title lang="en">3天搞定XQuery</title>
	      <author>Yizhi</author>
	      <author>Maxsu</author> 
	      <year>2016</year>
	      <price>50.00</price>
	   </book>

	   <book category="XML">
	      <title lang="en">24小时搞定XPath</title>
	      <author>Jazz Bee</author>
	      <year>2019</year>
	      <price>16.50</price>
	   </book>

	</books>

#### XQueryTester.java
###### 新建, XQueryTester.java并导入相应的包
	public class XQueryTester {
	   public static void main(String[] args){
	      try {
	         execute();
	      }

	      catch (FileNotFoundException e) {
	         e.printStackTrace();
	      }

	      catch (XQException e) {
	         e.printStackTrace();
	      }
	   }

	   private static void execute() throws FileNotFoundException, XQException{
	      InputStream inputStream = new FileInputStream(new File("books.xqy"));
	      XQDataSource ds = new SaxonXQDataSource();
	      XQConnection conn = ds.getConnection();
	      XQPreparedExpression exp = conn.prepareExpression(inputStream);
	      XQResultSequence result = exp.executeQuery();

	      while (result.next()) {
	         System.out.println(result.getItemAsString(null));
	      }
	   }    
	}

###### 点击运行即可






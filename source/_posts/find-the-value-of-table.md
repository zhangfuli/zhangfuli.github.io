---
title: find the value of table
date: 2016-10-05 12:17:37
tags: javascript
categories: javascript
---


#### 如果想要找一个table中的某个td值
	var t=document.getElementById('tbody');
	var rows=t.rows;
	for(var i=0;i<rows.length;i++){
		var cell=rows[i].cells[1];
			if(cell.innerHTML<60){
				rows[i].bgColor='#ff3333';
			}
			else{
				rows[i].bgColor='green';
			}
	}

#### 这是js代码,其中"tbody"是tbody的id

#### 详细代码 
<a href="https://github.com/zhangfuli/changetablebgcolor/blob/master/index.html">click here</a>

---
title: vue多json弹窗修改相同属性解决方案
date: 2018-01-11 10:42:30
tags: vue
categories: vue
---


#### 描述
###### 项目中有时候会遇到这样一种情况,v-for渲染出一个列表,想加入弹出框修改某属性
#### 解决
###### 下面的代码是一种解决方案
<a href="https://gitee.com/UPCmvc/front_student/blob/master/src/components/ScienceCheck.vue">点击</a>

###### 通过动态绑定属性进行点击的列表给弹出框传值,然后再返回,如上述代码中29行,获取到data-id然后传值给checkId(该值位于弹出框),然后再修改
#### 问题2
###### 万一要是不同的json数组,相同的属性要修改怎么办呢？
###### html
	<div class="hello">
	    <div v-for="(list1,index) in lists1">
	      <span>{{list1.sex}}</span>
	      <button type="button" @click="change($event, list1.id, lists1, 'sex')">点击</button>
	    </div>
	    <br>
	    <div v-for="list2 in lists2">
	      <span>{{list2.wrap.sex}}</span>
	      <button type="button" @click="change($event, list2.id, lists2, 'wrap.sex')">点击</button>
	    </div>
	    <br>
	    <div v-for="list3 in lists3">
	      <span>{{list3.sex}}</span>
	      <button type="button" @click="change($event, list3.id, lists3, 'sex')">点击</button>
	    </div>

	    <input type="text" v-model="text">
	</div>
###### js
	import objectPath from 'object-path'
	export default {
	  name: 'HelloWorld',
	  data () {
	    return {
	      msg: 'Welcome to Your Vue.js App',
	      text:'',
	      path:'',
	      lists1:[{
	        "id":1,
	        "sex":1
	      },{
	        "id":2,
	        "sex":1
	      }],
	      lists2:[{
	        "id":1,
	        "wrap":{
	          "sex":1
	        }
	      },{
	        "id":2,
	        "wrap":{
	          "sex":1
	        }
	      }],
	      lists3:[{
	        "id":1,
	        "sex":1
	      },{
	        "id":2,
	        "sex":1
	      }],
	    }
	  },
	  methods:{
	    change: function(event, id, array, attr){
	      objectPath.set(array[id-1], attr, this.text)
	    }
	  }
	}
###### 主要传入的参数有json数组和路径,然后用objectPath.set进行修改
	npm install object-path --save
<a href="https://www.npmjs.com/package/object-path">objectPath具体详见</a>

###### 其实vue提供了set方法跟这个效果也是一样的Vue.set()
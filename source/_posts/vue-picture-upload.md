---
title: vue-picture-upload
date: 2017-08-26 10:42:30
tags: vue
categories: vue
---

### 前言
###### base64编码上传图片的vue组件
###### 通过 npm install picture-upload –save 安装到相应的项目下
### demo
###### 需要用到该组件时(下面是一个demo)
	<template>
	<div class="demo">
	    <PictureUpload v-on:imgInfo = 'getInfo'></PictureUpload>
	</div>
	</template>

	<script>
	  import PictureUpload from 'picture-upload'
	  import Css from 'picture-upload/dist/picture-upload.min.css'
	  export default {
	  name: 'demo',
	  data () {
	    return {

	    }
	  },
	  components:{
	    PictureUpload
	  },
	  methods:{
	    getInfo(imgInfo){
	      console.log(imgInfo)
	    }
	  }
	}
	</script>

	<!-- Add "scoped" attribute to limit CSS to this component only -->
	<style scoped>

	</style>
### demo讲解
###### getInfo方法的参数imginfo是一个json数组
###### 各参数如下
###### ‘format’: format, //转化后的格式
###### ‘base’ : base, //转化后的base码
###### ‘fullInfo’: fullInfo //全部信息
###### 必要时可以在控制台打印出来看一下
<a href="https://github.com/zhangfuli/picture-upload">项目地址</a>
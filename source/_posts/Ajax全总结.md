---
title: Ajax全总结
date: 2017-09-06 10:42:30
tags: javascript
categories: javascript
---

### 前言
###### 这篇是在看过JS高级程序设计以及自己以前的一些项目所做的总结
	let xhr = new XMLHttpRequest()
### xhr用法
###### open() 有三个参数
###### 1.请求类型 2.URL 3.是否为异步发送请求
	xhr.open(‘get’, URL, false)
###### 注意：它不会发送请求当调用send()时才会发送请求
	send(data) 
###### data:要发送的数据
###### 不需要发送数据时必须传入null
###### 响应的数据
###### xhr.responseText 返回主体
###### xhr.responseXML
###### xhr.status 响应的HTTP状态
###### xhr.readyState
###### 0： 未初始化 未调用open()
###### 1:  启动 已经调用open() 未调用send()
###### 2:  发送 已经调用send() 未收到响应
###### 3： 接受 接受部分响应数据   --用于检测http流
###### 4： 完成
	xhr.onreadystatechange = function(){
	    if(xhr.readStatus == 4){
	        if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
	            console.log("success")
	        }else{
	            console.log("error")
	        }
	    }
	}

	xhr.open()
	xhr.send(null)
	xhr.abort() 取消异步请求
	xhr.setRequestHeader(‘myHeader’, ‘myValue’) 设置头信息
	xhr.getAllResponseHeaders() 得到头部信息
### 超时设定
	xhr.timeout = 1000 (毫秒)
	xhr.ontimeout = function(){}
###### overrideMimeType() 重写XHR响应的MIME类型 必须在send()之前
###### xhr.overrideMimeType("text/xml")  强迫xhr对象将响应当作XML处理
### get请求
###### 1.&分隔符
###### 2.查询字符串中每个参数的名和值都必须用encodeURIComponent()进行编码
###### 在URL后能看见字符串参数
	function addURLParam(url, name, value){
	    url += (url.indexOf("?") == -1 ? "?" : "&");
	    url += encodeURIComponent(name) + "=" + encodeURIComponent(value);
	    return url;
	}

	let url = "example.php"
	url = addURLParam(url, "name" , "zfl")
	xhr.open('get', url, false)
###### 案例
	let URL = "http://localhost:8080/getWeather"
	let xhr = new XMLHttpRequest();

	// URL = addURLParam(URL, "city", data)
	xhr.open("get", URL, false)
	xhr.send(null)
	if((xhr.status>=200 && xhr.status <300) || xhr.status == 304){
	    console.log(xhr.responseText);
	}
	let resp = JSON.parse(xhr.responseText)
	console.log(resp.city)
	document.querySelector('.city').innerHTML = resp.city
	document.querySelector('.weather').innerHTML = resp.weather
### post请求
###### 不限传输长度和格式(base64上传图片就必须用post,get传输的太短)
###### post和提交web表单不一样
###### 将表单数据序列化传到服务器
	let form = document.getElementById("myform")
	xhr.send(serialize(form))
###### 或者
	let form = new FormData(document.getElementById("myform"))
	xhr.send(form)
###### 进度事件
###### loadstart: 在接收第一个字节时触发
###### progress：周期性的触发
	xhr.onprogress = function(event){
	    if(event.lengthComputable){  //进度信息是否可用
	        event.position  //已经接受的字节数
	        event.totalSize  //根据Content-Length响应头部去顶的预期字节数
	    }
	}
### 跨域
###### CORES
###### 本质上是使用自定义的HTTP头部让浏览器与服务器沟通，从而决定是否响应成功
	Origin: http://123.com   //浏览器
	Access-Control-Allow-Origin: http://123.com
###### 通过将withCredentials 设置为true可以指定某请求发送凭据
	Access-Control-Allow-Credentials: true  //浏览器
###### 以下情况在服务器端设定好了
	Access-Control-Allow-Origin: *
	Access-Control-Allow-Credentials : true
###### vue实现跨域请求–以vue-cli为例
###### 在vue-cli生成的时候会给你引入vueresource如果没有的话可以参照项目自己引入进去
###### 只需在router/index.js里面加入
	Vue.http.options.emulateJSON = true
###### 这样在每次进行get、post的时候就不用都写入{emulateJSON: true}
###### 善用 let self = this
###### 不会出现在自己未知的情况下this指针作用域发生变化而导致的bug
###### 两个例子是在17年暑期实践的时候给队友写的这边就直接拿来了
	initGet(){
	  let self = this
	    self.$http.get(API.testGet).then((response)=> {
	        console.log(response)
	    },()=>{
	        console.log("error")
	    })
	},
	initPost(){
	  let self = this
	  let data = {
	      a:'test'
	  }
	  self.$http.post(API.testPost,data).then((response)=> {
	    console.log()
	  },()=>{
	    console.log("error")
	  })
	}
### ng1的跨域问题
###### 用yeoman生成的angular的脚手架,在16年的暑假黑客马拉松写的易二手轻应用
###### 在app.js里的config里加入
	$httpProvider.defaults.withCredentials = true;
###### 其他就正常调用get、post方法即可
###### ng2的跨域到目前为止，我只是粗略的走过ng2的教程并没有用ng2写过项目，但是跨域的重点在于前后端的http头匹配问题，也就是前后端设定相同的http头，其他的就没什么大问题了
### 其他跨域方法
###### 图像ping
######利用的核心思想是一个网页可以从任何网页加载图像而不需要担心跨域的问题图像ping是与服务器进行简单的、单向的苦于通信，浏览器得不到任何数据，而且只能发送get请求
	let img = new Image()
	img.onload = img.onerror = function(){}
	img.src = 'http://www.example.com?name = zfl'   //设定路径的那一刻发送get请求
### JSONP跨域
###### 通过script标签
	function showData (result) {
	    var data = JSON.stringify(result); //json对象转成字符串
	}
	$("#btn").click(function () {
	    $("head").append("<script src='http://www.expample.com?callback=showData'><\/script>");   
	})
###### 在服务器端
	List<Student> studentList = getStudentList();
	JSONArray jsonArray = JSONArray.fromObject(studentList);
	String result = jsonArray.toString();
	//前端传过来的回调函数名称
	String callback = request.getParameter("callback");   
	//用回调函数名称包裹返回数据，这样，返回数据就作为回调函数的参数传回去了
	result = callback + "(" + result + ")";
	response.getWriter().write(result)
###### 特别注意：JSONP只能是get方法
### Comet
###### 服务器向页面推送数据的技术
###### 1.长轮询：浏览器定时发送请求，是否有数据
###### 2.短轮询：浏览器发送一次请求，服务器保持请求，直到有数据发送
###### 3.HTTP流：浏览器发送一次请求，服务器保持请求，并周期性的发送一个数据
	function createStreamingClient(url, progress, finished){
	    let xhr = new XMLHttpRequest(),
	        received = 0;
	    xhr.open('get', url, true);
	    xhr.onreadystatechange = function(){
	        let result;
	        if(xhr.readyState == 3){
	            reault = xhr.reaponseText.substring(received);
	            received += result.length;

	            progress(result)
	        }else if(xhr.readyState == 4){
	            finished(xhr.responseText)
	        }
	    }
	}
	let client = createStreamingClient("example.com",function(data){
	        alert("received:" + data);
	    },function(data){
	        alert("Done!");
	    })
### 服务器发送事件
###### 1.SSE 适合长轮询和http流
	let source = new EventSource(“example.com”) 断开始: source.close()
##### 必须为同源，有三个事件open、message、error
	source.onmessage = function(event){
	    console.log(event.data)  //返回的数据在event.data里
	}
### 2.事件流
###### 响应的MIME类型为”text/event-stream” 结果在event.data里
	data: foo    //"foo"

	data: bar    //"bar"

	data: foo
	data: bar     //"foo/nbar"
### websockets—全双工、双向通信
###### 它不使用http://、https://协议，有自己的ws://、wss://协议
	let socket = new WebSocket("ws://example.com")
###### 同源策略不适用所以可以传入任何站点链接,只要服务器端认可,实例化对象后马上尝试建立链接
	readyState属性
	WebSocket.OPENING(0)  :正在建立连接
	WebSocket.OPEN(1) : 已经建立连接
	WebSocket.CLOSING(2) : 正在关闭连接
	WebSocket.CLOSE(3) :  已经关闭连接
###### 其他事件跟xhr一样
### web安全
#### sql注入原理
###### 在提交表单时，把sql语句提交上去
###### 1.永远不要信任用户的输入，要对用户的输入进行校验，可以通过正则表达式，或限制长度，对单引号和双”-“进行转换等。
###### 2.永远不要使用动态拼装SQL，可以使用参数化的SQL或者直接使用存储过程进行数据查询存取。
###### 3.永远不要使用管理员权限的数据库连接，为每个应用使用单独的权限有限的数据库连接。
###### 4.不要把机密信息明文存放，请加密或者hash掉密码和敏感的信息
###### xxs 跨站脚本攻击(Cross Site Scripting)，恶意攻击者往Web页面里插入恶意html代码，当用户浏览该页之时，嵌入其中Web里面的 (html代码/javascript代码) 会被执行，从而达到恶意攻击用户的特殊目的。
###### 最终目的是为了获取用户信息，网站存在xxs，csrf就无从谈起
防范
###### 首先代码里对用户输入的地方和变量都需要仔细检查长度和对”<”,”>”,”;”,”’”等字符做过滤；
###### 其次任何内容写到页面之前都必须加以encode，避免不小心把html tag弄出来。这一个层面做好，至少可以堵住超过一半的XSS 攻击。
###### 避免直接在cookie 中泄露用户隐私，例如email、密码等等。
###### 通过使cookie 和系统ip 绑定来降低cookie 泄露后的危险。这样攻击者得到的cookie 没有实际价值，不可能拿来重放。
###### 如果网站不需要再浏览器端对cookie 进行操作，可以在Set-Cookie 末尾加上HttpOnly 来防止javascript 代码直接获取cookie 。
###### 尽量采用POST 而非GET 提交表单
### CSRF 伪造用户身份去干事情
###### 检查referer, X-Requested-With, Orign头
###### 使用POST代替GET
###### 添加校验Token至表单中
###### 添加验证码或其他人机验证手段，如 Google 的 recaptcha
###### 把Token放到自定义的HTTP Header, Cookie-to-Header Token
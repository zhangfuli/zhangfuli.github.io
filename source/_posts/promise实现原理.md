---
title: promise实现原理
date: 2018-02-27 10:42:30
tags: javascript
categories: javascript
---


###### 若没有链式then,then函数就不用return new Promise递归
	function Promise(fn) {
	    var state = "pending"
	    var value = null,
	        callbacks = [];  //callbacks为数组，因为可能同时有很多个回调

	    this.then = function (onFulfilled, onRejected) {
	        return new Promise(function(resolve, reject){
	            handle({
	                onFulfilled: onFulfilled || null,
	                resolve: resolve,
	                onRejected: onRejected || null,
	                reject: reject,
	            })
	        })
	    };
	    function handle(callback){
	        if(state === "pending"){
	            callbacks.push(callback);
	            return;
	        }
	        let cb = state === 'fulfilled'? callback.onFulfilled: callback.onRejected,
	        ret;
	        if(cb === null){
	            cb = state == 'fulfilled'? callback.resolve: callback.reject;
	            cb(value)
	            return;
	        }
	        try{
	            ret = cb(value);
	            callback.resolve(ret);
	        }catch(e){
	            callback.reject(e)
	        }

	    }
	    function resolve(newValue) {
	        if(newValue && (typeof newValue === 'object' || typeof newValue === 'function')){
	            let then = newValue.then
	            if(typeof then === 'function'){
	                then.call(newValue, resolve, reject);
	                return;
	            }
	        }
	        value = newValue
	        state = "fulfilled"
	        execute()    
	    }
	    function reject(reason){
	        state = 'rejected'
	        value = reason
	        execute()
	    }
	    //用于操作resolve和reject
	    function execute(){
	        setTimeout(function(){
	            callbacks.forEach(function (callback) {
	                handle(callback)
	            });
	        },0)  
	    }
	    fn(resolve, reject);
	}

	function getUserId() {
	    return new Promise(function (resolve) {
	        resolve("success")
	        console.log('success')
	        // reject("reason")
	    })
	}
	function getUserJobById(id){
	    return new Promise(function (resolve){
	        resolve("UserJob")
	    })
	}
	getUserId().then(getUserJobById).then(function(id) {
	    console.log(id)
	})
	function testError(){
	    return new Promise(function(resolve, reject){
	        reject("reason")
	    })
	}
	testError().then(function(){

	},function(error){
	    console.log("error:" + error)
	})
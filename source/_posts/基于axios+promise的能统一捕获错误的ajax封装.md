---
title: 基于axios+promise的能统一捕获错误的ajax封装
date: 2017-6-10 21:22:46
tags: 总结记录
---

```javascript
// ajax.js



import axios from 'axios'
import qs from 'qs'
import { Message } from 'element-ui'   //用于弹出错误的提示框组件

// 将$axios作为window的全局方法
window.$axios = function (url, formData) {
	let promise = new Promise(function (resolve, reject) {
	
		let transform = [function (data) {
			// axios的数据转换功能
			return qs.stringify(data)
		}];

		axios({
			url: url,
			data: formData,
			method: 'post', //默认post请求
			headers: {
			'X-Requested-With': 'XMLHttpRequest',
			'Content-Type': 'application/x-www-form-urlencoded'
		    },
			transformRequest: transform
		})
		.then((res) => {						//如果请求成功
			var error = res.data ? res.data.EXCPTION : res.EXCPTION;		//和后台沟通好的error处理：返回错误类型和错误描述
			if (error) {	//如果后台返回error（这里的error一般是指请求发生后状态码为200，有返回值，但是请求参数有误一类的错误）
				Message.error(`操作失败，${error.desc}`)	//统一捕获错误
				reject(error)		//用reject将状态转为失败，并且将error作为下一个.catch的参数error
			} else {			
				resolve(res.data)	//如果没有返回error ，则用resolve将状态转为成功，并且将res.data作为下一个.then的参数json
			}
		})
		.catch((NetWorkError) => {				//如果请求失败，这里的NetWorkError指404，500一类的网络连接失败
			// loadingInstance.close();
			Message.error(`请求${url}时，发生${NetWorkError}`)
		})
	})
	return promise
}

//使用示例
/*
	$axios(url,formData)
	.then((json)=>{
		this.data = json
	})
	.catch((error)=>{

	})

 */
```

这样写的优点就是可以统一捕获错误，妈妈再也不用担心我为后台返回的错误抓狂了
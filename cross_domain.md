# jQuery 跨域请求

tags: jquery
---

## 一、什么是跨域

## 二、JSONP 解决跨域

`<script>`不受同源策略的限制，可以直接获取外域的js。

### 什么是JSONP

JSONP（JSON with Padding）是 JSON 的一种使用模式，JSONP 格式只能以 GET 形式请求服务器。

### 栗子

```javascript
$.ajax({
	url : "//vip.woqu.com/center/coupon/count",
	type: "GET",
	data: "status=unused",
	jsonp: "callback",//传递给请求处理程序或页面的，用以获得jsonp回调函数名的参数名(一般默认为:callback)
	jsonpCallback:"flightHandler",//自定义的jsonp回调函数名称，默认为jQuery自动生成的随机函数名，也可以写"?"，jQuery会自动为你处理数据
	dataType : 'jsonp',
	success:function(res){
		console.log(res);
	},error:function(a,b,c,d){
		alert(a+b+c+d);
	}
});
```

```java
int count = 22;
response.setContentType("text/plain");
String callback = request.getParameter("callback");
response.getWriter().write(callback + "("+Json.toJson(count)+")");
```

实现跨域请求的两个步骤：

1. ajax 请求添加`dataType : 'jsonp'`
2. Java 代码进行跨域处理[^f]
   ajax 请求最后返回的数据`res`就是 java 代码里的`count`

## 三、后台实现 CORS

CORS 全称 Cross-Origin Resource Sharing，即跨域资源共享，CORS能接受所有类型的 http 请求（JSONP只能发送get请求），只有现代浏览器才支持CORS。

参考 http://targetkiller.net/cross-domain/

[跨域资源共享 CORS 详解 - 阮一峰的网络日志](https://www.ruanyifeng.com/blog/2016/04/cors.html)

[^f]: 原理待研究

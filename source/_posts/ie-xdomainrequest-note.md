title: "IE XDomainRequest 学习笔记"
date: 2013-07-13
tags: Web Front
---

W3C CORS（跨域资源共享）是对于跨域请求的一个规范，目前各个主流浏览器都实现了这个规范，IE8开始，增加了XDomainRequest作为跨域请求的对象，而Firefox与Chrom则CORS直接实现在XMLHttpRequest对象中，更方便使用。不过IE10中，XMLHttpRequest对象也实现了CORS。本文主要总结 XDomainRequest 对象一些使用方法。<!--more-->

XDomainRequest 是个简单的实现，他仅有有限的几个方法供我们来调用，支持少数事件但足够。


- 仅只是 GET 和 POST 两个方法。
- 支持的事件有：onerror，onload，onprogress，ontimeout
- 提供的方法：abort，open，send
- 提供的属性：contentType， responseText，timeout

以下是个一个典型的代码片段

``` javascript 
var url = "http://localhost:8084/Server/HandlerServlet";

var xdr = new XDomainRequest();
xdr.onload = function() {			
	alert(xdr.responseText);
};
xdr.onerror = function() {
	alert("error");
};

xdr.timeout = 1000;
xdr.ontimeout = function() {
	alert("timeout");
};

xdr.open("get", url);
xdr.send();
```

注意，使用跨域的时候，如果需要使用跨域请求返回的内容，则需添加响应头 Access-Control-Allow-Origin:* ，* 代表了允许任何域使用响应的内容。在Java中的做法如下:

``` java
response.addHeader("Access-Control-Allow-Origin", "*");
```

上面是使用 GET 请求，然后再说一下使用 POST 请求的方式，这里有需要注意的地方，使用 POST 请求的代码片段如下：

``` javascript 
xdr.open("post", url);
var postData = encodeURI(JSON.stringify(data));
xdr.send("num=1000");
```

这里并没有什么需要注意的地方，需要注意的地方在服务端，在服务端你并不能从参数中获取到 POST 的数据，在java中你并不能通过 request.getParameter来获得num的内容，你只能从流里面读取出来，然后在自行解析，代码片段如下：

``` java
BufferedReader br = request.getReader();
String s = null;
while ((s = br.readLine()) != null) {
	System.out.println(s);
}
```

这可能是 XDomainRequest 的一个bug，也可能是故意这么实现的，这我们就不深究了，知道如何获取数据就可以了。

不管如何，XDomainRequest毕竟给我提供了一个跨域请求的方式，在Ajax开发中，如果有跨域请求，且浏览器版本在IE10以下， XDomainRequest 给了我们一个捷径。

参考：

http://msdn.microsoft.com/en-us/library/ie/cc288060(v=vs.85).aspx
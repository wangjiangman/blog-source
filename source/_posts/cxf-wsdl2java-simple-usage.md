title: "Apache CXF wsdl2java 生成客户端简单用法"
date: 2015-03-25
tags: [Java, WebService]
categories: 技术
---

cxf中带的工具包 wsdl2java 很方便的帮我们生成客户端程序，使用方式如下：

``` bash
wsdl2java -d d:\src -encoding utf-8 -autoNameResolution -client http://xx.x?wsdl
```

不指定包明的话，会按照 wsdl中的包结构生成。生成程序后，可直接参考 *_Client.java 中的例子即可执行，无需依赖 cxf 任何jar包。

更多 wsdl2java 的用法可参考： http://cxf.apache.org/docs/wsdl-to-java.html
cxf下载地址：http://cxf.apache.org/download.html

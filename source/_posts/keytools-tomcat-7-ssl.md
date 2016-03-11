title: "使用keytools 为 tomcat 7 配置ssl 双向认证"
date: 2013-07-19
tags: Java
categories: 技术
---

## 第一、证书库、证书等生成

### 1、生成服务器证书库

``` bash
keytool -validity 36500 -genkey -v -alias tomcat_server -keyalg RSA -keystore tomcat_server.keystore -dname "CN=127.0.0.1,OU=,O=,L=,ST=,c=" -storepass 123456 -keypass 123456
```

-validity 36500 有效期，以天为单位
CN 这项一定是服务器的域名或者IP地址
OU 组织单位
O 组织
L 区域
ST 州/省份
C 国家

<!--more-->

### 2、客户端证书

``` bash
keytool -validity 36500 -genkeypair -v -alias testclient -keyalg RSA -storetype PKCS12 -keystore testclient.p12 -dname "CN=testclient,OU=,O=,L=,ST=,c=" -storepass 123456
```

-storetype PKCS12 主要是为了将证书导入IE/firefox 中。

将生成的证书导入IE中。

### 3、将客户端证书导入服务器端证书库

服务器端证书不识别 p12格式的证书，需要从客户端证书导出 CER格式证书，然后将CER格式证书导入到服务器端证书中。

``` bash
keytool -export -alias testclient -keystore testclient.p12 -storetype PKCS12 -storepass 123456 -rfc -file testclient.cer
```

然后将client.cer 导入到服务器证书库(使用下面任意一个命令)

``` bash
keytool -import -v -file testclient.cer -keystore tomcat_server.keystore -storepass 123456
keytool -import -alias testclient -v -file testclient.cer -keystore tomcat_server.keystore -storepass 123456
```

注意这里的别名，如果不加别名，则默认别名则是 mykey，所以见到mykey 请不要吃惊。

### 4、从服务器证书库导出服务器证书

``` bash
keytool -export -alias tomcat_server -keystore tomcat_server.keystore -storepass 123456 -rfc -file tomcat_server.cer
```

该证书可以导入浏览器中，让客户端信任服务器证书。不导入也不影响使用，但浏览器会不信任服务器证书，会提示错误信息。

5、查看证书库中的所有证书

``` bash
keytool -list -keystore tomcat_server.keystore -storepass 123456
```

## 第二、Tomcat 配置
配置 server.xml

``` xml
<Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true"
	maxThreads="150" scheme="https" secure="true"
	keystoreFile="D:\\dev\\tomcat-https\\note\\tomcat_server.keystore" 
	keystorePass="123456" 
	truststoreFile="D:\\dev\\tomcat-https\\note\\tomcat_server.keystore" 
	truststorePass="123456"
	clientAuth="true" sslProtocol="TLS" />
```

启动tomcat 就可以了。

## 问题

如果启动时报如下错误：

``` bash
SEVERE: Failed to initialize end point associated with ProtocolHandler ["http-apr-8443"]
java.lang.Exception: Connector attribute SSLCertificateFile must be defined when using SSL with APR
	at org.apache.tomcat.util.net.AprEndpoint.bind(AprEndpoint.java:507)
	at org.apache.tomcat.util.net.AbstractEndpoint.init(AbstractEndpoint.java:610)
	at org.apache.coyote.AbstractProtocol.init(AbstractProtocol.java:429)
	at org.apache.catalina.connector.Connector.initInternal(Connector.java:981)
	at org.apache.catalina.util.LifecycleBase.init(LifecycleBase.java:102)
	at org.apache.catalina.core.StandardService.initInternal(StandardService.java:559)
	at org.apache.catalina.util.LifecycleBase.init(LifecycleBase.java:102)
	at org.apache.catalina.core.StandardServer.initInternal(StandardServer.java:814)
	at org.apache.catalina.util.LifecycleBase.init(LifecycleBase.java:102)
	at org.apache.catalina.startup.Catalina.load(Catalina.java:640)
	at org.apache.catalina.startup.Catalina.load(Catalina.java:665)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:601)
	at org.apache.catalina.startup.Bootstrap.load(Bootstrap.java:281)
	at org.apache.catalina.startup.Bootstrap.main(Bootstrap.java:455)
```

则是由于Tomcat中的SSL采用了 APR来实现的，关于SSL的实现，Tomcat提供了两种：JSSE和APR，如果安装了 APR，则优先选择APR作为实现。APR的ssh配置需要通OpenSSH来进行配置。这在 server.xml 中有说明：

> Define a SSL HTTP/1.1 Connector on port 8443
This connector uses the JSSE configuration, when using APR, the connector should be using the OpenSSL style configuration described in the APR documentation

那么避免采用 APR呢？ 有两种方法，
1，将 protocol="HTTP/1.1" 修改为 protocol="org.apache.coyote.http11.Http11Protocol"
2，在windows 下，可以将 bin 目录下的 tcnative-1.dll 删掉。
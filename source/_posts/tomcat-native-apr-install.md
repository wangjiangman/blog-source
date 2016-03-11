title: "Tomcat 安装配置 Native APR 模式"
date: 2015-08-04
tags: Tomcat
categories: 技术
---

APR 能大幅提高 tomcat 的性能是不争的事实，但我并没有做过性能比较测试。本文记录 CentOS 6.5 下为 tomcat 7 安装和配置 APR 模式的步骤和过程。 

<!--more-->

## 技术准备

### 什么是APR？ 

以下是来自[维基百科](https://zh.wikipedia.org/wiki/Apache%E5%8F%AF%E7%A7%BB%E6%A4%8D%E8%BF%90%E8%A1%8C%E6%97%B6)的内容

> Apache可移植运行时（ Apache Portable Runtime，简称APR）是Apache HTTP服务器的支持库，提供了一组映射到下层操作系统的API。如果操作系统不支持某个特定的功能，APR将提供一个模拟的实现。这样程序员使用APR编写真正可在不同平台上移植的程序。

### 什么是 tomcat-native

tomcat-native 库为 Tomcat 提供了本地实现。 tomcat-native 依赖于三个组件：APR, OPENSSL, JDK。

## 大致思路

大致思路就是通过 tomcat-native 库，使tomcat运行时通过APR更多的调用本地API，达到提升性能的目的。由于依赖关系，安装时，先安装 APR, OPENSSL， 然后再安装 tomcat-native, 最后配置 tomcat 启动时依赖的库路径。 

## 配置安装

具体的安装步骤其实在 tomcat 自带的 tomcat-native.tar.gz 文件中就有，这里记录是自己的安装和配置过程，大致一样，略有不同。我在安装的没有选择将 tomcat-native 安装在某个tomcat下，而是独立安装。

1, 安装 apr, openssl

``` shell
yum install apr-devel openssl-devel
```

2, 安装 tomcat-native

tomcat-native 安装文件已经存在于 tomcat 中，位于 $CATALINA_HOME/bin 的文件 tomcat-native.tar.gz。 将文件 tomcat-native.tar.gz 拷贝出来后解压，配置，安装。

``` shell
tar xzcf tomcat-native.tar.gz
cd tomcat-native-1.1.32-src/jni/native
./configure --with-apr=/usr/bin/apr-1-config --with-ssl=yes
make && make install
```

安装完成后，会看到下面提示，标识已经安装到 /usr/local/apr/lib 中。

``` shell
Libraries have been installed in:
   /usr/local/apr/lib
```

**以上步骤都是通过 root 用户操作，以下步骤使用 tomcat 的所有者用户操作**

3, 配置 tomcat 依赖库

在 $CATALINA_HOME/bin/setenv.sh 中添加（没有则新建)
export CATALINA_OPTS="$CATALINA_OPTS -Djava.library.path=/usr/local/apr/lib"

4, 配置 tomcat server.xml

打开下面的配置

``` xml
<Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
```

确保 Connector 配置中的 protocol 为下列值中之一。

```xml
protocol="HTTP/1.1" 
protocol="org.apache.coyote.http11.Http11AprProtocol"
```
> 如果指定为 HTTP/1.1, 则 tomcat 自动选择合适的协议。

最后重启 Tomcat.



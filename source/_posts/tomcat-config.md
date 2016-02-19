title: "Tomcat 配置汇总"
date: 2013-01-07
tags: Tomcat
---

## Tomcat6 配置线程池

打开 tomcat 的配置文件 server.xml, 去掉以下代码的注释：

``` xml
<executor
        maxthreads="150"
        minsparethreads="4"
        name="tomcatThreadPool"
        nameprefix="catalina-exec-"></executor>
```

然后在Connector中加入如下executor参数，如下：

``` xml
<connector
        connectiontimeout="20000"
        executor="tomcatThreadPool"
        port="8080" protocol="HTTP/1.1"
        redirectport="8443"></connector>
```

name 指定线程池的名称，以供Connector使用
namePrefix 这个属性用来指定 线程名称的前缀，线程名称类似于：catalina-exec-1
maxThreads 指定最大的线程数
minSpareThreads 指定最少备用线程数

更多参数可以参看tomcat官方文档：["http://tomcat.apache.org/tomcat-6.0-doc/config/executor.html](http://tomcat.apache.org/tomcat-6.0-doc/config/executor.html)

## 在Eclipse中远程 DEBUG Tomcat

catalina.sh加入以下配置后重启tomcat：

``` bash 
CATALINA_OPTS="-Xdebug -Xnoagent -Djava.compiler=NONE -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=2010"
```

其中address只指定远程连接的端口号，transport=dt_socket是指定远程debug（dt_shmem时为本机debug）。

进入Eclipse，Run-&gt;Debug Configuration-&gt;Remote Java Application,双击或右键-&gt;New，Connect Type 选择 Socket Attach，输入Host和Port后，点击Debug 按钮就可以了

## Tomcat 配置 JMX 实现远程监控

修改 setenv.sh (没有则添加), 添加下面一下配置行：

``` bash 
CATALINA_OPTS="$CATALINA_OPTS -Djava.rmi.server.hostname=192.168.80.168
    -Dcom.sun.management.jmxremote.port=8999
    -Dcom.sun.management.jmxremote.ssl=false
    -Dcom.sun.management.jmxremote.authenticate=true
    -Dcom.sun.management.jmxremote.password.file=the-password-file-path
    -Dcom.sun.management.jmxremote.access.file=the-access-file-path";
```

- -Djava.rmi.server.hostname 的值为当前主机的IP即可。
- -Dcom.sun.management.jmxremote.port=8999 为端口号
- -Dcom.sun.management.jmxremote.authenticate=true 表明需要认证
- -Dcom.sun.management.jmxremote.password.file 指定存放用户/密码的文件
- -Dcom.sun.management.jmxremote.access.file 指定用户的访问权限

jmxremote.authenticate=false时，不用配置最后两项。后面两项是密码文件和访问权限文件，默认位于bin目录下，2个文件的权限为400。

the-password-file-path 格式为：
```
admin admin_password
master master_password
```

the-access-file-path 格式为：
```
admin readwrite
master readonly
```

重启 tomcat 后，就可以使用 VisualVM进行远程监控了。

由于 tomcat jmx 的远程通信接口是随机的，不便于通过防火墙，可采用下面的配置方式：

1，setenv.sh 去掉行 -Dcom.sun.management.jmxremote.port=8999
``` bash 
CATALINA_OPTS="$CATALINA_OPTS -Djava.rmi.server.hostname=192.168.80.168
    -Dcom.sun.management.jmxremote.ssl=false
    -Dcom.sun.management.jmxremote.authenticate=true
    -Dcom.sun.management.jmxremote.password.file=the-password-file-path
    -Dcom.sun.management.jmxremote.access.file=the-access-file-path";
```

2，server.xml 添加

```xml
<Listener className="org.apache.catalina.mbeans.JmxRemoteLifecycleListener" rmiRegistryPortPlatform="18085" rmiServerPortPlatform="28085" />
```

注意：由于 org.apache.catalina.mbeans.JmxRemoteLifecycleListener 在 tomcat7 中已经独立出来，所以需要单独下载 catalina-jmx-remote.jar 放在 $CATALINA_HOME/lib 下。

``` bash
wget http://mirrors.hust.edu.cn/apache/tomcat/tomcat-7/v7.0.63/bin/extras/catalina-jmx-remote.jar
```

## Windows 下tomcat 启动时，弹出窗口一闪就关了

有时候在修改了 tomcat 的一些参数后，启动时运行 startup.bat, 如果有错误，弹出窗口一闪就关掉了，不知道启动错误的原因，对文件 startup.bat 做如下修改：

``` bash 
rem call "%EXECUTABLE%" start %CMD_LINE_ARGS%
call "%EXECUTABLE%" run %CMD_LINE_ARGS%
```

就是将call "%EXECUTABLE%" start %CMD_LINE_ARGS% 行中的 start 修改为 run。这样修改后，启动时，就不会弹出新cmd窗口，如果有错，错误就直接显示在当前cmd窗口了。


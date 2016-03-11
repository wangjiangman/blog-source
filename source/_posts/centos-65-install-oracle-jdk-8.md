title: "centos 6.5 安装 oracle jdk 8u40"
date: 2015-03-27 23:18:09
tags: [Linux, Java]
categories: 技术
---

采用 oracle jdk 压缩版本进行安装，无废话，直接上干货。<!-- more -->

## 安装前准备

### 惯例，安装之前先升级

``` bash
yum update
```

<!-- more -->

### 搜索其他版本jdk

``` bash
rpm -qa | grep -E '^open[jre|jdk]|j[re|dk]'
```

输出

``` bash
pygobject2-doc-2.20.0-5.el6.x86_64
libbasicobjects-0.1.1-11.el6.x86_64
cjet-0.8.9-9.1.el6.x86_64
cjkuni-uming-fonts-0.2.20080216.1-36.el6.noarch
cjkuni-fonts-common-0.2.20080216.1-36.el6.noarch
pygobject2-devel-2.20.0-5.el6.x86_64
java-1.7.0-openjdk-devel-1.7.0.75-2.5.4.0.el6_6.x86_64
java-1.6.0-openjdk-devel-1.6.0.34-1.13.6.1.el6_6.x86_64
cjkuni-ukai-fonts-0.2.20080216.1-36.el6.noarch
java-1.6.0-openjdk-1.6.0.34-1.13.6.1.el6_6.x86_64
pygobject2-codegen-2.20.0-5.el6.x86_64
eject-2.1.5-17.el6.x86_64
openjpeg-libs-1.3-10.el6_5.x86_64
pygobject2-2.20.0-5.el6.x86_64
java-1.7.0-openjdk-1.7.0.75-2.5.4.0.el6_6.x86_64
openjade-1.3.2-36.el6.x86_64
```

### 删除其他版本jdk

``` bash
yum remove java-1.6.0-openjdk
yum remove java-1.7.0-openjdk
```

## 下载安装 oracle jdk

我选择安装在 /opt 下，所以进入到 /opt 目录下 下载文件

``` bash
cd /opt
```

开始下载文件，使用下面的命令能直接下载 oracle jdk 8u40

``` bash
# 64位 
wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u40-b25/jdk-8u40-linux-x64.tar.gz"
# 32位
wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u40-b25/jdk-8u40-linux-i586.tar.gz"
```

再解压缩即可

``` bash
tar -xvf jdk-8u40-linux-x64.tar.gz
```

## 配置环境变量

环境变量，避免在直接修改 /etc/profile, 在 /etc/profile.d/ 目录下创建文件 java.sh

``` bash
vi /etc/profile.d/java.sh
```

> profile.d 目录下的所有 sh 文件都会被执行。参看 /etc/profile 中 最后一段。 如下：

``` bash 
for i in /etc/profile.d/*.sh ; do
    if [ -r "$i" ]; then
        if [ "${-#*i}" != "$-" ]; then
            . "$i"
        else
            . "$i" >/dev/null 2>&1
        fi
    fi
done
```

添加下面的内容

``` bash
#!/bin/bash
JAVA_HOME=/opt/java/jdk1.8.0_40/
PATH=$JAVA_HOME/bin:$PATH
export PATH JAVA_HOME
export CLASSPATH=.
```

保存并关闭文件，然后给文件赋予可执行权限
``` bash
chmod +x /etc/profile.d/java.sh
```

使环境变量生效

``` bash
source /etc/profile.d/java.sh
```

> source 命令用来执行某个文件，同 ./filename 作用一样。不同的是 ./filename 在一个 子shell中执行，而 source 会在当前 shell中执行。


Done!

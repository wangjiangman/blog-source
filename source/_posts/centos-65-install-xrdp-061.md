title: "CentOS 6.5 安装远程桌面工具 xrdp 0.6.1"
date: 2015-03-26
tags: [Linux, CentOS]
categories: 技术
---

linux 远程桌面工具中，除了 vnc ，xrdp也是很不错的，连接速度很快，直接使用微软的远程桌面工具mstsc即可链接。下面整理一下安装配置步骤。<!-- more -->

> 使用root用户进行以下操作

## 安装依赖包

``` bash
yum upgrade
yum install gcc pam-devel openssl-devel libXfont pixman libX11-devel libXfixes-devel autoconf automake libtool
yum install tigervnc-server
yum groupinstall Desktop
```

这里包含了 vnc-server ，这是必须的，否则最后都安装成功后，登录后会出错，错误信息大致如下：



``` bash
connecting to sesman ip 127.0.0.1 port 3350
sesman connect ok
sending login info to session manager, please wait...
xrdp_mm_process_login_response: login successful for display
started connecting
connecting to 127.0.0.1 5910
error - problem connecting
```

## 安装 xrdp

下载文件、解压缩、编译、安装。

``` bash
wget http://sourceforge.net/projects/xrdp/files/xrdp/0.6.1/xrdp-v0.6.1.tar.gz
tar xzvf xrdp-v0.6.1.tar.gz
cd xrdp-v0.6.1
./bootstrap
./configure
make
make install clean
```

## 启动 xrdp

``` bash
/etc/xrdp/xrdp.sh start
```

命令可用选项：start|stop|restart|force-reload

## 加入开机启动

将 xrdp 设置为随机器启动，避免每次手工启动。

``` bash
vi /etc/rc.d/rc.local
# 文件尾部添加下面的行
/etc/xrdp/xrdp.sh start
```

## 添加防火墙规则

``` bash 
iptables -I INPUT 4 -m state --state NEW -p tcp --dport 3389 -j ACCEPT

service iptables save
service iptables reload
```

## 远程桌面链接

windows 中启动远程桌面程序 mstsc，输入 ip 地址，在打开的对话框中 Module 选择sesman-Xvnc (默认值)，输入 username 和 password ，就可以了。

Done！

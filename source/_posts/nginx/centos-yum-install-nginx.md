title: "CentOS yum install nginx"
date: 2015-06-01
tags: [Linux, Nginx]
---

Install Nginx by yum on CentOS 6.5 . See the follows. <!--more-->

## 创建 nginx 仓库描述文件

``` bash
[root@linux001 ~]# cd /etc/yum.repos.d/
[root@linux001 yum.repos.d]# vi nginx.repo
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```

## 安装 nginx

``` shell
yum install nginx -y 
```

## 启动/停止 nginx

``` shell
service nginx start
```

nginx 命令参数说明：
- start - 启动
- stop - 停止
- restart - 重启动
- reload - 热加载配置文件
- status - 查看状态

> nginx配置文件位置  /etc/nginx/ ，根据自己需要修改配置

## 添加防火墙规则

``` bash
iptables -I INPUT 4 -m state --state NEW,ESTABLISHED -p tcp --dport 80 -j ACCEPT
service iptables save
service iptables restart
```

Done.

title: "linux 上安装 virtualmin/webmin"
date: 2013-03-06
tags: Linux
categories: 技术
---
Webmin是一个可视化的linux管理工具，囊括了所有的linux管理功能；而Virtualmin则是基于webmin的网站虚拟主机管理面板，可以很方便的帮助我们建站（FTP，Apache、mysql、php等）。这里说一下如何在linux系统上安装virtualmin和webmin。

Virtualmin给我提供了一个安装脚本，可以一键安装webmin和virtualmin。virtualmin分为免费版和专业版，对于我来说，免费版就够了。这个页面（<a href="http://virtualmin.com/download.html">http://virtualmin.com/download.html</a>）是virtualmin官方的下载安装说明。<!--more-->

下载地址：<a href="http://software.virtualmin.com/gpl/scripts/install.sh">http://software.virtualmin.com/gpl/scripts/install.sh</a>

具体执行命令如下:

``` bash
wget http://software.virtualmin.com/gpl/scripts/install.sh
chmod +x install.sh
./install.sh
```

然后按照提示操作，等着安装结束就好了。

安装结束后，使用https://your-server-ip:10000 来访问就可以了， 登录时使用root用户即可。
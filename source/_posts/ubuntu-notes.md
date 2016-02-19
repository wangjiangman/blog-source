title: "折腾 Ubuntu"
date: 2013-01-14
tags: Ubuntu
---

最近开始学习使用ubuntu，于是在virtual box上安装了ubuntu server来折腾，安装的时候，什么都没有默认安装，所以什么都需要自己手动来安装，顺便学习一下ubuntu。<!--more-->

安装完成后，首先更新一下系统软件，保持最新，使用如下命令：

``` bash
sudo apt-get update
```

紧接着安装了ssh，以便可以在其他的终端工具上访问此服务器，使用如下命令：

``` bash 
sudo apt-get install openssh-server
```

一般来说，就可以直接在其他终端连接了，如果没有启动，则手动启动一下ssh：

``` bash 
sudo service ssh start
```

备注：如果你登录用户不是root用户，则命令前都需要使用sudo来切换到root模式，否则导致很多命令没有权限。

## 安装ftp工具vsftpd

1、使用命令：

``` bash 
sudo apt-get install vsftpd
```

2、配置vsftpd

vsftpd 默认的用户模式为匿名用户，如果你想启用本地用户的ftp，则需要修改配置文件/etc/vsftpd.conf

注释掉：anonymous_enable=YES

打开注释：local_enable=YES 和 write_enable=YES

保存文件后，然后重启vsftpd: sudo service vsftpd restart

## php5-gd扩展安装

在Ubuntu下，wordpress 使用验证码插件提示：ERROR: si-captcha.php plugin says GD image support not detected in PHP， 是由于 Ubuntu下没有安装 php5-gd， 使用如下命令安装即可

``` bash
sudo apt-get install php5-gd
```

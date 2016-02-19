title: "scp 命令"
date: 2015-09-12
tags: [Linux]
---

<code>scp</code> 命令用于在不同 linux 机器之间拷贝/复制文件、目录，是 <code>cp</code> 命令的加强版。 <!--more-->

## 基本命令格式和参数

### 基本用法

scp [可选参数] 源文件/目录 目标文件/目录

### 常用的可选参数

- <code>-P</code> 大写的，指定端口号
- <code>-r</code> 递归复制整个目录
- <code>-v</code> 显示所有 debug 信息
- <code>-q</code> 不显示传输进度
- <code>-C</code> 大写的，启用压缩
- <code>-4</code> 使用 IPv4 地址
- <code>-6</code> 使用 IPv6 地址

## 本地复制到远程

### 复制文件

```shell
scp /root/test.txt root@192.168.1.10:/root/
```

将 /root/test.txt 拷贝到 192.168.1.10 的 /root/ 目录下，文件名还是 text.txt，使用 root 用户，此时会提示输入远程 root 用户的密码。

### 复制文件并重命名

```shell
scp /root/test.txt root@192.168.1.10:/root/test1.txt
```

将 /root/test.txt 拷贝到 192.168.1.10 的 /root/ 目录下，文件名还是 text1.txt，使用 root 用户，此时会提示输入远程 root 用户的密码。

### 复制整个目录

```shell
scp -r /root/test/ root@192.168.1.10:/root/
```

将整个目录 /root/test/ 复制到  192.168.1.10 的 /root/ 下，使用 root 用户，此时会提示输入远程 root 用户的密码。

## 远程复制到本地

远程复制到本地 与 从本地复制到远程命令类似，不同的是 远程文件作为源文件在前，本地文件作为目标文件在后。

```shell
scp root@192.168.1.10:/root/test.txt /root/test.txt 
```

## 避免每次都输入远程密码

scp 默认使用 ssh， 每次传输都需要输入远程用户的密码，十分麻烦。但是如果两台机器之间建立了信任关系后，则不需要输入密码，如下

首先在本机上生成配对密钥

```shell
ssh-keygen -t rsa 
```

然后将本机上的公钥文件拷贝到远程机器，并命名为 authorized_keys

```shell
scp /root.ssh/id_rsa.pub 192.168.1.10:/root/.ssh/authorized_keys 
```

以后再传文件就不用输入密码了，包括使用 ssh, sftp 时，都不用输入密码了。

Done.

title: "CentOS 7 下不能使用 ifconfig 命令"
date: 2016-01-12
tags: [Linux, CentOS]
---

CentOS 7 为最小化安装，安装完成后，ifconfig 命令不能使用，究其原因是因为没有安装 net-tools 工具包，安装后就可以了。

```shell
yum install net-tools
```

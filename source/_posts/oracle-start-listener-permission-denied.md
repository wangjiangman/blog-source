title: "Linux 下 ORACLE 启动侦听报错 TNS:permission denied"
date: 2015-04-02 09:00:00
tags:
categories: 技术
---


今天开发环境 oracle 启动侦听的时候，出现了 TNS:permission denied 的问题，并最终找到了解决方案，现在共享出来。

<!-- more -->

## 错误描述

``` bash
[oracle@oracle ~]$ lsnrctl start     

LSNRCTL for Linux: Version 11.2.0.1.0 - Production on 27-MAR-2015 15:37:59

Copyright (c) 1991, 2009, Oracle.  All rights reserved.

Starting /u01/app/oracle/product/11.2.0/dbhome_1/bin/tnslsnr: please wait...

TNSLSNR for Linux: Version 11.2.0.1.0 - Production
System parameter file is /u01/app/oracle/product/11.2.0/dbhome_1/network/admin/listener.ora
Log messages written to /u01/app/oracle/diag/tnslsnr/oracle/listener/alert/log.xml
Error listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=IPC)(KEY=EXTPROC1521)))
TNS-12555: TNS:permission denied
 TNS-12560: TNS:protocol adapter error
  TNS-00525: Insufficient privilege for operation
   Linux Error: 1: Operation not permitted

Listener failed to start. See the error message(s) above...
```

## 解决方案

出现这个错误是由于没有 /var/tmp/.oracle 的读写权限引起的。由于开发环境没有严格管理，不知道哪位同学把此目录的所有者改成了root，导致 oracle 用户没有权限操作此目录。使用下面方式修正过来即可。

``` bash
cd /var/tmp
chown oracle .oracle
chgrp oinstall .oracle
chmod 777 .oracle
```

> 看到网上有资料说 直接干掉 /var/tmp/.oracle 这个目录也是可以的，我没有尝试，不知道效果如何，这里仅作笔记。

Done!

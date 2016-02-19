title: "Oracle 自动备份脚本"
date: 2015-06-18
tags: [Linux, Oracle]
---

本脚本的主要思路是通过 oracle 字典的 exp 命令将数据库导出为 dmp 文件，然后将 dmp 文件打包压缩成 tar.gz 文件，最后将文件 ftp 到远程服务器上。

## 建立脚本文件<!--more-->

> 使用 oracle 用户

``` shell
vi /home/oracle/backup/backup.sh

#!/bin/bash

export ORACLE_HOME=/u01/app/oracle
export PATH=$PATH:$ORACLE_HOME/bin
export ORACLE_SID=orcl

today=$(date +%Y%m%d)

cd /home/oracle/backup
exp xxx/xxx123 file=xxx-${today}.dmp log=xxx-${today}.log
tar -zcf xxx-${today}.tar.gz xxx-${today}.dmp xxx-${today}.log --remove-files

exp yyy/yyy123 file=yyy-${today}.dmp log=yyy-${today}.log
tar -zcf yyy-${today}.tar.gz yyy-${today}.dmp yyy-${today}.log --remove-files

ftp -i -v -n 192.168.1.10 <<END
user oracle oracle
binary
mput xxx-${today}.tar.gz yyy-${today}.tar.gz
bye
END
```

## 设置每天自动备份

> 使用 oracle 用户

``` shell
crontab -e
0 3 * * * /home/oracle/backup/backup.sh >> /home/oracle/backup/backup.log 2>&1
```

> 使用 root 用户

``` shell
service crontab restart
```


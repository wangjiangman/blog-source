title: "通过直接拷贝数据文件实现 Oracle 迁移"
date: 2015-09-17
tags: [Oracle]
---

Oracle 数据迁移是比较麻烦的，作为菜鸟来说更是如此。最近由于更换服务器，需要将 Oracle 迁移到另外一台机器，在两个服务器环境相同，以及 Oracle 版本相同的前提下，通过直接拷贝数据文件到新服务器，就可以直接迁移成功。这里记录一下迁移步骤。<!--more-->

## 背景

由于机房服务器变更，需要将 Oracle 迁移到一台新服务器上去。一下是环境说明：

- 原服务器地址： 192.168.1.15
- 新服务器地址： 192.168.1.18
- 操作系统：都是 CentOS 6.5
- 数据库版本： 都是 11.2.0.1

## 新服务器上安装和配置 Oracle

> 192.168.1.18

在新服务器（192.168.1.18）上安装了Oracle，为了保险，主机名、数据库实例名、安装目录都和原数据库保持一致。具体安装方法可参考：[centos 6.5 安装 oracle 11gR2 及配置Oracle自动启动](/centos-65-install-oracle-11g-r2.html)

## 查询需要拷贝的文件

> 192.168.1.15
> 需要切换到 oracle 用户
> 使用 sqlplus 登录进 Oracle 数据库

```shell
sqlplus / as sysdba
```

```sql
SQL> show parameter pfile
NAME                                 TYPE        VALUE
------------------------------------ ----------- ------------------------------
spfile                               string      /u01/app/oracle/product/11.2.0/dbhome_1/dbs/spfileorcl.ora

SQL> show parameter control
NAME                                 TYPE        VALUE
------------------------------------ ----------- ------------------------------
control_file_record_keep_time        integer     7
control_files                        string      /u01/app/oracle/oradata/orcl/control01.ctl, /u01/app/oracle/recovery_area/orcl/control02.ctl
control_management_pack_access       string      DIAGNOSTIC+TUNING

SQL> select  * from v$logfile;
    GROUP# STATUS  TYPE    MEMBER                                                                           IS_RECOVERY_DEST_FILE
---------- ------- ------- -------------------------------------------------------------------------------- ---------------------
         3         ONLINE  /u01/app/oracle/oradata/orcl/redo03.log                                          NO
         2         ONLINE  /u01/app/oracle/oradata/orcl/redo02.log                                          NO
         1         ONLINE  /u01/app/oracle/oradata/orcl/redo01.log                                          NO

SQL> select name from v$datafile;
NAME
--------------------------------------------------------------------------------
/u01/app/oracle/oradata/orcl/system01.dbf
/u01/app/oracle/oradata/orcl/sysaux01.dbf
/u01/app/oracle/oradata/orcl/undotbs01.dbf
/u01/app/oracle/oradata/orcl/users01.dbf
/u01/app/oracle/oradata/orcl/users02.dbf

SQL> select name from v$tempfile;
NAME
--------------------------------------------------------------------------------
/u01/app/oracle/oradata/orcl/temp01.dbf
```

根据以上查询结果，发现有以下文件需要拷贝：

```
/u01/app/oracle/product/11.2.0/dbhome_1/dbs/spfileorcl.ora
/u01/app/oracle/oradata/orcl/control01.ctl
/u01/app/oracle/recovery_area/orcl/control02.ctl
/u01/app/oracle/oradata/orcl/redo03.log
/u01/app/oracle/oradata/orcl/redo02.log
/u01/app/oracle/oradata/orcl/redo01.log
/u01/app/oracle/oradata/orcl/system01.dbf
/u01/app/oracle/oradata/orcl/sysaux01.dbf
/u01/app/oracle/oradata/orcl/undotbs01.dbf
/u01/app/oracle/oradata/orcl/users01.dbf
/u01/app/oracle/oradata/orcl/users02.dbf
/u01/app/oracle/oradata/orcl/users03.dbf
/u01/app/oracle/oradata/orcl/temp01.dbf
```

## 停掉原数据库与新数据库

> 192.168.1.15 和 192.168.1.18
> 使用 root 用户

```shell
service oracle stop
```

## 使用scp拷贝文件到新服务器

> 192.168.1.15 
> scp 命令可以把文件复制到远程 Linux 服务器，可以参考：[scp 命令](/2015/linux-scp-notes.html) 

```shell
scp /u01/app/oracle/product/11.2.0/dbhome_1/dbs/spfileorcl.ora oracle@192.168.1.18:/u01/app/oracle/product/11.2.0/dbhome_1/dbs/spfileorcl.ora
scp /u01/app/oracle/oradata/orcl/control01.ctl oracle@192.168.1.18:/u01/app/oracle/oradata/orcl/control01.ctl
scp /u01/app/oracle/recovery_area/orcl/control02.ctl oracle@192.168.1.18:/u01/app/oracle/recovery_area/orcl/control02.ctl
scp /u01/app/oracle/oradata/orcl/redo03.log oracle@192.168.1.18:/u01/app/oracle/oradata/orcl/redo03.log
scp /u01/app/oracle/oradata/orcl/redo02.log oracle@192.168.1.18:/u01/app/oracle/oradata/orcl/redo02.log
scp /u01/app/oracle/oradata/orcl/redo01.log oracle@192.168.1.18:/u01/app/oracle/oradata/orcl/redo01.log
scp /u01/app/oracle/oradata/orcl/system01.dbf oracle@192.168.1.18:/u01/app/oracle/oradata/orcl/system01.dbf
scp /u01/app/oracle/oradata/orcl/sysaux01.dbf oracle@192.168.1.18:/u01/app/oracle/oradata/orcl/sysaux01.dbf
scp /u01/app/oracle/oradata/orcl/undotbs01.dbf oracle@192.168.1.18:/u01/app/oracle/oradata/orcl/undotbs01.dbf
scp /u01/app/oracle/oradata/orcl/users01.dbf oracle@192.168.1.18:/u01/app/oracle/oradata/orcl/users01.dbf
scp /u01/app/oracle/oradata/orcl/users02.dbf oracle@192.168.1.18:/u01/app/oracle/oradata/orcl/users02.dbf
scp /u01/app/oracle/oradata/orcl/users03.dbf oracle@192.168.1.18:/u01/app/oracle/oradata/orcl/users03.dbf
scp /u01/app/oracle/oradata/orcl/temp01.dbf oracle@192.168.1.18:/u01/app/oracle/oradata/orcl/temp01.dbf
```

等待拷贝完成

## 尝试启动新数据库

> 192.168.1.18
> 使用 oracle 用户

dba 登录进行启动数据库

```shell
[oracle@oracle ~]$ sqlplus / as sysdba

SQL*Plus: Release 11.2.0.1.0 Production on Thu Sep 17 09:26:11 2015

Copyright (c) 1982, 2009, Oracle.  All rights reserved.

Connected to an idle instance.

SQL> startup
ORACLE instance started.

Total System Global Area 6747725824 bytes
Fixed Size                  2213976 bytes
Variable Size            5100275624 bytes
Database Buffers         1610612736 bytes
Redo Buffers               34623488 bytes
Database mounted.
Database opened.
```

注意最后一句，到这里就启动成功了。这次很顺利，没有出现意外。也可以通过以下语句检测数据库的状态：

```sql
SQL> select status from v$instance;

STATUS
------------
OPEN
```

如果 <code>Database mounted</code> 成功后报错，也就是数据库最终不是 open 状态，只是 mounted 状态， 可以尝试恢复数据库。

```sql
recover database;
```

完成后，再打开数据库，一般可以成功。

```sql
alter database open;
```

## 验证两个库的数据

根据自己的实际情况进行验证, 这里不再赘述. 

Done.

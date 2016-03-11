title: "oracle 学习笔记"
date: 2015-04-06
tags: [Oracle]
categories: 技术
---

## 常用命令

- lsnrctl status 查看监听状态
- lsnrctl start  启动监听
- lsnrcrl stop   停止监听
- sqlplus "/as sysdba" 以管理员身份进入命令行
  - startup 启动数据库实例
  - shutdown immediate 立刻关闭数据库
  
<!-- more -->

## oracle启动步骤

1. 启动侦听 lsnrctl start
1. 启动数据库实例 

``` bash
[oracle@linux001 ~]$ sqlplus "/as sysdba"

SQL*Plus: Release 11.2.0.1.0 Production on Tue Dec 16 13:50:41 2014

Copyright (c) 1982, 2009, Oracle.  All rights reserved.


Connected to:
Oracle Database 11g Release 11.2.0.1.0 - 64bit Production

SQL> startup
ORA-01081: cannot start already-running ORACLE - shut it down first
SQL> shutdown immediate
Database closed.
Database dismounted.
ORACLE instance shut down.
SQL> 
SQL> 
SQL> 
SQL> 
SQL> startup
ORACLE instance started.

Total System Global Area  972898304 bytes
Fixed Size                  2219272 bytes
Variable Size             285213432 bytes
Database Buffers          679477248 bytes
Redo Buffers                5988352 bytes
Database mounted.
Database opened.
SQL> 
```

## 连接Oracle时，建立连接很慢

表现为客户端连接 Oracle 时，建立连接时很慢，约耗时10-30秒，建立连接后，查询很快。这个主要是由于服务器主机配置了DNS解析导致的，去掉DNS后连接正常。

``` bash
[root@linux001 ~]# vi /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
TYPE=Ethernet
UUID=1407a1c8-3705-435c-8580-4e1ab2b6131e
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=yes
IPV6INIT=no
NAME="System eth0"
IPADDR=192.168.1.20
PREFIX=24
GATEWAY=192.168.1.1
#DNS1=192.168.1.1
HWADDR=00:E0:4C:19:CF:79
LAST_CONNECT=1418632757
```

## 用户、表空间、权限相关

**创建用户**

``` sql
- create user username identified by password; 
```

**创建表空间**

``` sql
create tablespace tablespace_name logging datefile 'd:\oracle\tablespace\tabespace_name.dbf' size 40m autoextend on next 40m maxsize unlimited default storage (initial 40m next 40m pct increase 10); 
```

**分配表空间给用户**

``` sql
alert user username default tablespace tablespace_name; 
```

**授权**

``` sql
grant create session, create table, create view, unlimited tablespace to username; 
```

**<code>grant connect, resource to username</code> 的含义**
- connect 是连接权限
- resource 包括
  - CREATE   CLUSTER
  - CREATE   INDEXTYPE
  - CREATE   OPERATOR
  - CREATE   PROCEDURE
  - CREATE   SEQUENCE
  - CREATE   TABLE
  - CREATE   TRIGGER
  - CREATE   TYPE

**调试存储过程权限**

``` sql
GRANT debug any procedure, debug connect session TO username;
```

## 查杀死锁会话

```sql
SELECT OBJECT_NAME AS 对象名称,S.SID,S.SERIAL#,P.SPID AS 系统截程号 , s.USERNAME
FROM V$LOCKED_OBJECT L , DBA_OBJECTS O , V$SESSION S , V$PROCESS P  
WHERE L.OBJECT_ID=O.OBJECT_ID AND L.SESSION_ID=S.SID AND S.PADDR=P.ADDR; 
```

``` sql
-- 查看被锁对象
SELECT * FROM V$LOCKED_OBJECT WHERE ORACLE_USERNAME= 'USERNAME';
-- 查看被锁对象
SELECT v.sid,v.SERIAL#,v.USERNAME,v.ACTION FROM v$session v WHERE v.sid IN (SELECT session_id FROM v$locked_object WHERE ORACLE_USERNAME= 'USERNAME')
```

```sql
-- 杀死会话： SID,SERIAL# 由上个语句查出
ALTER SYSTEM KILL SESSION 'SID,SERIAL#';  
```

## 超过最大进程数

今天碰到一个问题，登陆不上数据库了，登陆时报错：<code>ORA-00020: maximum number of processes (150) exceeded</code>。这意思是很明显，就是超过最大进程数了，这个可以问题可以通过修改进程数的设置来进行修改，方法如下：

``` sql
SQL>alter system set processes = 500 scope=SPFILE;
SQL>shutdown immediate
SQL>startup
SQL> show parameter processes
```

当然上面的方法只是治标不治本的解决方法。经过观察，发现是由于数据库连接数过多引起的进程数过多，因为每建立一个链接，oracle就会创建一个对应的进程，通过查看 oracle 中的 session 或者 通过查看 oracle 用户的进程可以得到验证，通过下面的命令可以 oracle 链接打开的文件数。

``` bash
lsof -i:1521
```

当时就看到有个 IP 地址与 oracle 机器建立了好多链接，于是得出该IP地址的程序操作后没有及时关闭连接，导致连接不能释放。经与同事沟通后，验证了猜测，修复了程序后恢复正常。

@2015-04-08

## 查询每个表占用的存储空间

``` sql
select segment_name,sum(bytes)/1024/1024 from user_extents group by segment_name;
```

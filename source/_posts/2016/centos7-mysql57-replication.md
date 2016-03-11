title: "Mysql 配置主从复制"
date: 2016-01-18
tags: [Linux,Mysql]
categories: 技术
---

Mysql 5.7.10 配置主从复制的步骤很简单，这里记录下配置全新的主从复制的步骤。<!--more-->

## Master

### 配置文件

```
[root@centos7-1 ~]# vi /etc/my.cnf

[mysqld]
log-bin=mysql-bin
server-id=1
```

重启 mysql

```
[root@centos7-1 ~]# systemctl restart mysqld.service
```

### 创建独立的主从复制用户

```
[root@centos7-1 ~]# mysql -u root -p
Enter password: 

mysql> CREATE USER 'repl'@'%' IDENTIFIED BY 'Sujia100.com';
mysql> GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
```

```sql
mysql> FLUSH TABLES WITH READ LOCK;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW MASTER STATUS;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000001 |      595 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)

mysql> UNLOCK TABLES;
Query OK, 0 rows affected (0.00 sec) 
```

## Slave 

### 配置文件

```
[root@centos7-1 ~]# vi /etc/my.cnf

[mysqld]
server-id=2
```

重启 Slave

### 设置复制源

```
mysql> stop slave;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> 
mysql> 
mysql> 
mysql> 
mysql> CHANGE MASTER TO
    -> MASTER_HOST='192.168.0.180',
    -> MASTER_USER='repl',
    -> MASTER_PASSWORD='Sujia100.com',
    -> MASTER_LOG_FILE='mysql-bin.000001',
    -> MASTER_LOG_POS=595;
Query OK, 0 rows affected, 2 warnings (0.02 sec)

mysql> 
mysql> start slave;
```

然后查看 slave 状态，看是否有问题

```
show slave staus
```

几个问题：

1. 如果 Master 防火墙还未开放 mysql 端口，会出现通信错误。
2. 如果 Slave 和 Master 都是虚拟机复制过来的， server-uuid 相同，也会报错，需要在文件 /var/lib/mysql/auto.cnf 修改 server-uuid 。

## 备注

这里的步骤适用于新搭建的 master slave 复制，并不适用于为已经存在数据 master slave 添加 master。 因此我们搭建完成后，为了以后方便的添加新的 slave， 最好保存刚刚开始的 日志文件的坐标，避免需要导出、导入数据的麻烦。

参考：

- http://dev.mysql.com/doc/refman/5.7/en/replication-howto-masterstatus.html
- http://heyiyi.blog.51cto.com/205455/1708255

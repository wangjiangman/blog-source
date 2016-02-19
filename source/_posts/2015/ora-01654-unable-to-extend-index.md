title: "ORA-01654 索引无法通过128扩展, 表空间不足错误"
date: 2015-08-18
tags: [Oracle]
---

今早系统突然报错：ORA-01654,索引无法通过128(在表空间USERS中) 扩展，究其原因是由于表空间不足引起的。查了表空间以及数据文件的使用情况，发现表空间数据文件 /u01/app/oracle/oradata/orcl/users01.dbf 已经达到最大32G了，所以只能对表空间再添加一个数据文件解决（一个表空间可以对应多个数据文件）。

<!--more-->

解决方案：

```sql
alter tablespace USERS add datafile '/u01/app/oracle/oradata/orcl/users02.dbf' size 100M autoextend on next 100M maxsize unlimited;
```

## 数据库文件为什么增长这么快？

这是由于项目前期没有对表空间进行足够的规划和设计，导致所有的表都放在默认表空间 USERS 上，表空间 USERS 默认的对应的数据文件为 
/u01/app/oracle/oradata/orcl/users01.dbf, 因此该数据文件增长很快。

因此在做设计的时候，应该将表根据实际情况分散在不同的表空间上，对不同的表空间的增长和大小做出不同的规划。同时万一其中一个表空间数据文件损坏，不至于影响全局。

## 为什么数据文件最大是 32G ？

Oracle的 rowid 中使用 22 位来代表Block号，这 22 位最多只能代表 2^22-1=4194303 个数据块。因此 db_block_size=8K 的数据文件最大为  8K * 4194304 = 32G.

## 如何给表空间增加数据文件？

```sql
alter tablespace USERS add datafile '/u01/app/oracle/oradata/orcl/users02.dbf' size 100M autoextend on next 100M maxsize unlimited;
```

## 如何查看表空间和数据文件？

```sql
-- 所有表空间
select * from dba_tablespaces;
-- 所有数据文件
select * from dba_data_files;

-- 查看表空间使用情况 1
SELECT a.tablespace_name,
       a.bytes total,
       b.bytes used,
       c.bytes free,
       (b.bytes * 100) / a.bytes "% USED ",
       (c.bytes * 100) / a.bytes "% FREE "
  FROM sys.sm$ts_avail a, sys.sm$ts_used b, sys.sm$ts_free c
 WHERE a.tablespace_name = b.tablespace_name
   AND a.tablespace_name = c.tablespace_name;

-- 查看表空间使用情况 2
SELECT a.tablespace_name "表空间名",
       total "表空间大小",
       free "表空间剩余大小",
       (total - free) "表空间使用大小",
       total / (1024 * 1024 * 1024) "表空间大小(G)",
       free / (1024 * 1024 * 1024) "表空间剩余大小(G)",
       (total - free) / (1024 * 1024 * 1024) "表空间使用大小(G)",
       round((total - free) / total, 4) * 100 "使用率 %"
  FROM (SELECT tablespace_name, SUM(bytes) free
          FROM dba_free_space
         GROUP BY tablespace_name) a,
       (SELECT tablespace_name, SUM(bytes) total
          FROM dba_data_files
         GROUP BY tablespace_name) b
 WHERE a.tablespace_name = b.tablespace_name
```

## Oracle 表空间知识扩充

Oracle 数据库由一个或多个逻辑存储单元组成，这个逻辑存储单元就是表空间 tablespace, 表空间内的逻辑存储单位为段 segment, 段又可以继续划分为数据扩展 extent, 而数据扩展是由一组连续的数据块 datablock 构成。

tablespace 又分为 smallfile tablespace 和 bigfile tablespace; database 默认创建的是 smallfile tablespace; smallfile tablespace 的 db_block_size 为 8K, 这就决定了 smallfile tablespace 的数据文件最大为 32G, 因此smallfile tablespace 需要对应多个数据文件, 这就给维护带来了一定的工作量。 在 bigfile tablespace 中，表空间可以由一个单一的大文件组成，但是所有的数据都集中在一个数据文件中，也有一定的弊端，不符合 I/O 分散的原则，所以 bigfile tablespace 一般需要合适的存储支持。

创建 bigfile tablespace 的语法

```sql
CREATE BIGFILE TABLESPACE bigfile DATAFILE '/u01/app/oracle/oradata/orcl/bigfile.dbf' SIZE 10M AUTOEXTEND OFF;
```


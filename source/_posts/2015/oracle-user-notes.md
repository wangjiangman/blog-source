title: "Oracle 用户相关操作"
date: 2015-09-07
tags: [Oracle]
categories: 技术
---

这里记录一下 Oracle 中用户相关的操作。 <!--more-->

## 创建用户

### 创建用户

```sql
create user username identified by password; 
```

### 修改密码

```sql
alter user username identified by password; 
```

### 删除用户

```sql
drop user username cascade;
```

## 权限

### 连接和资源权限

```sql
grant connect to username;
grant resource to username;
-- 或者
grant connect, resource to username;
```

- connect 是连接权限
- resource 包括
  - CREATE CLUSTER
  - CREATE INDEXTYPE
  - CREATE OPERATOR
  - CREATE PROCEDURE
  - CREATE SEQUENCE
  - CREATE TABLE
  - CREATE TRIGGER
  - CREATE TYPE

### 视图权限

```sql
grant create view to username; 
```

### 调试存储过程权限

```sql
grant debug any procedure, debug connect session to username;
```

### 删除权限

```sql
revoke connect, resource from username; 
```

### 授权给其他用户

```sql
grant select,update on table_name to user2 [with grant option];
```

只有更新某个字段的权限
```sql
grant update(column_name) on table_name to user2 [with grant option];
```

**<code>with grant option</code>**

<code>with grant option</code> 的作用是权限传递，就是使被授权的人，同样可以授权给别人。打个比方，就是你传球给我，我接到球后同样可以把球传给别人。

上面的例子中，如果带了 <code>with grant option</code>, user2 就可以把 select, update 权限授权给其他人。否则不能给其他用户授权.

### 收回权限

```sql
revoke select on table_name from user2;
revoke update on table_name from user2;
```

## 表空间

### 创建表空间

```sql
create tablespace TABLESPACE_NAME 
logging 
datafile '/u01/app/oracle/oradata/orcl/TABLESPACE_FILENAME.dbf'
size 100m 
autoextend on 
next 100m maxsize unlimited 
extent management local; 
```

### 给表空间添加数据文件

```sql
alter tablespace USERS add datafile '/u01/app/oracle/oradata/orcl/users02.dbf' size 100M autoextend on next 100M maxsize unlimited;
```

### 分配表空间给用户

```sql
alert user username default tablespace tablespace_name; 
```

### 修改表的表空间

```sql
alter table table_name move tablespace tablespace_name;
```

### 删除表空间

语法：drop tablespace '表空间名' , 有三个选项：

- INCLUDING CONTENTS: 指删除表空间中的segments
- INCLUDING CONTENTS AND DATAFILES: 指删除segments和datafiles
- CASCADE CONSTRAINTS: 删除所有与该空间相关的完整性约束条件

举例：

```sql
drop tablespace TABLESPACE_NAME including contents and datafiles cascade constraints;  
```

## 查看当前权限

### 查看用户权限

```sql
select * from user_role_privs; 
```

### 查看用户下所有的表 

```sql
select * from user_tables; 
```

### 查看用户序列 

```sql
select * from user_sequences; 
```

### 查看视图的名称 

```sql
select view_name from user_views;
```


# Mysql 安装后的一些基本配置.md


## 设置字符集

设置字符集为 uft8

## 设置表名不区分大小写

Window 下 Mysql 的表名是不区分大小写的，而 Linux 下表名和别名是区分大小写的，为了方便，我们通常也修改为不区分大小写。需要使用 lower_case_table_names 参数设置。

```
vi /etc/my.cnf
[mysqld]
lower_case_table_names=1
```


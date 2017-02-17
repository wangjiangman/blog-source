title: "CentOS 7 安装 mysql 5.7.10"
date: 2016-01-15
tags: [Mysql,Linux,CentOS]
categories: 技术
---

本文记录 CentOS 7 下安装 mysql 5.7.10 及简单的配置步骤。<!--more-->

## 通过 yum 安装 mysql 社区版

```
wget http://repo.mysql.com//mysql57-community-release-el7-7.noarch.rpm
yum -y localinstall mysql57-community-release-el7-7.noarch.rpm 
yum -y install mysql-community-server
```

## 设置服务并启动

```
systemctl enable mysqld
systemctl start mysqld
```

## 修改数据库编码为 utf8

修改 /etc/my.cnf, [mysqld] 分组下添加如下两行

```
character-set-server=utf8
collation_server=utf8_unicode_ci
```

## 修改表名字段不区分大小写

修改 /etc/my.cnf, [mysqld] 分组下添加如下行

```
lower_case_table_names=1
```

## 重启

```
systemctl restart mysqld
```

## 修改 root 密码

1、查看安装时的临时密码 
```
grep 'temporary password' /var/log/mysqld.log
```

2、利用密码登录进去

```
mysql -u root -p
```

3、修改root密码（至少8位，大小写混合，含有数字和特殊字符）

```
alter user 'root'@'localhost' identified by 'MyNewPass4!';
```


参考文档：

- http://dev.mysql.com/doc/refman/5.7/en/linux-installation-yum-repo.html






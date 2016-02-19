MySql
======================

## 1、登陆
```sql
mysql -u root -p 
```
或者

```sql
mysql -hlocalhost -P3306 -uroot -prootpassword
```

## 2、创建用户/授权

```mysql
-- 只允许本地访问
grant all privileges on *.* to USERNAME@'localhost' identified by 'USERPASSWORD' with grant option;
-- 不限制访问IP
grant all privileges on *.* to USERNAME@'%' identified by 'USERPASSWORD' with grant option;
```
### 2.1、修改root密码

```
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpass');
```
```
UPDATE user SET Password = PASSWORD('newpass') WHERE user = 'root';
FLUSH PRIVILEGES;
```
前面两种需要登陆mysql，下面命令不需要登陆进去
```
mysqladmin -u root password "newpass"
mysqladmin -u root password oldpass "newpass"
```

### 2.2、重置root密码

```
mysqld_safe --skip-grant-tables&
mysql -u root mysql
mysql> UPDATE user SET password=PASSWORD("new password") WHERE user='root';
mysql> FLUSH PRIVILEGES;
```

## 3、导入数据

登录进去，选择数据库
```
source xxx.sql
```

或者

```
load data local infile 'D:\data.txt' into table exceltomysql fields terminated by '\t';
```

或者无需登录

```
mysql -h localhost -u root -p mydb2 < e:\mysql\mydb2.sql
```

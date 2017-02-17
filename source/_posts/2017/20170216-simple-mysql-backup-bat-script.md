title: "一个简单的 Mysql 备份 BAT 脚本"
date: 2017-02-16
tags: [Mysql]
categories: 技术
---

这是一个简单的 Windows 下备份 Mysql 的 BAT 脚本，脚本使用 mysqldump 命令来备份一个指定的 Mysql 数据库到一个文件，文件格式为 `%dbname%-yyyyMMddHHmmss.sql`，只保留最近60天的备份。如果想定时执行，在 Windows 中添加**任务计划**即可。<!--more-->

```
@echo off

set hour=%time:~0,2%
if "%time:~0,1%"==" " set hour=0%time:~1,1%
set now=%Date:~0,4%%Date:~5,2%%Date:~8,2%%hour%%Time:~3,2%%Time:~6,2%
echo %now%

set host=xxx.xxx.xxx.xxx
set port=3306
set user=root
set pass=root
set dbname=boke_yydz
set backupfile=E:\backup\db\%dbname%-%now%.sql

E:\backup\mysql-5.7.13-winx64\bin\mysqldump -h%host% -P%port% -u%user% -p%pass% -c --add-drop-table %dbname% > %backupfile%

echo delete files before 60 days

forfiles /p "E:\backup\db" /m *.sql /d -60 /c "cmd /c del @file /f"
```
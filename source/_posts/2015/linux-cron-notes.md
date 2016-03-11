title: "cron 学习笔记"
date: 2015-09-07
tags: [Linux]
categories: 技术
---

cron 是 linux/unix 下的一个计划任务管理工具，相当于 Windows 下的计划任务，可以帮助我们按计划、定时的执行某些指定的任务，本文是在使用过程中做的简单的笔记。

<!--more-->

> 以下操作只在 CentOS 中测试通过

## 基本命令

cron 作为系统服务存在，可使用如下命令进行启动、停止、重启、重新加载配置等。

- <code>service crond start</code> 启动
- <code>service crond stop</code> 停止
- <code>service crond restart</code> 重启
- <code>service crond reload</code> 重新加载配置

## crontab 命令

<code>crontab</code> 命令是用来直接编辑 cron 任务的的命令，可以直接编辑当前用户下的 cron 任务，主要参数如下

- <code>crontab -l</code> 列出当前用户的计划任务
- <code>crontab -e</code> 编辑当前用户的计划任务
- <code>crontab -r</code> 删除当前用户的计划任务
- <code>crontab -u USERNAME -[ler]</code> 查看/编辑/删除某用户下的计划任务，一般root用户使用

每次使用命令 <code>crontab -e</code> 编辑完后，会在 <code>/var/spool/cron</code> 下生成一个文件名为用户名的文件，此文件不能直接编辑。使用 <code>crontab -e</code> 编辑后，不需要重启或者重新加载 cron 服务，因为 cron 每分钟会都会扫描 <code>/var/spool/cron</code> ，检查文件是否有改动 。

## /etc/crontab 文件

除了通过 <code>crontab</code> 命令编辑当前用户的计划任务，还可以通过编辑 <code>/etc/crontab</code> 文件来设定系统级的计划任务。

## 计划任务定义

在 <code>/etc/crontab</code> 中有对如何定义计划任务的详细说明，如下：

```shell
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
```

总共有6列，通过空格分隔。前5列用来定义任务执行的时间触发条件，最后一部分为要执行的命令。这里做一个简要的说明：

- 第1列：分；0-59；* 表示每分钟，*/N 表示每N分钟
- 第2列：时；0-23；* 表示每小时，*/N 表示每N小时
- 第3列：天；1-31；* 表示每天，*/N 表示每天
- 第4列：月；1-12；* 表示每月
- 第5列：星期；0-6；0 表示周天；或者 sun,mon,tue,wed,thu,fri,sat 表示
- 第6列：要执行的命令

## 举例

**凌晨3点整进行 oracle 备份**

```shell
0 3 * * * /home/oracle/backup/backup.sh >> /home/oracle/backup/backup.log 2>&1
```

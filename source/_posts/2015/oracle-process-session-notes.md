title: "Oracle 进程、连接相关"
date: 2015-10-23
tags: [Oracle]
---

默认情况下，Oracle 允许的最大进程数为 150，这往往不能满足实际环境的要求，因此我们需要修改进程数和会话数的配置。<!--more-->

**显示进程、会话数相关配置**

```sql
show parameter processes;
show parameter sessions;
```

**修改会话进程数相关配置**

```sql
alter system set processes=100 scope=spfile;
alter system set sessions=115 scope=spfile;
```

需要遵循的公式: sessions = 1.1*process + 5

设置之后必须重启数据库实例才能生效。

**Oracle 11g**

oracle 11g 后已变的不同, 公式发生了变化为: sessions = (1.5 * PROCESSES) + 22, 并适用以下规则: 若在参数文件中指定sessions的值小于该默认推算值，数据库实例将自动调整至该推算值，设置的值大于该推算值时，oracle将会考虑使用我们所设置的值，简言之，oracle会在我们设置的值和推算值之间取较大者。一般情况下，我们只需要修改 processes 的值就可以了。

**查看当前会话、进程的信息**

```sql
select count(*) from v$process;  --查询进程数
select count(*) from v$session;  --查询会话数
select count(*) from v$session where status='ACTIVE';  --查询激活状态的会话数
```


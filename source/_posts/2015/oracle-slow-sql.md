title: "oracle 查看执行最慢 sql"
date: 2015-08-21
tags: [Oracle]
categories: 技术
---

**查询执行最慢的sql**

```sql
select *
  from (select sa.SQL_TEXT,
               sa.SQL_FULLTEXT,
               sa.EXECUTIONS "执行次数",
               round(sa.ELAPSED_TIME / 1000000, 2) "总执行时间",
               round(sa.ELAPSED_TIME / 1000000 / sa.EXECUTIONS, 2) "平均执行时间",
               sa.COMMAND_TYPE,
               sa.PARSING_USER_ID "用户ID",
               u.username "用户名",
               sa.HASH_VALUE
          from v$sqlarea sa
          left join all_users u
            on sa.PARSING_USER_ID = u.user_id
         where sa.EXECUTIONS > 0
         order by (sa.ELAPSED_TIME / sa.EXECUTIONS) desc)
 where rownum <= 50;
```

<!--more-->

**查询次数最多的 sql**

```sql
select *
  from (select s.SQL_TEXT,
               s.EXECUTIONS "执行次数",
               s.PARSING_USER_ID "用户名",
               rank() over(order by EXECUTIONS desc) EXEC_RANK
          from v$sql s
          left join all_users u
            on u.USER_ID = s.PARSING_USER_ID) t
 where exec_rank <= 100;
```

参考资料

- http://loversoul.blog.163.com/blog/static/161447251201183002327124
- http://blog.csdn.net/yabingshi_tech/article/details/44101069
- http://blog.itpub.net/12361284/viewspace-151830/

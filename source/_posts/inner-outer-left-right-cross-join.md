title: "内连接、左联接、右连接、交叉连接"
date: 2013-01-24
tags: sql & db
categories: 技术
---

当多表存在关联时，一般使用连接进行关联查询。连接一般分为：内连接、外连接， 这里做一个总结。

**内连接(inner join)**，返回的是2个表中满足条件的记录的组合。
**左外连接(left join/left outer join)**，以左表的记录为基础，右表符合条件便组合，不符合条件则以空字段留空。
**右外连接(right join/right outer join)**，以右表的记录为基础，左表符合条件便组合，不符合条件则以空字段留空。
**全外连接(full join/full outer join)**，相当于左外连接和右外连接的 union。<!--more-->

为了更容易的理解，我们建立了2个表：学生表（student）和班级表（class），建表脚本如下(postgre数据库)：

``` sql
CREATE TABLE student
(
  id integer NOT NULL,
  name character varying(100),
  class_no integer,
  CONSTRAINT &quot;PK&quot; PRIMARY KEY (id )
);

CREATE TABLE class
(
  no integer NOT NULL,
  name character varying(100),
  CONSTRAINT &quot;PK_CLASS&quot; PRIMARY KEY (no )
);
```

两个表的数据分别如下图所示：

![](/images/table-student.jpg)

![](/images/table-class.jpg)

内连接

``` sql
select * from student s inner join class c on s.class_no = c.no;
```

通常会写作

``` sql
select * from student s, class c where s.class_no = c.no;
```

执行结果如下

![](/images/result1.jpg)

左外连接/左连接

``` sql
select * from student s left  join class c on s.class_no = c.no;
```

![](/images/result2.jpg)

右外连接/右连接

``` sql
select * from student s right join class c on s.class_no = c.no;
```

![](/images/result3.jpg)

全外连接/全连接
``` sql
select * from student s full join class c on s.class_no = c.no;
```

![](/images/result4.jpg)

另外还有一种叫交叉连接，他返回2个记录集的笛卡尔积。

``` sql
select * from student s, class c;
```

或者

``` sql
select * from student s cross join class c;
```

结果如下：

![](/images/result5.jpg)

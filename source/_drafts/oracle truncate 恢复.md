

使用prm来恢复 truncate 的数据

http://www.parnassusdata.com/zh-hans/node/52

prm 的几个license

1、
DB NAME : SHITAN
Licence key: 0B824760DF227C26FE77A6B99D0287BFA65A9C1C8A6E6525F2BEE362E0D5C4F1ED2BC8897811A8FA671BF37EBC06D397F1329CD86BF0547677B147E3AA218CF2AC410FCBD94D662061668938AE4D4039

2、
DB name: PARNASSU
Licence key: 0B824760DF227C26FE77A6B99D0287BFDE221374DFF9E9D688F9333B1B9518223AD1E3ABC324D5681EF857BE24577A3C2CD6D251CEBA930DAC5D3C08736853C3B5BB92490FFA33CA29E85830AB7F29C4

# oracle 数据库重命名

shutdown immediate;
startup mount;

nid target=system/oracle DBNAME=orcl SETNAME=Y

startup nomount;

alter system set db_name='ORCL' scope=spfile;

startup force;


show parameter db_name
select name from v$database;


# sqlldr 用法 

sqlldr userid=username/password control=xxx.ctl

如果为utf8编码，导入后有乱码，则需要在 ctl 文件里 加入 
CHARACTERSET AL32UTF8 指令。（第二行）

冷备份和热备份

- http://soulful.blog.51cto.com/468033/367942/
- http://candon123.blog.51cto.com/704299/249801
- http://blog.csdn.net/leshami/article/details/5816012

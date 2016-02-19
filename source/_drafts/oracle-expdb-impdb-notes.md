
expdb 只能存放于 directory 对应的实际目录中。

首先创建 directory 

mkdir /home/oracle/dump

create directory BACKUP_DUMP_DIR as '/home/oracle/dump';

grant read, write on directory BACKUP_DUMP_DIR to username;

查询 directory

select * from dba_directories;

expdp xfy/xfy456@orcl directory=BACKUP_DUMP_DIR dumpfile=xfy.dmp schemas=xfy

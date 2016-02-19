title: "centos 6.5 安装 oracle 11gR2 及配置Oracle自动启动"
date: 2015-03-29
tags: [Linux, Oracle]
---

CentOS 下安装 Oracle 是一件比较麻烦的事情，这里记录了在 centos 6.5 下安装 oracle 11gR2的前的配置步骤，以及安装完成后，如何设置为随系统自动启动。配置完成后，启动图形化安装，没有什么可说的，本文就没有一一截图，掠过了。<!-- more -->

> 以下命令使用 root 用户操作

## 安装依赖包

主要思路为配置 oracle yum 仓库，直接使用 yum 命令来安装依赖包，避免手工逐个的安装依赖包。

### 配置 yum 仓库以下载 orcale 依赖包

注意这里是CentOS 6.X 的 oracle 依赖仓库，其他版本不同的。

``` bash
cd /etc/yum.repos.d
wget https://public-yum.oracle.com/public-yum-ol6.repo --no-check-certificate
```

### 导入 GPG Keys

``` bash
wget https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6 -O /etc/pki/rpm-gpg/RPM-GPG-KEY-oracle --no-check-certificate
```

### 安装 oracle 依赖 
``` bash
yum install oracle-rdbms-server-11gR2-preinstall
```

## 设置 hostname

打开文件 /etc/sysconfig/network，并修改 HOSTNAME。

``` bash
vi /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=oracle.buzheng.org
```

打开 /etc/host， 添加服务器本机IP地址 指向到 HOSTNAME

``` bash
vi /etc/hosts
192.168.1.15 oracle.buzheng.org oracle  
```

## oracle 用户设置

当安装好 oracle 依赖之后， oracle 用户已经创建好了，不需要手工创建 oracle 用户和相关的用户组。

### 修改 oracle 用户密码

``` bash
[root@oracle ~]# passwd oracle
更改用户 oracle 的密码 。
新的 密码：
重新输入新的 密码：
passwd： 所有的身份验证令牌已经成功更新。
```

### 修改系统限制 

在文件 /etc/security/limits.d/90-nproc.conf 中添加如下的最后一行

``` bash
[root@oracle ~]# vi /etc/security/limits.d/90-nproc.conf
# Default limit for number of user's processes to prevent
# accidental fork bombs.
# See rhbz #432903 for reasoning.

*          soft    nproc     1024
root       soft    nproc     unlimited
*          -       nproc     16384
```

### 修改 SELinux 为 permissive 模式

修改文件 /etc/selinux/config 如下所示

``` bash
[root@oracle ~]# vi /etc/selinux/config

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=permissive
# SELINUXTYPE= can take one of these two values:
#     targeted - Targeted processes are protected,
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

## 重启机器

``` bash
[root@oracle]# reboot
```

## 创建 oracle 安装目录，并授权

``` bash
[root@oracle]# mkdir -p /u01/app/oracle/product/11.2.0/dbhome_1
[root@oracle]# chown -R oracle:oinstall /u01
[root@oracle]# chmod -R 775 /u01
```

授权 oracle 用户能够访问 X Server

``` bash
[root@oracle]# xhost +  # 要在主机上运行，不能是远程 shell
```

> 以下命令使用 oracle 用户操作

## 配置 oracle 安装环境变量

切换到 oracle 用户, 编辑 .bash_profile 文件, 如下所示, 添加环境配置。

``` bash
[root@oracle]# su oracle
[oracle@oracle]# vi .bash_profile

# Oracle Settings
TMP=/tmp; export TMP
TMPDIR=$TMP; export TMPDIR

ORACLE_HOSTNAME=oracle.buzheng.org; export ORACLE_HOSTNAME
ORACLE_UNQNAME=orcl; export ORACLE_UNQNAME
ORACLE_BASE=/u01/app/oracle; export ORACLE_BASE
ORACLE_HOME=$ORACLE_BASE/product/11.2.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=orcl; export ORACLE_SID
PATH=/usr/sbin:$PATH; export PATH
PATH=$ORACLE_HOME/bin:$PATH; export PATH

LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib; export LD_LIBRARY_PATH
CLASSPATH=$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib; export CLASSPATH export PATH
```

## 下载 oracle 安装文件，解压缩后安装

###  下载 oracle

打开 oracle 下载页面，http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html , 下载下面两个文件到 /home/oracle

``` bash
linux.x64_11gR2_database_1of2.zip
linux.x64_11gR2_database_2of2.zip
```

### 解压缩文件并运行

``` bash
unzip linux.x64_11gR2_database_1of2.zip
unzip linux.x64_11gR2_database_2of2.zip

cd database
./runInstaller
```

若为中文系统，安装界面会出现中文乱码，需要先将环境变为英文，在安装

``` bash
export LANG=en_US
./runInstaller
```

没有截图, 安装步骤大致包括以下几步:

1. Create and configure a database
2. Server Class
3. Single instance database installation
4. Advanced install
5. Language: English
6. Enterprise Edition
7. database type: General Purpose / Transaction Processing
8. Memory: Enable Automatic Memory Management
9. Character sets: Use Unicode(AL32UTF8)

图性界面安装最后会提示以ROOT 执行下面两个脚本

``` bash
[root@oracle ~]# /u01/app/oraInventory/orainstRoot.sh 
Changing permissions of /u01/app/oraInventory.
Adding read,write permissions for group.
Removing read,write,execute permissions for world.

Changing groupname of /u01/app/oraInventory to oinstall.
The execution of the script is complete.
```

``` bash
[root@oracle ~]# /u01/app/oracle/product/11.2.0/dbhome_1/root.sh 
Running Oracle 11g root.sh script...

The following environment variables are set as:
    ORACLE_OWNER= oracle
    ORACLE_HOME=  /u01/app/oracle/product/11.2.0/dbhome_1

Enter the full pathname of the local bin directory: [/usr/local/bin]: 
   Copying dbhome to /usr/local/bin ...
   Copying oraenv to /usr/local/bin ...
   Copying coraenv to /usr/local/bin ...


Creating /etc/oratab file...
Entries will be added to the /etc/oratab file as needed by
Database Configuration Assistant when a database is created
Finished running generic part of root.sh script.
Now product-specific root actions will be performed.
Finished product-specific root actions.
```


### 出现的问题

1, 当环境变量中的 ORACLE_SID 与 安装时实际配置不一致时，会导致 startup 出错，出错信息如下：

``` bash
ORA-01078: failure in processing system parameters
LRM-00109: could not open parameter file '/u01/app/oracle/product/11.2.0/dbhome_1/dbs/initDB11G.ora'
```

2, 如果远程客户端连接是报错：ora-12514 tns 监听程序当前无法识别连接描述符中请求的服务，则需要配置 listener.ora，添加如下节点：

```
SID_LIST_LISTENER = 
  (SID_LIST=    
    (SID_DESC =
      (SID_NAME = orcl)
      (ORACLE_HOME = /u01/app/oracle/product/11.2.0/dbhome_1)
    )
  )

```


好了，到此已经安装完成，通过访问 web 管理界面可验证。下面步骤也很重要，请继续。

> 以下命令使用 root 用户操作

## 配置防火墙规则，开放侦听端口

将 1521（你配置的 oracle 侦听端口） 端口添加到防火墙规则，以便远程用户能顺利的访问到 oracle 服务。

``` bash 
iptables -I INPUT 4 -m state --state NEW -p tcp --dport 1521 -j ACCEPT
service iptables save
service iptables reload
```

## 设置 oracle 自动启动

完成 oracle 安装后，我们可以通过命令启动停止数据库，但是当机器重启时并不能随机器启动，这也给我们带来了一些不便，下面记录了，如何将 oralce 加入服务中，并随机机器启动。

###  修改配置文件 /etc/oratab

修改 /etc/oratab 的最后一行的最后一个字符，将 N 修改为 Y。此举主要是为了使 oracle 的 dbstart 命令能在系统启动时能运行。如下所示：

``` bash
[root@oracle ~]# vi /etc/oratab
#



# This file is used by ORACLE utilities.  It is created by root.sh
# and updated by the Database Configuration Assistant when creating
# a database.

# A colon, ':', is used as the field terminator.  A new line terminates
# the entry.  Lines beginning with a pound sign, '#', are comments.
#
# Entries are of the form:
#   $ORACLE_SID:$ORACLE_HOME:<N|Y>:
#
# The first and second fields are the system identifier and home
# directory of the database respectively.  The third filed indicates
# to the dbstart utility that the database should , "Y", or should not,
# "N", be brought up at system boot time.
#
# Multiple entries with the same $ORACLE_SID are not allowed.
#
#
orcl:/u01/app/oracle/product/11.2.0/dbhome_1:Y
```

### 创建 /etc/init.d/oracle 

在 /etc/init.d/下创建文件 oracle，此为 oracle 自启动脚本。内容如下：

``` bash
#!/bin/sh
# chkconfig: 345 61 61
# description: Oracle 11g R2 AutoRun Servimces
# /etc/init.d/oracle
#
# Run-level Startup script for the Oracle Instance, Listener, and
# Web Interface

ORACLE_HOME="/u01/app/oracle/product/11.2.0/dbhome_1"
ORA_OWNR="oracle"

# if the executables do not exist -- display error

if [ ! -f "$ORACLE_HOME/bin/dbstart" ]
then
echo "Oracle startup: cannot start"
exit 1
fi

# parameter: start, stop, restart

case "$1" in
start)
# Oracle listener and instance startup
su $ORA_OWNR -s /bin/bash $ORACLE_HOME/bin/dbstart $ORACLE_HOME
echo "Oracle Start Succesful!OK."
;;
stop)
# Oracle listener and instance shutdown
su $ORA_OWNR -s /bin/bash $ORACLE_HOME/bin/dbshut $ORACLE_HOME
echo "Oracle Stop Succesful!OK."
;;
restart)
$0 stop
$0 start
;;
*)
echo $"Usage: `basename $0` {start|stop|restart}"
exit 1
esac
exit 0
```

### 赋予启动脚本执行权限并链接好：

``` bash
# chmod 750 /etc/init.d/oracle
# ln -s /etc/init.d/oracle /etc/rc1.d/K61oracle
# ln -s /etc/init.d/oracle /etc/rc3.d/S61oracle
```

启动脚本设置好了之后，可以使用 service 命令来启动停止 oracle 了，这时候 oracle 已经是系统服务了。

``` bash
service oracle start
service oracle stop
service oracle restart
```


### 设置 oracle 服务为自启动

``` bash
# chkconfig --add oracle
# chkconfig --level 345 oracle on
```

好了，重启机器试试吧。

Done!

title: "CentOS 7 安装 redis 3.0.6 集群"
date: 2016-01-30
tags: [CentOS, Redis, Linux]
categories: 技术
---

本文记录 CentOS 7 下 安装 redis 3.0.6 并配置集群的过程。 <!--more-->

## 安装依赖

```
[root@centos7-1 ~]# yum -y install gcc openssl-devel libyaml-devel libffi-devel readline-devel zlib-devel gdbm-devel ncurses-devel gcc-c++ automake autoconf
```

## 安装 redis

```
[root@centos7-1 ~]# wget http://download.redis.io/releases/redis-3.0.6.tar.gz
[root@centos7-1 ~]# tar xvf redis-3.0.6.tar.gz
[root@centos7-1 ~]# cd redis-3.0.6/
[root@centos7-1 redis-3.0.6]# make MALLOC=libc
[root@centos7-1 redis-3.0.6]# make install
```

## 启动 server

通过命令 redis-server 来启动 redis server。通过下面的输出信息可以看出这次启动并没有指定配置文件，可以使用命令 <code>redis-server /path/to/redis.conf</code> 来指定具体的配置文件启动。

```
[root@centos7-1 redis-3.0.6]# redis-server 
4435:C 25 Jan 11:40:48.816 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
4435:M 25 Jan 11:40:48.817 * Increased maximum number of open files to 10032 (it was originally set to 1024).
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 3.0.6 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 4435
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

4435:M 25 Jan 11:40:48.817 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
4435:M 25 Jan 11:40:48.817 # Server started, Redis version 3.0.6
4435:M 25 Jan 11:40:48.817 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
4435:M 25 Jan 11:40:48.817 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
4435:M 25 Jan 11:40:48.817 * DB loaded from disk: 0.000 seconds
4435:M 25 Jan 11:40:48.817 * The server is now ready to accept connections on port 6379
```

到此，单节点的 Redis server 已经安装完毕，以下进入配置和安装集群的过程。

## 创建集群中的6个节点

### 集群配置文件

集群相关的配置文件主要修改 redis.conf 中的一下几个地方，因为都部署在一个服务器上，每个节点的端口不同。

```
[root@centos7-1 redis-3.0.6]$ vi redis.conf 
#修改以下地方
port 7000
cluster-enabled yes
cluster-config-file nodes-7000.conf
cluster-node-timeout 5000
appendonly yes
```

### 每个节点的配置文件

创建6个文件夹，分别表示一个 redis 节点，文件夹使用 redis 节点的端口号命名，文件里存放每个节点的配置文件。

```
[root@centos7-1 redis-3.0.6]# mkdir /usr/local/redis-cluster
[root@centos7-1 redis-3.0.6]# cd /usr/local/redis-cluster
[root@centos7-1 redis-cluster]# mkdir 7000  7001 7002 7003 7004 7005
```

拷贝 redis.conf 拷贝到6个文件夹中

```
[root@centos7-1 redis-cluster]# cp ~/redis-3.0.6/redis.conf 7000
[root@centos7-1 redis-cluster]# cp ~/redis-3.0.6/redis.conf 7001
[root@centos7-1 redis-cluster]# cp ~/redis-3.0.6/redis.conf 7002
[root@centos7-1 redis-cluster]# cp ~/redis-3.0.6/redis.conf 7003
[root@centos7-1 redis-cluster]# cp ~/redis-3.0.6/redis.conf 7004
[root@centos7-1 redis-cluster]# cp ~/redis-3.0.6/redis.conf 7005
```

并修改每个配置中的内容为各自节点的端口，注意 port 和 cluster-config-file 的值必须唯一。


### 分别启动每个节点

```
[root@centos7-1 ~]# redis-server /usr/local/redis-cluster/7000/redis.conf
[root@centos7-1 ~]# redis-server /usr/local/redis-cluster/7001/redis.conf
[root@centos7-1 ~]# redis-server /usr/local/redis-cluster/7002/redis.conf
[root@centos7-1 ~]# redis-server /usr/local/redis-cluster/7003/redis.conf
[root@centos7-1 ~]# redis-server /usr/local/redis-cluster/7004/redis.conf
[root@centos7-1 ~]# redis-server /usr/local/redis-cluster/7005/redis.conf
```

启动完成后查看进程

```
[root@centos7-1 ~]# ps -ef | grep redis
root      4704  2177  0 12:12 pts/0    00:00:07 redis-server *:7000 [cluster]
root      4707  4599  0 12:12 pts/1    00:00:07 redis-server *:7001 [cluster]
root      4710  4638  0 12:12 pts/2    00:00:07 redis-server *:7002 [cluster]
root      4752  4717  0 12:12 pts/3    00:00:07 redis-server *:7003 [cluster]
root      4788  4759  0 12:12 pts/4    00:00:07 redis-server *:7004 [cluster]
root      4824  4795  0 12:13 pts/5    00:00:07 redis-server *:7005 [cluster]
root      9018  8984  0 14:08 pts/6    00:00:00 grep --color=auto redis
```

## 建立 Redis 集群

上面的步骤分别创建了6个节点，并已经启动，这里要把这6个节点加入到一个集群里面。redis 已经为我们提供了集群操作的脚本 redis-trib.rb , 操作起来很简单， 继续。

### 安装 ruby

由于集群操作需要用到 ruby 脚本 redis-trib.rb , 所以要安装 ruby 和 rubygems

```
[root@centos7-1 ~]# yum -y install ruby rubygems
[root@centos7-1 ~]# gem install redis --version 3.0.6
Fetching: redis-3.0.6.gem (100%)
Successfully installed redis-3.0.6
Parsing documentation for redis-3.0.6
Installing ri documentation for redis-3.0.6
1 gem installed
```

redis-trib.rb 是一个 ruby 脚本工具，用来建立和管理 redis 集群，因为使用比较频繁，所以拷贝脚本 redis-trib.rb 到 /usr/local/bin/

```
[root@centos7-1 redis-3.0.6]$ cp src/redis-trib.rb /usr/local/bin/
```

### 创建集群

```
[root@centos7-1 ~]# redis-trib.rb create --replicas 1 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005
>>> Creating cluster
>>> Performing hash slots allocation on 6 nodes...
Using 3 masters:
127.0.0.1:7000
127.0.0.1:7001
127.0.0.1:7002
Adding replica 127.0.0.1:7003 to 127.0.0.1:7000
Adding replica 127.0.0.1:7004 to 127.0.0.1:7001
Adding replica 127.0.0.1:7005 to 127.0.0.1:7002
M: d900d3667196121f95bdbd68ca2317926a6950bc 127.0.0.1:7000
   slots:0-5460 (5461 slots) master
M: b662853288cc89a3b78f70120d57c0a34f6a91d5 127.0.0.1:7001
   slots:5461-10922 (5462 slots) master
M: 6dc5ee101c2432ec4c1d934d54a6a3565116bbff 127.0.0.1:7002
   slots:10923-16383 (5461 slots) master
S: d18e50ac6aff2f612fac43da74a79288aa574c87 127.0.0.1:7003
   replicates d900d3667196121f95bdbd68ca2317926a6950bc
S: 85f6455e14d03ebaefeca3dbe3c78c92f2f76ead 127.0.0.1:7004
   replicates b662853288cc89a3b78f70120d57c0a34f6a91d5
S: 89d792acda7f2c99c95b0d3947572647cf9edc67 127.0.0.1:7005
   replicates 6dc5ee101c2432ec4c1d934d54a6a3565116bbff
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join..
>>> Performing Cluster Check (using node 127.0.0.1:7000)
M: d900d3667196121f95bdbd68ca2317926a6950bc 127.0.0.1:7000
   slots:0-5460 (5461 slots) master
M: b662853288cc89a3b78f70120d57c0a34f6a91d5 127.0.0.1:7001
   slots:5461-10922 (5462 slots) master
M: 6dc5ee101c2432ec4c1d934d54a6a3565116bbff 127.0.0.1:7002
   slots:10923-16383 (5461 slots) master
M: d18e50ac6aff2f612fac43da74a79288aa574c87 127.0.0.1:7003
   slots: (0 slots) master
   replicates d900d3667196121f95bdbd68ca2317926a6950bc
M: 85f6455e14d03ebaefeca3dbe3c78c92f2f76ead 127.0.0.1:7004
   slots: (0 slots) master
   replicates b662853288cc89a3b78f70120d57c0a34f6a91d5
M: 89d792acda7f2c99c95b0d3947572647cf9edc67 127.0.0.1:7005
   slots: (0 slots) master
   replicates 6dc5ee101c2432ec4c1d934d54a6a3565116bbff
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

以上 create 命令创建了集群，选项 --replicas 1 表示每个 Master 都需要一个 Slave。所以结果是集群中创建了3个 Master 节点 和 3个 Slave 节点。 

上面信息中 <code>M</code> 表示 Master 节点， <code>S</code> 表示 Slave 节点。由此可以看出三组 Master 和 Slave 关系如下：

```
M: d900d3667196121f95bdbd68ca2317926a6950bc -> S: d18e50ac6aff2f612fac43da74a79288aa574c87
M: 6dc5ee101c2432ec4c1d934d54a6a3565116bbff -> S: 89d792acda7f2c99c95b0d3947572647cf9edc67
M: b662853288cc89a3b78f70120d57c0a34f6a91d5 -> S: 85f6455e14d03ebaefeca3dbe3c78c92f2f76ead
```

### 检测集群的状态

```
[root@centos7-1 ~]# redis-trib.rb check 127.0.0.1:7000
>>> Performing Cluster Check (using node 127.0.0.1:7000)
M: d900d3667196121f95bdbd68ca2317926a6950bc 127.0.0.1:7000
   slots:0-5460 (5461 slots) master
   1 additional replica(s)
M: 6dc5ee101c2432ec4c1d934d54a6a3565116bbff 127.0.0.1:7002
   slots:10923-16383 (5461 slots) master
   1 additional replica(s)
S: 89d792acda7f2c99c95b0d3947572647cf9edc67 127.0.0.1:7005
   slots: (0 slots) slave
   replicates 6dc5ee101c2432ec4c1d934d54a6a3565116bbff
S: d18e50ac6aff2f612fac43da74a79288aa574c87 127.0.0.1:7003
   slots: (0 slots) slave
   replicates d900d3667196121f95bdbd68ca2317926a6950bc
S: 85f6455e14d03ebaefeca3dbe3c78c92f2f76ead 127.0.0.1:7004
   slots: (0 slots) slave
   replicates b662853288cc89a3b78f70120d57c0a34f6a91d5
M: b662853288cc89a3b78f70120d57c0a34f6a91d5 127.0.0.1:7001
   slots:5461-10922 (5462 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```


### 测试集群

利用 redis-cli -c -p 端口号 连接集群 

```
[root@centos7-1 ~]# redis-cli -c -p 7000
127.0.0.1:7000> get hello
(nil)
127.0.0.1:7000> set hello "hello world"
OK
127.0.0.1:7000> get hello
"hello world"
127.0.0.1:7000> get name 
-> Redirected to slot [5798] located at 127.0.0.1:7001
(nil)
127.0.0.1:7001> set name "redis server"
OK
127.0.0.1:7001> get name
"redis server"
```

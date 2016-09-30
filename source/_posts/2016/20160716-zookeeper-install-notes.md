title: "ZooKeeper 集群安装笔记"
date: 2016-07-16
tags: [ZooKeeper, 分布式]
categories: 技术
---

本文回顾一下以前学习 zookeeper 的笔记，简单记录下 zookeeper 安装和配置步骤，以及简单的操作命令。 

<!--more-->

## 安装 Java 环境

zookeeper 需要 Java 环境的支持，所以先要有 java 环境，刚好以前写过一篇如何安装java的笔记，参看：https://buzheng.org/centos-65-install-oracle-jdk-8.html

## 创建用户 

为了方便演示，所有的操作都在 zookeeper 用户下进行。

```shell
useradd zookeeper
```

## 下载 & 安装

```shell
wget http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.4.8/zookeeper-3.4.8.tar.gz
```

下载完成后解压到 ~/apps/zookeeper-3.4.8

## 配置集群

这里安装是 zookeeper 集群， 由于服务器有限，多个节点安装在一台服务器上，每个节点通过端口来区分.

### 创建节点目录

```shell
mkdir /home/zookeeper/nodes/zk1
mkdir /home/zookeeper/nodes/zk2
mkdir /home/zookeeper/nodes/zk3
```

### 为节点创建 myid 文件

myid 文件用来存储本节点服务器ID，这个ID与后面配置文件中 server.ID 必须保持一致。

```shell
echo "1" > /home/zookeeper/nodes/zk1/myid
echo "2" > /home/zookeeper/nodes/zk2/myid
echo "3" > /home/zookeeper/nodes/zk3/myid
```

### 配置文件

**节点1**

节点1的对外服务端口为 2181。

集群的配置格式为：server.ID:NODE_HOST:port1:port2 , 说明如下：
- ID 与每个节点 myid 中的配置必须保持一致
- NODE_HOST 每个节点的主机地址
- port1 为 follower 节点连接到 leader 节点的端口号
- port2 为 leadership 选举的端口号

```
vi /home/zookeeper/nodes/zk1/zoo.cfg
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/home/zookeeper/nodes/zk1
clientPort=2181

server.1=192.168.0.211:2888:3888
server.2=192.168.0.211:2889:3889
server.3=192.168.0.211:2890:3890
```

**节点2**

```
vi /home/zookeeper/nodes/zk2/zoo.cfg
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/home/zookeeper/nodes/zk2
clientPort=2182

server.1=192.168.0.211:2888:3888
server.2=192.168.0.211:2889:3889
server.3=192.168.0.211:2890:3890
```

**节点2**

```
vi /home/zookeeper/nodes/zk3/zoo.cfg
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/home/zookeeper/nodes/zk3
clientPort=2183

server.1=192.168.0.211:2888:3888
server.2=192.168.0.211:2889:3889
server.3=192.168.0.211:2890:3890
```

## 操作命令

操作命令位于 bin目录下： /home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh 

命令用法： zkServer.sh 操作选项 [配置文件] , 配置文件会默认使用安装目录下 conf 下的 zoo.cfg 

关于操作选项，有下面几个选项

- start 后台启动服务器
- start-foreground 前台启动服务器，如果服务器不正常的时候，可以前台启用来排错
- stop 停止服务器
- restart 重新启动服务器
- status 查看状态
- upgrade 升级

### 启动

以下命令逐个启动每个节点

```shell
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh start /home/zookeeper/nodes/zk1/zoo.cfg
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh start /home/zookeeper/nodes/zk2/zoo.cfg
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh start /home/zookeeper/nodes/zk3/zoo.cfg
```

输入如下：

```shell
[zookeeper@localhost ~]$ /home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh start /home/zookeeper/nodes/zk1/zoo.cfg
ZooKeeper JMX enabled by default
Using config: /home/zookeeper/nodes/zk1/zoo.cfg
Starting zookeeper ... STARTED
[zookeeper@localhost ~]$ /home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh start /home/zookeeper/nodes/zk2/zoo.cfg
ZooKeeper JMX enabled by default
Using config: /home/zookeeper/nodes/zk2/zoo.cfg
Starting zookeeper ... STARTED
[zookeeper@localhost ~]$ /home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh start /home/zookeeper/nodes/zk3/zoo.cfg
ZooKeeper JMX enabled by default
Using config: /home/zookeeper/nodes/zk3/zoo.cfg
Starting zookeeper ... STARTED
```

注意 STARTED 是表示启动成功了，有时候启动成功了，并不表示各个节点之间的集群通信没有问题，如果需要排查错误，请使用 start-foreground 选项，这样就能一目了然的发现问题。

### 查看状态

```shell
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh status /home/zookeeper/nodes/zk1/zoo.cfg
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh status /home/zookeeper/nodes/zk2/zoo.cfg
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh status /home/zookeeper/nodes/zk3/zoo.cfg
```

输出如下：

```shell
[zookeeper@localhost ~]$ /home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh status /home/zookeeper/nodes/zk1/zoo.cfg
okeeper/nodes/zk3/zoo.cfg
ZooKeeper JMX enabled by default
Using config: /home/zookeeper/nodes/zk1/zoo.cfg
Mode: follower
[zookeeper@localhost ~]$ /home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh status /home/zookeeper/nodes/zk2/zoo.cfg
ZooKeeper JMX enabled by default
Using config: /home/zookeeper/nodes/zk2/zoo.cfg
Mode: leader
[zookeeper@localhost ~]$ /home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh status /home/zookeeper/nodes/zk3/zoo.cfg
ZooKeeper JMX enabled by default
Using config: /home/zookeeper/nodes/zk3/zoo.cfg
Mode: follower
```

从这里看出，节点2被选为leader。

### 停止

```
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh stop /home/zookeeper/nodes/zk1/zoo.cfg
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh stop /home/zookeeper/nodes/zk2/zoo.cfg
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh stop /home/zookeeper/nodes/zk3/zoo.cfg
```

## 开通端口

为了对外提供服务，别忘了把端口好都加入到防火墙中

```shell
iptables -I INPUT 5 -m state --state NEW,ESTABLISHED -p tcp --dport 2181 -j ACCEPT
iptables -I INPUT 6 -m state --state NEW,ESTABLISHED -p tcp --dport 2182 -j ACCEPT
iptables -I INPUT 7 -m state --state NEW,ESTABLISHED -p tcp --dport 2183 -j ACCEPT
iptables -I INPUT 8 -m state --state NEW,ESTABLISHED -p tcp --dport 2888 -j ACCEPT
iptables -I INPUT 9 -m state --state NEW,ESTABLISHED -p tcp --dport 3888 -j ACCEPT
iptables -I INPUT 10 -m state --state NEW,ESTABLISHED -p tcp --dport 2889 -j ACCEPT
iptables -I INPUT 11 -m state --state NEW,ESTABLISHED -p tcp --dport 3889 -j ACCEPT
iptables -I INPUT 12 -m state --state NEW,ESTABLISHED -p tcp --dport 2890 -j ACCEPT
iptables -I INPUT 13 -m state --state NEW,ESTABLISHED -p tcp --dport 3890 -j ACCEPT
```

## 连接 zookeeper

zookeeper 带了客户端程序能方便的连接到 zookeeper 服务 ： ~/apps/zookeeper-3.4.8/bin/zkCli.sh

用法为 zkCli.sh -server SERVER_HOST:PORT 

```shell
[zookeeper@localhost ~]$ zkCli.sh -server 127.0.0.1:2181
Connecting to 127.0.0.1:2181
2016-07-08 02:17:23,255 [myid:] - INFO  [main:Environment@100] - Client environment:zookeeper.version=3.4.8--1, built on 02/06/2016 03:18 GMT
2016-07-08 02:17:23,258 [myid:] - INFO  [main:Environment@100] - Client environment:host.name=localhost
2016-07-08 02:17:23,258 [myid:] - INFO  [main:Environment@100] - Client environment:java.version=1.8.0_92
2016-07-08 02:17:23,260 [myid:] - INFO  [main:Environment@100] - Client environment:java.vendor=Oracle Corporation
2016-07-08 02:17:23,260 [myid:] - INFO  [main:Environment@100] - Client environment:java.home=/opt/java/jdk1.8.0_92/jre
2016-07-08 02:17:23,260 [myid:] - INFO  [main:Environment@100] - Client environment:java.class.path=/home/zookeeper/apps/zookeeper-3.4.8/bin/../build/classes:/home/zookeeper/apps/zookeeper-3.4.8/bin/../build/lib/*.jar:/home/zookeeper/apps/zookeeper-3.4.8/bin/../lib/slf4j-log4j12-1.6.1.jar:/home/zookeeper/apps/zookeeper-3.4.8/bin/../lib/slf4j-api-1.6.1.jar:/home/zookeeper/apps/zookeeper-3.4.8/bin/../lib/netty-3.7.0.Final.jar:/home/zookeeper/apps/zookeeper-3.4.8/bin/../lib/log4j-1.2.16.jar:/home/zookeeper/apps/zookeeper-3.4.8/bin/../lib/jline-0.9.94.jar:/home/zookeeper/apps/zookeeper-3.4.8/bin/../zookeeper-3.4.8.jar:/home/zookeeper/apps/zookeeper-3.4.8/bin/../src/java/lib/*.jar:/home/zookeeper/apps/zookeeper-3.4.8/bin/../conf:.
2016-07-08 02:17:23,260 [myid:] - INFO  [main:Environment@100] - Client environment:java.library.path=/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
2016-07-08 02:17:23,260 [myid:] - INFO  [main:Environment@100] - Client environment:java.io.tmpdir=/tmp
2016-07-08 02:17:23,260 [myid:] - INFO  [main:Environment@100] - Client environment:java.compiler=<NA>
2016-07-08 02:17:23,260 [myid:] - INFO  [main:Environment@100] - Client environment:os.name=Linux
2016-07-08 02:17:23,260 [myid:] - INFO  [main:Environment@100] - Client environment:os.arch=amd64
2016-07-08 02:17:23,260 [myid:] - INFO  [main:Environment@100] - Client environment:os.version=3.10.0-327.22.2.el7.x86_64
2016-07-08 02:17:23,260 [myid:] - INFO  [main:Environment@100] - Client environment:user.name=zookeeper
2016-07-08 02:17:23,260 [myid:] - INFO  [main:Environment@100] - Client environment:user.home=/home/zookeeper
2016-07-08 02:17:23,260 [myid:] - INFO  [main:Environment@100] - Client environment:user.dir=/home/zookeeper
2016-07-08 02:17:23,261 [myid:] - INFO  [main:ZooKeeper@438] - Initiating client connection, connectString=127.0.0.1:2181 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@506c589e
Welcome to ZooKeeper!
2016-07-08 02:17:23,336 [myid:] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1032] - Opening socket connection to server 127.0.0.1/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)
JLine support is enabled
2016-07-08 02:17:23,390 [myid:] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@876] - Socket connection established to 127.0.0.1/127.0.0.1:2181, initiating session
[zk: 127.0.0.1:2181(CONNECTING) 0] 2016-07-08 02:17:23,644 [myid:] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1299] - Session establishment complete on server 127.0.0.1/127.0.0.1:2181, sessionid = 0x155c91f63ed0000, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null
 
[zk: 127.0.0.1:2181(CONNECTED) 0] 
```

已经连接成功了, 迫不及待拿几个命令试一下 

```shell
[zk: 127.0.0.1:2181(CONNECTED) 0] ls /
[note, conf, sgroup, servers, zookeeper]
[zk: 127.0.0.1:2181(CONNECTED) 4] create /test "this is a test node"
Created /test
[zk: 127.0.0.1:2181(CONNECTED) 5] get /test
this is a test node
cZxid = 0x800000002
ctime = Fri Jul 08 02:22:58 EDT 2016
mZxid = 0x800000002
mtime = Fri Jul 08 02:22:58 EDT 2016
pZxid = 0x800000002
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 19
numChildren = 0
```

到此, 结束了.

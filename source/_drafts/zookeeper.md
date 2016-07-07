需要有  java 环境

http://buzheng.org/centos-65-install-oracle-jdk-8.html

创建用户 

useradd zookeeper

下载

wget http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.4.8/zookeeper-3.4.8.tar.gz


解压缩

/home/zookeeper/apps/zookeeper-3.4.8

创建节点目录

mkdir /home/zookeeper/nodes/zk1
mkdir /home/zookeeper/nodes/zk2
mkdir /home/zookeeper/nodes/zk3

echo "1" > /home/zookeeper/nodes/zk1/myid
echo "2" > /home/zookeeper/nodes/zk2/myid
echo "3" > /home/zookeeper/nodes/zk3/myid

myid 文件用来存储本节点服务器ID

配置文件

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

/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh 

命令用法： zkServer.sh 操作选项 [配置文件]

如果不提供配置文件会默认使用安装目录下 conf 下的 zoo.cfg 

关于操作选项，有下面几个选项

- start 后台启动服务器
- start-foreground 前台启动服务器，如果服务器不正常的时候，可以前台启用来排错
- stop 停止服务器
- restart 重新启动服务器
- status 查看状态
- upgrade 升级

启动

```
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh start /home/zookeeper/nodes/zk1/zoo.cfg
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh start /home/zookeeper/nodes/zk2/zoo.cfg
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh start /home/zookeeper/nodes/zk3/zoo.cfg
```

查看状态

```
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh status /home/zookeeper/nodes/zk1/zoo.cfg
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh status /home/zookeeper/nodes/zk2/zoo.cfg
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh status /home/zookeeper/nodes/zk3/zoo.cfg
```

停止

```
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh stop /home/zookeeper/nodes/zk1/zoo.cfg
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh stop /home/zookeeper/nodes/zk2/zoo.cfg
/home/zookeeper/apps/zookeeper-3.4.8/bin/zkServer.sh stop /home/zookeeper/nodes/zk3/zoo.cfg
```


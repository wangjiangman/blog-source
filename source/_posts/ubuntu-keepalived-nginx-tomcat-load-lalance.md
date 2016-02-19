title: "Ubuntu下配置 keepalived+nginx+tomcat 负载均衡"
date: 2014-06-29
tags: 运维
---

本文力图阐述在 Ubuntu Server 环境下使用 Keepalived + Nginx + Tomcat 搭建高可用负载均衡环境的操作步骤和简约配置，这里不涉及性能调优。<!--more-->

先说一下他们各自扮演的角色：

- Tomcat - 应用服务器
- Nginx - 反向代理服务器，作为负载均衡器
- Keepalived - 用以检测 Nginx 状态，保证高可用

整个环境都在Virtual Box中的虚拟机上完成，以下是要完成此环境需要的软件：

- Oracle VM Virtual Box 4.3
- Ubuntu Server 14.04
- Jdk_1.7.0_60
- tomcat-7.0.54
- nginx/1.7,1
- Keepalived v1.2.7

以下是总体的节点规划，5个节点，IP分配如下：

- 192.168.1.50 - tomcat 节点，端口 8080
- 192.168.1.51 - tomcat 节点，端口 8080
- 192.168.1.52 - tomcat 节点，端口 8080
- 192.168.1.40 - nginx 节点， 端口 80 ，主节点
- 192.168.1.41 - nginx 节点， 端口 80 ，备份节点
- 192.168.1.44 - 虚拟IP

<!--more-->

## 安装 Virtual box 和 Ubuntu Server

分别到 Oracle 官网和 Ubuntu 官网下载相应的版本，安装 Virtual Box， 并新建虚拟机安装 Ubuntu Server。这里Virtual Box 中的网络设置选择“桥接网卡”，启动Ubuntu Server 后， 配置IP地址为 192.168.1.50。

``` bash
sudo vi /etc/network/interfaces
```

修改文件内容为：

``` bash
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
#iface eth0 inet dhcp

iface eth0 inet static
address 192.168.1.50
netmask 255.255.255.0
gateway 192.168.1.1
```

设置DNS

``` bash
sudo vi /etc/resolvconf/resolv.conf.d/base
```


``` bash
nameserver 192.168.1.1
nameserver 8.8.8.8
```

设置完成后重启生效。

## 安装 JRE 与 Tomcat

1,先到oracle官网下载 jre， 以下是 oracle java 下载首页：http://www.oracle.com/technetwork/java/javase/downloads/index.html，然后选择相应的版本下载即可。这里选择版本是：server-jre-7u60-linux-x64.tar.gz

2，上传到 用户目录下，解压缩

``` bash
tar -xvf server-jre-7u60-linux-x64.tar.gz
```

完成后，同目录下会出现文件夹 jdk1.7.0_60

3，创建 /usr/local/java ，然后将jdk目录拷贝过来

``` bash
sodu mkdir /usr/local/java
sudo cp -r ./jdk1.7.0_60 /usr/local/java/
```

4，设置环境变量

``` bash
sudo vi /etc/profile

# 添加一下内容
# java
export JAVA_HOME=/usr/local/java/jdk1.7.0_60
export PATH=$JAVA_HOME/bin:$PATH
```

5，使环境变量生效

``` bash
source /etc/profile
```

6，下载 tomcat：http://tomcat.apache.org/download-70.cgi， 选择7.0.54 进行下载，解压缩即可。

7，上传应用 load-balance 到 tomcat 的 webapps 目录下。 load-balance只包含一个jsp页面:index.jsp，内容如下：

``` text
<%=request.getLocalAddr() %>
```

request.getLocalAddr() 获取服务器本机IP地址

8，启动tomcat

``` bash
apache-tomcat-7.0.54/bin/startup.sh
```

查看地址 http://192.168.1.50:8080/load-balance/ 页面正常显示，并输出服务器的IP地址。

## 创建 51，52，40 节点

利用 Virtual Box 的虚拟机复制功能，将192.168.1.50节点复制三个，然后利用刚刚上面的办法将Ip地址分别修改为 192.168.1.51，192.168.1.51，192.168.1.40。

192.168.1.40 节点留作 nginx 节点。

## 安装 Nginx 并配置负载均衡

1，在节点 192.168.1.40 上执行安装 nginx，操作如下：

``` bash
sudo apt-add-repository ppa:nginx/development
sudo apt-get update
sudo apt-get install nginx
```

nginx启动命令为 sudo /etc/init.d/nginx start   或者 service nginx start

多说几句，以下是对 nginx 的几个参数的说明

- start - 启动
- stop - 停止
- restart - 重启动
- reload - 热加载配置文件
- status - 查看状态

直接访问 http://192.168.1.40 看看 nginx 是否安装成功

2，配置Nginx 负载均衡

通过命令 sudo vi /etc/nginx/nginx.conf 编辑 nginx 配置文件，在 http 节点中添加如下信息

``` bash
upstream myserver {
  server 192.168.1.50:8080 weight=1 max_fails=3 fail_timeout=20s;
  server 192.168.1.51:8080 weight=1 max_fails=3 fail_timeout=20s;
  server 192.168.1.52:8080 weight=1 max_fails=3 fail_timeout=20s;
}

server
{
    listen 80;
    server_name 192.168.1.40;
    index index.htm index.html;
    root /etc/wwwroot;

        location / {
                proxy_pass http://myserver;
                proxy_next_upstream http_500 http_502 http_503 error timeout invalid_header;
                include /etc/nginx/proxy_params;
        }
}
```

upstream 节点配置负载均衡，后面的myserver 必须与 server 节点中 location 姐弟啊你的 proxy_pass http://myserver保持一致。 负载均衡默认采用轮询算法，根据权重将请求分发到不同的后台tomcat节点上。当然也可以设置其他的分发算法，比如ip_hash等。

weight 表示的分发的权重。

server 节点定义服务器信息，server_name 节点为本机的IP地址。

修改完配置后，使用如下命令是配置生效

``` bash
sudo service nginx reload
```

3，验证

使用URL: http://192.168.1.40/load-balance 来验证请求是否分发到tomcat节点上，如果配置成功，不停的刷新页面，会发现页面的内容为3个tomcat节点的IP地址交替出现。

停止一个tomcat 节点后，会发现请求不再向此节点转发。重启tomcat节点后，又自动加入负载均衡队伍中。

## 使用 Keepalived 保证高可用性

nginx 负载均衡虽然提高了性能，但是一个 nginx 节点会成为很容易成带来一个问题，那就是“单点故障”。万一机器挂掉怎么办？如何平滑切换到备份机？ keepalived 可以在主 nginx 节点挂掉时，能自动帮助我们切换到备份节点。这里主要用到一个虚拟IP，即主 nginx 节点和备份 nginx 节点都使用同一个虚拟IP 192.168.1.44，访问的时候，我们通过虚拟IP来访问应用，具体步骤请继续往下（以下操作都在 192.168.1.40 节点上进行， 等配置完成后，直接克隆出 192.168.1.41 节点，只需要修改 IP地址和keepalived 的配置文件）。

1，增加虚拟IP

``` bash
sudo ifconfig  eth0:0 192.168.1.44 netmask 255.255.255.0
```

通过 ifconfig 或者 ip a 或者 ip address show eth0 便可以看出此时机器已经有两个IP，如下

``` bash
adam@vm1:~$ ip a show eth0
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:b9:3c:f7 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.40/24 brd 192.168.1.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet 192.168.1.44/32 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:feb9:3cf7/64 scope link 
       valid_lft forever preferred_lft forever
```

2，安装 keepalived

``` bash
sudo apt-get install keepalived
```

安装完成 keepalived后，需要手动增加配置文件 /etc/keepalived/keepalived.conf，如下：

``` bash
vrrp_script chk_nginx {
	script &quot;/etc/keepalived/check_nginx.sh&quot;
	interval 2
	weight 2
}

global_defs {
	notification_email {
		
	}
}
vrrp_instance VI_1 {
	state MASTER
	interface eth0
	virtual_router_id 51 
	mcast_src_ip 192.168.1.40
	priority 20
	advert_int 1
	
	authentication {
		auth_type PASS
		auth_pass 123456
	}
	track_script {
		chk_nginx
	}
	virtual_ipaddress {
		192.168.1.44
	}
}
```

这里说一下 keepalived 配置在两台 nginx节点上的区别：

- state 不同，MASTER 和 BACKUP
- priority 不同，MASTER 要高于 BACKUP
- mcast_src_ip  各自nginx服务器的实际IP

除了以上配置外，两台 Nginx 节点上的 keepalived.conf 内容完全相同。

还有  /etc/keepalived/check_nginx.sh 是一个脚本，用来检查 nginx 的状态，大意为如果nginx 进程不存在，则启动 nginx， 如果启动不成功，则关闭keepalived。内容如下

``` bash
#!/bin/bash  
A=`ps -C nginx --no-header |wc -l`  
if [ $A -eq 0 ];then  
 /etc/init.d/nginx start  
sleep 3  
if [ `ps -C nginx --no-header |wc -l`-eq 0 ];then  
 /etc/init.d/keepalived stop
fi  
fi
```

使配置生效

``` bash
sudo service keepalived reload
```

3，修改 nginx 的配置， 将 server_name 的值由本机IP（192.168.1.40），修改为 虚拟IP地址（192.168.1.44），然后使配置生效。

``` bash
sudo service nginx reload
```

4，验证，http://192.168.1.44/load-balance 看是否能负载分发到不同的tomcat节点。

5，从 Virtual Box 中 复制 192.168.1.40 虚拟机 到 192.168.1.41，然后修改 IP地址为 192.168.1.41，再修改 keepalived 配置文件， state 为 BACKUP, mcast_src_ip 为 192.168.1.40，priority 为一个小点值。然后使配置生效。

6，先验证 http://192.168.1.44/load-balance 看是否能访问，成功后， shutdown 掉节点 192.168.1.40，再访问 http://192.168.1.44/load-balance ， 看是否能正常访问，如果能正常访问，则成功了。

注： 若修改IP后，不能正常访问，重启服务器试试。

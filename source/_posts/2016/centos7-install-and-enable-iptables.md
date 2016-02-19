title: "CentOS 7 下安装并启用 iptables"
date: 2016-01-13
tags: [Linux, CentOS]
---

CentOS 7 安装成功后，发现 iptables 并不能使用，这是因为 CentOS 7 中启用了新的防火墙 firewalld，如果想使用 iptables 则需要安装。<!--more-->

**安装 iptables 和 iptables 服务**

```shell
yum -y install iptables
yum -y install iptables-services
```

**禁用 firewalld，启用 iptables 和 ip6tables**

```shell
systemctl mask firewalld.service
systemctl enable iptables.service
systemctl enable ip6tables.service
```

**停用 firewalld，启动 iptables 和 ip6tables**

```shell
systemctl stop firewalld.service
systemctl start iptables.service
systemctl start ip6tables.service
```

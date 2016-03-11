title: "centos 6.5 修改 SSH 端口并禁用 root 远程登录"
date: 2015-08-12
tags: [Linux, Oracle]
categories: 技术
---

我们知道 SSH 的默认端口为 22，但是基于安全的需要，我们需要修改服务器的 SSH 端口，和禁用 root 远程登录。 

<!--more-->

通过以下步骤，我们通过编辑 /etc/ssh/sshd_config ，将端口修改为 10089，并禁用 root 远程登录，同时为新端口添加防火墙规则，并删除默认端口的规则。

> 1，使用 root 用户执行以下步骤；2，只在 CentOS 6.5 下验证。

## 修改端口

``` bash
vi /etc/ssh/sshd_config

Port 10089 #端口号
PermitRootLogin no # 禁止root ssh
```

## 为新端口开通防火墙规则

``` bash
iptables -I INPUT 4 -m state --state NEW -p tcp --dport 10089 -j ACCEPT
service iptables save
service iptables reload
```

## 删除22端口的规则

``` bash
iptables -D INPUT $RULE_LINE_NUMBER
service iptables save
service iptables reload
```

> $RULE_LINE_NUMBER 可以通过 iptables -L -n --line-number 来查看



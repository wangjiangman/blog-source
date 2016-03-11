title: "iptables 使用笔记"
date: 2015-03-31 23:08:09
tags: [Linux, iptables]
categories: 技术
---

iptables 是 linux 下的防火墙程序，在所有的 linux 发行版中都能使用。

iptables 从名字上看就是基于 tables的，其层次结构为 iptables -> tables -> chains -> rules。 iptables 中包括4个 table ：Filter, NAT, Mangle, Raw；filter 表包含了三种 Chain: INPUT, OUTPUT, FORWARD；在每个 Chain 下，可以定义各种 rule 。

<!-- more -->

> filter 是默认的表，本文所有操作只涉及 filter 表; 所有操作的用户为 root。


## 查看现有规则

**举例**

``` bash
iptables -L 
iptables -L -n
iptables -L -n --line-number
iptables -L -t filter
```

**参数说明**

- <code>-L</code> / <code>--list</code> 列表显示 
- <code>-n</code> / <code>--numeric</code> 用数字显示端口号，默认显示服务名
- <code>--line-number</code> 显示行号
- <code>-t</code> / <code>--table</code> 指定表，可用值: filter(默认), nat, mangle, raw

## 添加规则

iptables 每个 chain 中包含了若干规则，当需要验证规则的时候，iptables 会逐条判断规则，当前规则条件不满足的时候会进入下一个规则判定。
每一个规则由条件和目标(target)组成，目标就是当满足规则条件时，要对连接进行的操作。 target 有下面的四个值：

- <code>ACCEPT</code> 接受请求数据包 
- <code>DROP</code> 丢掉数据包，不响应任何内容
- <code>REJECT</code> 拒绝，响应错误包
- <code>QUEUE</code> 将数据包移交到用户空间
- <code>RETURN</code> 直接返回到调用链中，不再执行后续规则

**举例**

``` bash
iptables -A INPUT -m state --state NEW,ESTABLISHED -p tcp --dport 1521 -j ACCEPT
iptables -I INPUT 4 -m state --state NEW,ESTABLISHED -p tcp --dport 1521 -j ACCEPT
```

**参数说明**

- <code>-A INPUT</code> 追加规则到 INPUT 链中
- <code>-I INPUT 4</code> 在INPUT链中的第4行插入规则  
- <code>-p</code> 协议，可用之：tcp, udp, imcp, all 等
- <code>-s</code> 源IP地址
- <code>--sport</code> 源地址端口号
- <code>-d</code> 目的IP地址
- <code>--dport</code> 目的端口号
- <code>-j</code> target，可用值：ACCEPT, DROP, REJECT, QUEUE, RETURN
- <code>-m state</code> 启用状态模块
- <code>--state</code> 匹配状态值 NEW, ESTABLISHED
- <code>-i</code> 指定输入端口，eg: -i eth0
- <code>-o</code> 指定输出端口，eg: -o eth0


## 删除规则

**删除某个规则**

如下 删除 INPUT chain 中的第9行规则，查询行号可用 --line-number 参数。

``` bash
iptables -D INPUT 9   
```

**清空所有规则**
``` bash
iptables -F 
```

- <code>-F</code> / <code>--flush</code> 删除现有所有规则

## 使规则生效

当使用命令添加/删除规则后，并不能立即使规则生效，需要先保存规则，然后重启 iptables 。

> 以下命令在 CentOS 中测试

``` bash
serviec iptables save # 保存
serviec iptables reload # 重新加载服务
```

当然也可以使用 iptables-save 来保存 iptables 的新规则

``` bash
iptables-save
/etc/init.d/iptables reload
```

## 参考文档

[iptables详细教程：基础、架构、清空规则、追加规则、应用实例](https://lesca.me/archives/iptables-tutorial-structures-configuratios-examples.html)

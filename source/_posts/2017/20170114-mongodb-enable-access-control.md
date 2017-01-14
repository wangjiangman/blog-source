# 

title: "Mongodb 开启访问控制"
date: 2017-01-14
tags: [Mongodb]
categories: 技术
---

Mongodb 数据库默认情况下是没有访问控制的，整个数据库对外是开发的，只要能连上数据库，则可以进行任何操作，这会对数据带来很大的风险。当然，我们可以启用mongodb的访问控制，只让通过认证的用户才能对数据库进行角色范围内的操作。

启用访问控制可以通过在启动 mongodb 时指定 `--auth` 参数来设置，另外还涉及到创建用户 db.createUser 操作以及一些角色的定义，我们先来看这部分内容。<!--more-->

## db.createUser() 用法 

```js
db.createUser({
	user: "$USERNAME",
	pwd: "$PASSWROD",
	roles: [
		{ role: "$ROLE_NAME", db: "$DBNAME"}
	]
})
```

参数说明：
- user 是用户名  
- pwd 是密码 
- role 来指定用户的角色
- db 来指定所属的数据库
- roles 是用户所有角色的集合

## Mongodb 预定义角色

Mongodb 中预定义了一些角色，把这些角色赋予给适当的用户上，用户就只能进行角色范围内的操作。

- 数据库用户角色 (所有数据库都有)
  - `read`  用户可以读取当前数据库的数据
  - `readWrite` 用户可以读写当前数据库的数据
- 数据库管理角色(所有数据库都有)
  - `dbAdmin`  管理员用户但不能对用户和角色管理授权
  - `dbOwner` 数据库所有者可进行任何管理任务
  - `userAdmin` 可以管理当前数据的用户和角色
- 集群管理角色(admin数据库可用)
  - `clusterAdmin` 集群所有管理权限，是 `clusterManager` , `clusterMonitor`, `hostManager` 合集
  - `clusterManager` 集群管理和监控
  - `clusterMonitor` 集群监控，只读的
  - `hostManager` 监控和管理服务器
- 备份和恢复角色(admin数据库可用)
  - `backup ` 
  - `restore` 
- 所有数据库角色(admin数据库可用)
  - `readAnyDatabase`  读取所有数据库
  - `readWriteAnyDatabase` 读写所有数据库
  - `userAdminAnyDatabase` 所有数据库的 userAdmin 权限
  - `dbAdminAnyDatabase` 所有数据库的 dbAdmin 权限
- 超级角色(admin数据库可用) 
  - `root`  超级用户
- 内部角色
  - `__system` 所有操作权限
	
更多预定于角色的信息请参看：https://docs.mongodb.com/manual/core/security-built-in-roles/

## 启用访问控制的步骤

### 1, 启动 mongodb 实例，关闭 访问控制

不带 `--auth`

```
./mongod 
```

### 2, 连接上 mongodb 实例

```
./mongo
```

### 3，创建用户管理员

在 admin 数据库中添加一个 具有 `userAdminAnyDatabase` 角色的用户作为用户管理用户。下面的例子中创建了 admin 为用户管理员。


```bash
> use admin
switched to db admin
> db.createUser({
... user: "admin",
... pwd: "admin",
... roles: [
... { role: "userAdminAnyDatabase", db: "admin"}
... ]
... })
Successfully added user: {
	"user" : "admin",
	"roles" : [
		{
			"role" : "userAdminAnyDatabase",
			"db" : "admin"
		}
	]
}
> 
```

退出连接

### 4，重启数据库启用访问控制

命令行启动，只需要添加 `--auth` 参数

```
./mongo --auth
```

### 5，使用管理用户连接，有两种方法

- 使用命令行  `./mongo -u "$USERNAME" -p "$PASSWROD" --authenticationDatabase "admin"`
- 使用 db.auth()
	
我们使用第二种

```js
> 
> use admin
switched to db admin
> db.auth("admin", "admin")
1
> 
```

1 表示认证成功

### 6， 为某个数据库创建独立用户

以下为 test 数据库 创建具有读写权限的用户   test 

`admin` 用户由于只有 `userAdminAnyDatabase` 权限，所以没有 `test` 数据的读写权限，所以，为了读写 `test` 数据库，我们需要创建一个用户。先看一下直接用 `admin` 会报什么错误

```js
> use test
> show collections
2017-01-13T13:49:17.691+0800 E QUERY    [thread1] Error: listCollections failed: {
"ok" : 0,
"errmsg" : "not authorized on test to execute command { listCollections: 1.0, filter: {} }",
"code" : 13
} :
_getErrorWithCode@src/mongo/shell/utils.js:25:13
DB.prototype._getCollectionInfosCommand@src/mongo/shell/db.js:773:1
DB.prototype.getCollectionInfos@src/mongo/shell/db.js:785:19
DB.prototype.getCollectionNames@src/mongo/shell/db.js:796:16
shellHelper.show@src/mongo/shell/utils.js:754:9
shellHelper@src/mongo/shell/utils.js:651:15
@(shellhelp2):1:1
```

我们直接使用  `show collections` , 则报错：`not authorized on test to execute command` ，意思是没有权限。

```js
> use test
switched to db test
> db.createUser({
... user: "test",
... pwd: "test",
... roles: [
... { role: "readWrite", db: "test"}
... ]
... })
Successfully added user: {
	"user" : "test",
	"roles" : [
		{
			"role" : "readWrite",
			"db" : "test"
		}
	]
}
> 
```

然后我们使用 db.auth("test", "test"), 再执行命令 则没有报错

```
> db.auth("test", "test")
1
> 
> show collections
```

试着写入一条数据，也是正常的。

```
> db.t.insert({name:"buzheng"});
WriteResult({ "nInserted" : 1 })
> db.t.find();
{ "_id" : ObjectId("58786c84bf5dd606ddfe1144"), "name" : "buzheng" }
> 
```

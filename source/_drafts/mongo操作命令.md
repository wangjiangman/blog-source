# mongo 常用命令

## 增删改查

### insert  & save 

```javascript
db.user.insert({name: "wangwu", age: 30, hobby: ["billiards", "coding"]});
db.user.save({name: "zhangsan", age: 20, hobby: ["basketball", "football", "writing"]});
db.user.save({name: "lisi", age: 25, hobby: ["football", "swimming"]});
```

insert 和 save 都能保存数据，区别在于，如果保存的实体如果带有ID的话，save会直接覆盖现有ID对应的实体类。

### update

```javascript
db.user.update({name:"wangwu"}, {$set: {age: 28}});
db.user.update({name:"wangwu"}, {$set: {age: 28}}, upsert=true, multi=true);
```

删除某个字段

```javascript
db.User.update({},{$unset:{'FIELD_NAME':''}},false, true)
```


### remove 

```javascript
db.user.remove({name:"wangwu"});
```

### find

```javascript
db.user.find();
db.user.find({age: {$gt: 20}});
```

fields

```javascript
db.user.find({age: {$gt: 20}}, {name:1});
db.user.find({age: {$gt: 20}}, {name:0});
db.user.find({age: {$gt: 20}}, {name:0, age:0});
```

### sort 

```javascript
db.user.find().sort({age:1});   // 1 asc  0r -1 desc
```


## 管理相关命令

**进入数据库admin**

use admin

**增加或修改用户密码**

db.addUser('name','pwd')

**查看用户列表**

db.system.users.find()

**用户认证**

db.auth('name','pwd')

**删除用户**

db.removeUser('name')

**查看所有用户**

show users

**查看所有数据库**

show dbs

**查看所有的collection**

show collections

**查看各collection的状态**

db.printCollectionStats()

**查看主从复制状态**

db.printReplicationInfo()

**修复数据库**

db.repairDatabase()

**设置记录profiling，0=off 1=slow 2=all**

db.setProfilingLevel(1)

**查看profiling**

show profile

**拷贝数据库**

db.copyDatabase('mail_addr','mail_addr_tmp')

**删除collection**

db.mail_addr.drop()

**删除当前的数据库**

db.dropDatabase()

**备份数据库**

mongodump -h localhost:27017 -d dataname -o /data/dump

**恢复数据库**

mongorestore -d dataname /data/dump

**备份数据库表**

mongodump -h localhost:27017 -d dataname -c tablename -o /data/dump

**恢复数据库表**

mongorestore -d dataname -c tablename /data/dump

mongorestore -h host:port -d dataname --collection tablename ./tmpdump/some.bson

**远程连接服务器mongodb (mongo --help)**

mongo --host 18.126.23.156 --port 27017

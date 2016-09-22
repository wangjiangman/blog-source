# mongo 常用命令

## 增删改查



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


db.createUser({ user: "admin", pwd: "admin", roles: [ { role: "userAdminAnyDatabase", db: "admin"} ] })

db.createUser({ user: "btw", pwd: "btw", roles: [ { role: "readWrite", db: "btw"} ] })

mongodump -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -o 文件存在路径 

/opt/mongodb/bin/mongodump -h localhost --port 27017 -u btw -p btw -d btw -o ~/btw.json 


mongorestore -h localhost --port 27017 -u btw -p btw -d btw --drop /home/buzheng/btw


mongodump mongorestore 导出与导入数据库

mongoexport mongoimport 到处与导入数据库表


#!/bin/bash

sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
echo "deb [ arch=amd64,arm64 ] http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
sudo apt-get update
sudo apt-get install -y mongodb-org
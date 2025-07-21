# 1、本地运行
## 1.1、mongodb

1、下载地址
https://www.mongodb.com/try/download/community

2、环境变量
MONGODB_HOME
%MONGODB_HOME%\bin

2、启动命令
mongod --dbpath="D:\soft\dev\mongodb\mongodb-win32-x86_64-windows-6.0.25\data\db" --bind_ip=0.0.0.0  --replSet myReplicaSet

mongod --config D:\soft\dev\mongodb\mongodb-win32-x86_64-windows-6.0.25\mongod.cfg

#停止mongo
systemctl stop mongod
systemctl status mongod
 
#备份配置文件
cp -a  /etc/mongod.conf /etc/mongod.conf.bk
 
#编辑配置文件
vim  /etc/mongod.conf
#添加以下内容
replication:
  oplogSizeMB: 2000
  replSetName: rs0   
  enableMajorityReadConcern: true
 
#保存
#启动mongo
systemctl start mongod
systemctl status mongod
 
 
##初始化副本
mongosh
mongosh> rs.initiate( { _id: "rs0", version: 1, members: [ { _id: 0, host: "localhost:27016" } ] })
mongosh> rs.status
 
## 1.2、postgresql
1、下载地址
https://pan.quark.cn/s/84b02d9e31a3

2、为PostgreSQL超级用户（默认为postgres）设置密码

## 1.3、pgvector
1、下载地址
https://github.com/pgvector/pgvector/tags

2、安装Visual Studio，工作负载模块选c++

3、依次执行以下命令
call "D:\soft\dev\visual studio 2022\VC\Auxiliary\Build\vcvars64.bat"
cd D:\soft\dev\postgresql\pgvector\pgvector-master
set "PGROOT=D:\soft\dev\postgresql\soft"
nmake /F Makefile.win
nmake /F Makefile.win install

4、最后在数据库连接工具中，选中具体的数据库实例，执行以下命令，就能扩展 vector 类型了
CREATE EXTENSION vector;

# 2、修改信息

## 2.1、修改知识库信息

docker exec -it 681 /bin/bash
mongo --username root --password 123456 --authenticationDatabase admin
use fastgpt
db.getCollectionNames()
db.datasets.find().pretty()

db.datasets.find({"name":"非煤"}).pretty()

db.datasets.updateOne(
  { name: "法律法规" },
  { $set: { vectorModel: "BAAI/bge-m3" } }
)

db.datasets.updateOne(
  { name: "ollama-test" , "teamId": ObjectId("682c16a47313d4f78acac0b1")},
  { $set: { vectorModel: "BAAI/bge-m3" } }
)

db.team_members.insert({
    teamId: db.teams.findOne({name:"lantrack"}, {_id:1})._id, 
    userId: db.users.findOne({username:"root"}, {_id:1})._id, 
    name: "Owner",
    role: "owner",
    status: "active",
    createTime: new ISODate(),
    defaultTeam: true
});


db.team_members.updateOne({userId: "6823154ed183465469e67fde"}, { $set: {teamId: "682c16a47313d4f78acac0b1"} });

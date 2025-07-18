# 1、mongodb

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
 
# 2、postgresql
1、下载地址
https://pan.quark.cn/s/84b02d9e31a3

2、为PostgreSQL超级用户（默认为postgres）设置密码

# 3、pgvector
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

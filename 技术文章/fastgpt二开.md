# 1、mongodb

1、下载地址
https://www.mongodb.com/try/download/community

2、环境变量
MONGODB_HOME
%MONGODB_HOME%\bin

2、启动命令
mongod --dbpath="D:\package\mongodb\data\db" --bind_ip=0.0.0.0

# 2、postgresql
1、下载地址
https://pan.quark.cn/s/84b02d9e31a3



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

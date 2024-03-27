# 1、基本概念
Arthas是阿里巴巴旗下的一款开源的JAVA诊断工具，可以进行线上监控诊断产品，实时查看应用的内存、gc、线程等信息，方便排查线上问题


# 2、安装

```shell
curl -O https://arthas.aliyun.com/arthas-boot.jar
java -jar arthas-boot.jar
```

启动之后，就会列出当前系统上所有的java进程，输入进程前面的序号，连接到相应的应用进程。

![image.png|675](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202403271031144.png)

# 3、常用命令

## 3.1、dashbord

dashboard 命令可以查看当前系统的实时数据面板。可以查看到CPU、内存、GC、运行环境等信息、

![](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202403271123268.png)
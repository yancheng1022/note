# 1、背景
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

>ID: 线程ID 
>NAME: 线程名 
>GROUP: 线程组名 
>PRIORITY: 线程优先级, 1~10之间的数字，越大表示优先级越高 
>STATE: 线程的状态. 
>CPU%: 线程的cpu使用率。比如采样间隔1000ms，某个线程的增量cpu时间为100ms，则cpu使用率=100/1000=10% 
>DELTA_TIME: 上次采样之后线程运行增量CPU时间，数据格式为秒 
>TIME: 线程运行总CPU时间，数据格式为分:秒 
>INTERRUPTED: 线程当前的中断位状态 
>DAEMON: 是否是daemon线程
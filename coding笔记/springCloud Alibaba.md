# 1、服务注册与发现 - nacos


## 1.1、nacos部署

单机模式 - 用于测试和单机试用。
集群模式 - 用于生产环境，确保高可用。
多集群模式 - 用于多数据中心场景

### 1.1.1、单机部署

1、下载地址：https://nacos.io/，解压
2、修改配置conf/spplication.properties

```properties
spring.datasource.platform=mysql
db.num=1
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user.0=root
db.password.0=root
```

3、初始化数据库

执行conf下 mysql-schema.sql 初始化数据库

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202405191517635.png)

4、修改bin/starup.cmd（linux下修改startup.sh）

set MODE="cluster"  改为 set MODE="standalone"

5、执行启动脚本

6、访问http://127.0.0.1:8848/nacos，输入用户名/密码：nacos/nacos，进入主界面

### 1.1.2、集群部署

本次部署 3个nacos节点，然后一个负载均衡器（nginx）代理3个Nacos

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202405191529539.png)

1、三个节点的IP和端口需要配置到cluster.conf文件中。 使用以Nacos自带的cluster.conf.example文件复制一份，作为cluster.conf文件。编辑cluster.conf文件

```conf
#it is ip
#example
127.0.0.1:8845
127.0.0.1.8846
127.0.0.1.8847
```

5、将nacos文件夹复制三份，分别命名为：nacos1、nacos2、nacos3，然后分别修改三个文件夹中的application.properties里的端口

6、配置nginx负载均衡

```
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    sendfile        on;
	  upstream nacos-cluster{
        server 127.0.0.1:8845;
        server 127.0.0.1:8846;
        server 127.0.0.1:8847;
	  }
    server {
        listen       88;
        server_name  localhost;
        location / {
            root   html/sky;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
        # 反向代理,处理管理端发送的请求
        location /nacos/ {
			proxy_pass   http://nacos-cluster;
        }
    }
}

```
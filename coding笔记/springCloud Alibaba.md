# 1、服务注册与发现 - nacos


## 1.1、nacos注册中心部署

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

7、在浏览器访问：http://localhost:88/nacos即可

>实际部署时，需要给做反向代理的nginx服务器设置一个域名，这样后续如果有服务器迁移nacos的客户端也无需更改配置.
 Nacos的各个节点应该部署到多个不同服务器，做好容灾和隔离

## 1.2、nacos注册中心工作流程

**服务注册**:Nacos Client会通过发送REST请求的方式向Nacos Server注册自己的服务，提供自身的元数据，比如ip地址、端口等信息。Nacos Server接收到注册请求后，就会把这些元数据信息存储在一个双层的内存Map中（外层key是namespace，内层key是group:service）。

**服务心跳**:在服务注册后，Nacos Client会维护一个定时心跳来持续通知Nacos Server，说明服务一直处于可用状态，防止被剔除。默认5s发送一次心跳。

**服务同步**:Nacos Server集群之间会互相同步服务实例，用来保证服务信息的一致性。

**服务发现**:服务消费者(Nacos Client)在调用服务提供者的服务时，会发送一个REST请求给NacosServer，获取上面注册的服务清单，并且缓存在Nacos Client本地，同时会在Nacos Client本地开启个定时任务（默认35s）定时拉取服务端最新的注册表信息更新到本地缓存

**服务健康检查**:Nacos Server会开启一个定时任务用来检查注册服务实例的健康情况，对于超过15s没有收到客户端心跳的实例会将它的healthy属性置为false(客户端服务发现时不会发现)，如果某个实例超过30秒没有收到心跳，直接别除该实例(被剔除的实例如果恢复发送心跳则会重新注册)

## 1.3、nacos领域模型

nacos的服务由三元组唯一确定（namespace、group、serviceName）
nacos的配置由三元组唯一确定（namespace、group、dataId）

![image.png|500](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202405191706470.png)


| 模型名称      | 解释                                     |
| --------- | -------------------------------------- |
| Namespace | 实现环境隔离，默认值public（一般用于区分测试、生产环境等）       |
| Group     | 不同的service可以组成一个Group，默认值Default-Group |
| Service   | 服务名称                                   |
| Cluster   | 对指定的微服务虚拟划分，默认值Default                 |
| Instance  | 某个服务的具体实例（ip+端口）                       |

Nacos 服务发现使用的领域模型是命名空间-分组-服务-集群-实例这样的多层结构。服务 Service 和实例 Instance 是核心模型，命名空间 Namespace 、分组 Group、集群 Cluster 则是在不同粒度实现了服务的隔离


# 2、远程调用 - RestTemplate

RestTemplate是一款Spring框架中的HTTP客户端工具类库，它封装了大量的HTTP请求处理代码，使得我们可以方便地进行HTTP请求的发送与处理。RestTemplate支持多种HTTP请求方式，例如GET、POST、PUT、DELETE等，同时也支持参数的传递与响应结果的解析等功能

下面简单看一下它的三种使用方式：

## 2.1、直接使用

```java
RestTemplate restTemplate = new RestTemplate();
String result = restTemplate.getForObject("http://localhost:8761/order", String.class);
```


## 2.2、使用cloud的 LoadBalancerClient 获取服务地址

```java
    @Autowired
    LoadBalancerClient loadBalancerClient;

    @GetMapping
    public String getOrder()
    {
        RestTemplate template = new RestTemplate();
		// ORDER为服务名
        ServiceInstance instance = loadBalancerClient.choose("ORDER");
        String url = instance.getHost();
        int port = instance.getPort();

        String result = template.getForObject(url + ":" + port + "/order", String.class);
        return result;
    }
```

## 2.3、Ribbon + RestTemplate

其实就是和cloud的Ribbon负载均衡器整合

```java
    // RestTemplate 注解为bean， 并加上 @LoadBalanced
    @Bean
    @LoadBalanced
    RestTemplate restTemplate()
    {
        return new RestTemplate();
    }
  
    
    // 注入 @Autowired
    RestTemplate template;
  
    // 使用(ORDER为服务名)
    @GetMapping
    public String getOrder()
    {
        String result = template.getForObject("http://ORDER/order", String.class);
        return result;
    }
```

# 3、负载均衡器 - Ribbon

实际开发中一般不会让网址直接访问代码服务器(小项目除外),通常是通过nginx进行反向代理和负载均衡.而在Springcloud里我们用了另外2个组件来代替nginx的这两个功能,网关代替反向代理，Ribbon就是代替负载均衡的组件.通常在微服务架构中,业务都会被拆分成一个独立的服务,服务与服务的通讯是基于http restful的.Spring cloud有两种服务调用方式,一种是ribbon+restTemplate,另一种是feign

## 3.1、Ribbon的工作流程

1、restTemplate的bean添加了@LoadBalanced注解，这个注解就是一个标记，表示这个RestTemplate发起的请求要被Ribbon拦截和处理。
2、Ribbon解析url中的host，获取服务名，根据服务名获取实例列表（先从本地缓存获取，没有再从nacos server获取并缓存实例列表）
3、通过相应的负载均衡策略获取一个实例的ip+端口
4、发起远程调用

## 3.2、Ribbon均衡策略

Ribbon核心组件IRule：根据特定算法从服务列表中选取一个需要访问的服务；其中IRule是一个接口，有七个自带的落地实现类，可以实现不同的负载均衡算法规则。

负载均衡的实现主要有三种算法：轮询（默认方式）、随机、权值分配，其中轮询 ZoneAvoidanceRule 也是 @LoadBalanced 注解默认的分配策略，细节上可以分为以下几种

| 策略名                       | 名称       | **规则描述**                                                                                                                                                                          |
| ------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RoundRobinRule            | 轮询策略     | 简单轮询服务列表来选择服务器。                                                                                                                                                                   |
| RandomRule                | 随机策略     | 随机选择一个可用的服务器                                                                                                                                                                      |
| RetryRule                 | 重试策略     | 正常按轮询，失败后重试机制的选择逻辑                                                                                                                                                                |
| BestAvailableRule         | 最低并发策略   | 忽略那些短路的服务器，并选择并发数较低的服务器                                                                                                                                                           |
| WeightedResponseTimeRule  | 响应时间加权策略 | 为每一个服务器赋予一个权重值。服务器响应时间越长，这个服务器的权重就越小。这个规则会随机选择服务器，这个权重值会影响服务器的选择                                                                                                                  |
| AvailabilityFilteringRule | 可用过滤策略   | 对以下两种服务器进行忽略：<br><br> （1）在默认情况下，这台服务器如果3次连接失败，这台服务器就会被设置为“短路”状态。短路状态将持续30秒，如果再次连接失败，短路的持续时间就会几何级地增加。<br><br>（2）并发数过高的服务器。如果一个服务器的并发连接数过高，配置了AvailabilityFilteringRule规则的客户端也会将其忽略 |
| ZoneAvoidanceRule（默认）     | 区域权衡策略   | 综合判断 Server 所在区域的性能和 Server 的可用性轮询选择 Server，并且判定一个 AWS Zone 的运行性能是否可用，剔除不可用的 Zone 中的所有 Server                                                                                     |

## 3.3、切换负载均衡策略

**方法一：**

1、创建配置类

```java
@Configuration  
public class MyRibbonConfig {

    @Bean
    public IRule ribbonRule() {
        //随机策略
        return new RandomRule();
    }

}
```

2、启动类上加注解

```java
@RibbonClient(name = "user-provider", configuration = MyRibbonConfig.class)
```


**方法二：**

application.yml文件中，添加新的配置也可以修改规则

```yml
userservice:
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule #负载均衡规则
```
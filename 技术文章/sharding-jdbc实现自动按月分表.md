# 1、基本需求

1、 项目中我们希望 能够按照时间、类别来添加表。但是sharding-jdbc 是固定配置 的 actual-data-nodes 参数。也就是说我们需要提前创建好分表或者分库。那么我们需要如何来实现动态创建表，并且动态刷新 actual-data-nodes 呢。

2、思路就是写个定时器来动态创建表 ，在创建表的时候 动态刷新 actual-data-nodes 实现动态创建表被shard加载。

# 2、功能实现

## 2.1、添加依赖
```xml
<dependency>
    <groupId>org.apache.shardingsphere</groupId>
    <artifactId>sharding-jdbc-spring-boot-starter</artifactId>
    <version>4.0.0-RC1</version>
</dependency>
<dependency>
    <groupId>org.apache.shardingsphere</groupId>
    <artifactId>sharding-jdbc-spring-namespace</artifactId>
    <version>4.0.0-RC1</version>
</dependency>
```


## 2.2、yml配置
```yml
sharding:  
    # 配置绑定表  
    binding-tables[0]: ims_test_result,ims_test_sample_fetch,ims_test_sample_diluent,ims_test_reagent_add,ims_test_ls_add,ims_test_incubate,ims_test_read  
    tables:  
        # 仪器温度记录表  
        ims_instrument_temperature:  
            actual-data-nodes: m1.sharding_data_nodes_2022  
            key-generator:  
                column: id # 注意：这里配置了主键,插入sql中就不要再显示写,否则这里配置的主键生成策略失效  
                type: SNOWFLAKE  
                props:  
                    worker.id: ${workerId}  
            table-strategy:  
                standard:  
                    sharding-column: monitor_time_format  
                    precise-algorithm-class-name: com.chivd.common.algorithm.TableShardingDateAlgorithm  
```
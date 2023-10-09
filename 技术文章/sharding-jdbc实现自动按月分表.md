# 1、基本需求

1、 项目中我们希望 能够按照时间、类别来添加表。但是sharding-jdbc 是固定配置 的 actual-data-nodes 参数。也就是说我们需要提前创建好分表或者分库。那么我们需要如何来实现动态创建表，并且动态刷新 actual-data-nodes 呢。

2、思路就是写个定时器来动态创建表 ，在创建表的时候 动态刷新 actual-data-nodes 实现动态创建表被shard加载。
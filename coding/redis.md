---
title: redis
date: 2023/08/07
categories:
  - coding
tags:
  - redis
  - 缓存中间件
  - 编程基础
---

# 1、基本概念

Redis诞生于2009年全称是Remote Dictionary Server，远程词典服务器，是一个基于内存的键值型NoSQL数据库。

## 1.1、redis特性

1. 键值（key-value）型，value支持多种不同数据结构，功能丰富
2. 单线程，每个命令具备原子性

> 为什么单线程？数据操作基于内存， 不会有太多的阻塞，单线程单实例完全可以适应几万的并发。并且使用单线程避免了线程上下文切换，和加锁解锁的过程。提升效率

3. 低延迟，速度快（原因：基于内存、IO多路复用、单线程）。
4. 支持数据持久化
5. 支持主从集群、分片集群
6. 支持多语言客户端

## 1.2、数据类型

> Redis是一个key-value的数据库，key一般是String类型，不过value的类型多种多样

![redis数据类型](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202308071403029.png)


# 2、redis常见命令
## 2.1、String

> String类型，也就是字符串类型，是Redis中最简单的存储类型
> 底层SDS结构。为什么不直接实用字符串？①C 语言字符数组最后一个元素总是 '\0'，而在Redis中\0可能会被判定为提前结束而识别不了字符串②获取字符串长度为O(n)，因为C字符串需要去遍历，开销较大，SDS对象有len属性直接获取

其value是字符串，不过根据字符串的格式不同，又可以分为3类：
（1）string：普通字符串
（2）int：整数类型，可以做自增、自减操作
（3）float：浮点类型，可以做自增、自减操作

| 命令 | 描述 |
| --- | --- |
| SET | 添加或者修改已经存在的一个String类型的键值对 |
| GET | 根据key获取String类型的value |
| MSET | 批量添加多个String类型的键值对 |
| MGET | 根据多个key获取多个String类型的value |
| INCR | 让一个整型的key自增1 |
| INCRBY | 让一个整型的key自增并指定步长，例如：incrby num 2 让num值自增2 |
| INCRBYFLOAT | 让一个浮点类型的数字自增并指定步长 |
| SETNX | 添加一个String类型的键值对，前提是这个key不存在，否则不执行 |
| SETEX | 添加一个String类型的键值对，并且指定有效期 |

> **Redis的key允许有多个单词形成层级结构，多个单词之间用” ：“隔开，格式如下：**

```java
项目名:业务名:类型:id
```
## 2.2、Hash

> Hash类型，也叫散列，底层是hashtable，其value是一个无序字典，类似于Java中的HashMap结构。

Hash结构可以将对象中的每个字段独立存储，可以针对单个字段做CRUD

| **命令** | **描述** |
| --- | --- |
| HSET key field value | 添加或者修改hash类型key的field的值 |
| HGET key field | 获取一个hash类型key的field的值 |
| HMSET | hmset 和 hset 效果相同 ，4.0之后hmset可以弃用了 |
| HMGET | 批量获取多个hash类型key的field的值 |
| HGETALL | 获取一个hash类型的key中的所有的field和value |
| HKEYS | 获取一个hash类型的key中的所有的field |
| HVALS | 获取一个hash类型的key中的所有的value |
| HINCRBY | 让一个hash类型key的字段值自增并指定步长 |
| HSETNX | 添加一个hash类型的key的field值，前提是这个field不存在，否则不执行 |

## 2.3、List

> list列表的数据结构使用的是压缩列表ziplist和普通的双向链表linkedlist组成。元素少的时候会用ziplist，元素多的时候会用linkedlist
  ziplist是一种压缩链表，它的好处是更能节省内存空间，因为它所存储的内容都是在连续的内存区域当中的,当数据量较大的时候因为需要重新分配，开销较大

常用来存储一个有序数据，例如：朋友圈点赞列表，评论列表等

| **命令** | **描述** |
| --- | --- |
| LPUSH key element … | 向列表左侧插入一个或多个元素 |
| LPOP key | 移除并返回列表左侧的第一个元素，没有则返回nil |
| **RPUSH key element …** | 向列表右侧插入一个或多个元素 |
| RPOP key | 移除并返回列表右侧的第一个元素 |
| LRANGE key star end | 返回一段角标范围内的所有元素 |
| BLPOP和BRPOP | 与LPOP和RPOP类似，只不过在没有元素时等待指定时间，而不是直接返回nil |

## 2.4、SET

> Redis的Set结构与Java中的HashSet类似，可以看做是一个value为null的HashMap。因为也是一个hash表，因此具备与HashSet类似的特征。数据结构的底层实现有两种方式：Intset 和 Hashtable。当集合中的所有元素都是整数，并且元素数量较少时，Redis 会使用 Intset 作为底层实现。当集合中的元素不仅限于整数，或者元素数量较多时，Redis 会使用 Hashtable 作为底层实现
 

| **命令** | **描述** |
| --- | --- |
| SADD key member … | 向set中添加一个或多个元素 |
| SREM key member … | 移除set中的指定元素 |
| SCARD key | 返回set中元素的个数 |
| SISMEMBER key member | 判断一个元素是否存在于set中 |
| SMEMBERS | 获取set中的所有元素 |
| SINTER key1 key2 … | 求key1与key2的交集 |
| SDIFF key1 key2 … | 求key1与key2的差集 |
| SUNION key1 key2 … | 求key1和key2的并集 |

## 2.5、SortedSet

> Redis的SortedSet是一个可排序的set集合，与Java中的TreeSet有些类似，但底层数据结构却差别很大。SortedSet中的每一个元素都带有一个score属性，可以基于score属性对元素排序，底层的实现是一个跳表（SkipList）加 hash表。

因为SortedSet的可排序特性，经常被用来实现排行榜这样的功能

| **命令** | **描述** |
| --- | --- |
| ZADD key score member | 添加一个或多个元素到sorted set ，如果已经存在则更新其score值 |
| ZREM key member | 删除sorted set中的一个指定元素 |
| ZSCORE key member | 获取sorted set中的指定元素的score值 |
| ZRANK key member | 获取sorted set 中的指定元素的排名 |
| ZCARD key | 获取sorted set中的元素个数 |
| ZCOUNT key min max | 统计score值在给定范围内的所有元素的个数 |
| ZINCRBY key increment member | 让sorted set中的指定元素自增，步长为指定的increment值 |
| ZRANGE key min max | 按照score排序后，获取指定排名范围内的元素 |
| ZRANGEBYSCORE key min max | 按照score排序后，获取指定score范围内的元素 |
| ZDIFF、ZINTER、ZUNION | 求差集、交集、并集 |

> **注意：所有的排名默认都是升序，如果要降序则在命令的Z后面添加REV即可**


# 3、java客户端
## 3.1、springboot整合redis
### 3.1.1、引入依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
### 3.1.2、redis基本配置

```properties
#redis基本配置
spring.redis.host=127.0.0.1
spring.redis.port=6379
```
### 3.1.3、redis固定配置

```java
@Configuration
public class RedisConfig {
    @Bean
    @SuppressWarnings("all")
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<String, Object>();
        // 配置连接池工厂
        template.setConnectionFactory(factory);

        // Jackson序列化配置
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        // 属性访问器为全部，作用域为全部
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        // 序列化输入类型必须是非final类型的
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);

        // String 的序列化
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

        // key采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        // hash的key也采用String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        // value序列化方式采用jackson
        template.setValueSerializer(jackson2JsonRedisSerializer);
        // hash的value序列化方式采用jackson
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();
        return template;
    }
}
```


> redis使用JDK提供的序列化功能。 优点是反序列化时不需要提供类型信息(class)，但缺点是需要实现Serializable接口，还有序列化后的结果非常庞大，是JSON格式的5倍左右，这样就会消耗redis服务器的大量内存
> 所以我们需要  使用Jackson库将对象序列化为JSON字符串。优点是速度快，序列化后的字符串短小精悍，易读

### 3.1.4、工具类


```java
package com.kaka.redis;

import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Component;
import org.springframework.util.CollectionUtils;

import javax.annotation.Resource;
import java.util.Collection;
import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.concurrent.TimeUnit;

@Component
public final class RedisUtils {

    @Resource
    private RedisTemplate<String, Object> redisTemplate;


    public Set<String> keys(String keys){
        try {
            return redisTemplate.keys(keys);
        }catch (Exception e){
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 指定缓存失效时间
     * @param key 键
     * @param time 时间(秒)
     * @return
     */
    public boolean expire(String key, long time) {
        try {
            if (time > 0) {
                redisTemplate.expire(key, time, TimeUnit.SECONDS);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * 根据key 获取过期时间
     * @param key 键 不能为null
     * @return 时间(秒) 返回0代表为永久有效
     */
    public long getExpire(String key) {
        return redisTemplate.getExpire(key, TimeUnit.SECONDS);
    }
    /**
     * 判断key是否存在
     * @param key 键
     * @return true 存在 false不存在
     */
    public boolean hasKey(String key) {
        try {
            return redisTemplate.hasKey(key);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * 删除缓存
     * @param key 可以传一个值 或多个
     */
    @SuppressWarnings("unchecked")
    public void del(String... key) {
        if (key != null && key.length > 0) {
            if (key.length == 1) {
                redisTemplate.delete(key[0]);
            } else {
                redisTemplate.delete((Collection<String>) CollectionUtils.arrayToList(key));
            }
        }
    }
    /**
     * 普通缓存获取
     * @param key 键
     * @return 值
     */
    public Object get(String key) {
        return key == null ? null : redisTemplate.opsForValue().get(key);
    }
    /**
     * 普通缓存放入
     * @param key 键
     * @param value 值
     * @return true成功 false失败
     */
    public boolean set(String key, Object value) {
        try {
            redisTemplate.opsForValue().set(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * 普通缓存放入, 不存在放入，存在返回
     * @param key 键
     * @param value 值
     * @return true成功 false失败
     */
    public boolean setnx(String key, Object value) {
        try {
            redisTemplate.opsForValue().setIfAbsent(key,value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * 普通缓存放入并设置时间
     * @param key 键
     * @param value 值
     * @param time 时间(秒) time要大于0 如果time小于等于0 将设置无限期
     * @return true成功 false 失败
     */
    public boolean set(String key, Object value, long time) {
        try {
            if (time > 0) {
                redisTemplate.opsForValue().set(key, value, time, TimeUnit.SECONDS);
            } else {
                set(key, value);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 普通缓存放入并设置时间,不存在放入，存在返回
     * @param key 键
     * @param value 值
     * @param time 时间(秒) time要大于0 如果time小于等于0 将设置无限期
     * @return true成功 false 失败
     */
    public boolean setnx(String key, Object value, long time) {
        try {
            if (time > 0) {
                redisTemplate.opsForValue().setIfAbsent(key, value, time, TimeUnit.SECONDS);
            } else {
                set(key, value);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 递增
     * @param key 键
     * @param delta 要增加几(大于0)
     * @return
     */
    public long incr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递增因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }
    /**
     * 递减
     * @param key 键
     * @param delta 要减少几(小于0)
     * @return
     */
    public long decr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递减因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, -delta);
    }
    /**
     * HashGet
     * @param key 键 不能为null
     * @param item 项 不能为null
     * @return 值
     */
    public Object hget(String key, String item) {
        return redisTemplate.opsForHash().get(key, item);
    }
    /**
     * 获取hashKey对应的所有键值
     * @param key 键
     * @return 对应的多个键值
     */
    public Map<Object, Object> hmget(String key) {
        return redisTemplate.opsForHash().entries(key);
    }
    /**
     * HashSet
     * @param key 键
     * @param map 对应多个键值
     * @return true 成功 false 失败
     */
    public boolean hmset(String key, Map<String, Object> map) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * HashSet 并设置时间
     * @param key 键
     * @param map 对应多个键值
     * @param time 时间(秒)
     * @return true成功 false失败
     */
    public boolean hmset(String key, Map<String, Object> map, long time) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * 向一张hash表中放入数据,如果不存在将创建
     * @param key 键
     * @param item 项
     * @param value 值
     * @return true 成功 false失败
     */
    public boolean hset(String key, String item, Object value) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * 向一张hash表中放入数据,如果不存在将创建
     * @param key 键
     * @param item 项
     * @param value 值
     * @param time 时间(秒) 注意:如果已存在的hash表有时间,这里将会替换原有的时间
     * @return true 成功 false失败
     */
    public boolean hset(String key, String item, Object value, long time) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * 删除hash表中的值
     * @param key 键 不能为null
     * @param item 项 可以使多个 不能为null
     */
    public void hdel(String key, Object... item) {
        redisTemplate.opsForHash().delete(key, item);
    }
    /**
     * 判断hash表中是否有该项的值
     * @param key 键 不能为null
     * @param item 项 不能为null
     * @return true 存在 false不存在
     */
    public boolean hHasKey(String key, String item) {
        return redisTemplate.opsForHash().hasKey(key, item);
    }
    /**
     * hash递增 如果不存在,就会创建一个 并把新增后的值返回
     * @param key 键
     * @param item 项
     * @param by 要增加几(大于0)
     * @return
     */
    public double hincr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, by);
    }
    /**
     * hash递减
     * @param key 键
     * @param item 项
     * @param by 要减少记(小于0)
     * @return
     */
    public double hdecr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, -by);
    }
    /**
     * 根据key获取Set中的所有值
     * @param key 键
     * @return
     */
    public Set<Object> sGet(String key) {
        try {
            return redisTemplate.opsForSet().members(key);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
    /**
     * 根据value从一个set中查询,是否存在
     * @param key 键
     * @param value 值
     * @return true 存在 false不存在
     */
    public boolean sHasKey(String key, Object value) {
        try {
            return redisTemplate.opsForSet().isMember(key, value);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * 将数据放入set缓存
     * @param key 键
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSet(String key, Object... values) {
        try {
            return redisTemplate.opsForSet().add(key, values);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }
    /**
     * 将set数据放入缓存
     * @param key 键
     * @param time 时间(秒)
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSetAndTime(String key, long time, Object... values) {
        try {
            Long count = redisTemplate.opsForSet().add(key, values);
            if (time > 0){
                expire(key, time);
            }
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }
    /**
     * 获取set缓存的长度
     * @param key 键
     * @return
     */
    public long sGetSetSize(String key) {
        try {
            return redisTemplate.opsForSet().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }
    /**
     * 移除值为value的
     * @param key 键
     * @param values 值 可以是多个
     * @return 移除的个数
     */
    public long setRemove(String key, Object... values) {
        try {
            Long count = redisTemplate.opsForSet().remove(key, values);
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }
    // ===============================list=================================
    /**
     * 获取list缓存的内容
     * @param key 键
     * @param start 开始
     * @param end 结束 0 到 -1代表所有值
     * @return
     */
    public List<Object> lGet(String key, long start, long end) {
        try {
            return redisTemplate.opsForList().range(key, start, end);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
    /**
     * 获取list缓存的长度
     * @param key 键
     * @return
     */
    public long lGetListSize(String key) {
        try {
            return redisTemplate.opsForList().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }
    /**
     * 通过索引 获取list中的值
     * @param key 键
     * @param index 索引 index>=0时， 0 表头，1 第二个元素，依次类推；index<0时，-1，表尾，-2倒数第二个元素，依次类推
     * @return
     */
    public Object lGetIndex(String key, long index) {
        try {
            return redisTemplate.opsForList().index(key, index);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
    /**
     * 将list放入缓存
     * @param key 键
     * @param value 值
     * @return
     */
    public boolean lSet(String key, Object value) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * 将list放入缓存
     * @param key 键
     * @param value 值
     * @param time 时间(秒)
     * @return
     */
    public boolean lSet(String key, Object value, long time) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            if (time > 0){
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * 将list放入缓存
     * @param key 键
     * @param value 值
     * @return
     */
    public boolean lSet(String key, List<Object> value) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * 将list放入缓存
     *
     * @param key 键
     * @param value 值
     * @param time 时间(秒)
     * @return
     */
    public boolean lSet(String key, List<Object> value, long time) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            if (time > 0){
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * 根据索引修改list中的某条数据
     * @param key 键
     * @param index 索引
     * @param value 值
     * @return
     */
    public boolean lUpdateIndex(String key, long index, Object value) {
        try {
            redisTemplate.opsForList().set(key, index, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
    /**
     * 移除N个值为value
     * @param key 键
     * @param count 移除多少个
     * @param value 值
     * @return 移除的个数
     */
    public long lRemove(String key, long count, Object value) {
        try {
            Long remove = redisTemplate.opsForList().remove(key, count, value);
            return remove;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }
}
```

5. 使用

```java
@RestController
public class Demo {
    @Autowired
    RedisUtils redisUtils;
    @RequestMapping("/redisTest")
    public String test(){
        redisUtils.set("test3:key3","hello,redis");
        return redisUtils.get("test3:key3").toString();
    }
}
```

# 4、进阶
## 4.1、redis持久化
### 4.1.1、rdb

把当前内存中的快照写入磁盘

（1）save：save指令执行会阻塞当前redis服务器，直到当前rdb过程执行完，可能造成长时间阻塞，线上环境不建议使用
（2）bgsave：调用fork函数生成子进程，解决了save的阻塞问题
（3）自动执行：（redis配置文件中配置）save 900 1   save 300 10   save 60 1000

### 4.1.2、aof

以日志的方式记录每次写命令，重启时再执行aof中的命令达到数据恢复的目的（是目前redis持久化的主流方式）

aof写数据策略：

（1）always：服务器每写入一个命令,就调用一次fdatasync（不会丢失数据）
（2）Everysec：服务器每一秒重调用一次fdatasync（数据同步），最多丢失1秒的数据
（3）NO：操作系统决定任何将缓冲区里面的命令写入磁盘里面，数据丢失量是不确定的

> 注：always策略持久化数据：先把写命令追加到aof buffer中，下一次进入事件循环循环后，再将buffer写到磁盘上。也就是说，这次写到磁盘上的内容是上一个事件循环产生的所以，即使设置为always，也会丢失一个循环的数据


### 4.1.3、对比

|  | rdb | aof |
| --- | --- | --- |
| 占用存储空间 | 小（数据级） | 大（指令级） |
| 恢复速度 | 快 | 慢（需要执行指令） |
| 数据安全性 | 可能会丢失最后一次持久化后的数据 | 根据策略决定 |

## 4.2、redis淘汰策略

### 4.2.1、定时删除
方式：创建一个定时器,当设置的key到达到期时间时,由定时器任务立即执行对key的删除操作

优缺点：
（1）节约内存,到时就删,快速释放掉不必要的内存占用 
（2）CPU压力变大,无论CPU此时负载量多高,均占用CPU

### 4.2.2、惰性删除
方式：数据到期时不做删除,等下次访问时进行删除

优缺点：
（1）节约cpu性能  
（2）若大量的key在超出超时时间后，很久一段时间内，都没有被获取过，那么可能发生内存泄露（无用的垃圾占用了大量的内存）

### 4.2.3、定期删除
方式：每隔一段时间主动检查一批过期键，并将其删除。这样可以保证过期键及时地从内存中释放

>（1）Redis 默认每秒进行 10 次过期扫描，此配置可通过 Redis 的配置文件 redis.conf 进行配置，配置健为
 hz 它的默认值是 hz 10。
 【注意】:Redis 每次扫描并不是遍历过期字典中的所有健，而是采用随机抽取判断并删除过期健的形式执行的。

删除流程：
（1）从过期字典随机取20个键
（2）删除这20个键中过期的键
（3）如果过期key的比例超过25%，重复步骤1

优缺点：
（1）分批处理，以避免对cpu产生过大的负载

## 4.3、redis内存淘汰策略

Redis的内存淘汰策略是指在Redis的用于缓存的内存不足时，怎么处理需要新写入且需要申请额外空间的数据

>LRU：淘汰最长时间没有被使用的
 LFU：一定时间内使用频次越低的
 random：随机
 ttl：越早过期的数据
 
|淘汰策略名称|策略含义|
|---|---|
|noeviction|默认策略，不淘汰数据；大部分写命令都将返回错误（DEL等少数除外）|
|allkeys-lru|从所有数据中根据 LRU 算法挑选数据淘汰|
|volatile-lru|从设置了过期时间的数据中根据 LRU 算法挑选数据淘汰|
|allkeys-random|从所有数据中随机挑选数据淘汰|
|volatile-random|从设置了过期时间的数据中随机挑选数据淘汰|
|volatile-ttl|从设置了过期时间的数据中，挑选越早过期的数据进行删除|
|allkeys-lfu|从所有数据中根据 LFU 算法挑选数据淘汰（4.0及以上版本可用）|
|volatile-lfu|从设置了过期时间的数据中根据 LFU 算法挑选数据淘汰（4.0及以上版本可用）|

  
  

7、mq  

7.1、mq的用处和缺点  
优势：  
（1）应用解耦:eg：订单系统不直接调用库存系统，库存系统宕机并不影响下单  
（2）异步提速：eg：订单系统不需要同步调用库存系统，提升速度  
（3）削峰填谷：eg：mq承载了5000请求，系统慢慢消费，就是削峰。但是因为消息积压，高峰过一段时间后消息才能被消费完，这就是填谷。  
劣势：  
（1）系统可用性降低：一旦mq宕机，就可能对多个业务造成影响。如何保证mq高可用  
（2）系统复杂度提高：如何保证消息不被重复消费？怎么处理丢失情况？怎么保证消息传递的顺序性？  
（3）一致性问题：A系统给B,C,D系统发送数据，如果B，c处理成功，D系统失败。如何保证消息数据处理的一致性  
  

7.2、rabbitmq组件  
  

![image.png](https://cdn.nlark.com/yuque/0/2023/png/2996398/1675740684760-d44e0791-b1da-483d-9274-a2e68714e7c7.png)

  
  
（1）broker（服务端）：接收客户端的连接，实现AMQP实体服务。  
（2）Connection：连接，应用程序与Server的网络连接，TCP连接。  
（3）Channel：信道，消息读写等操作在信道中进行。客户端可以建立多个信道，每个信道代表一个会话任务。  
如果每一次访问 RabbitMQ 都建立一个 Connection，在消息量大的时候建立 TCP Connection 的开销将是巨大的，效率也较低。Channel 是在 connection 内部建立的逻辑连接  
Channel 作为轻量级的Connection极大减少了操作系统建立TCP connection的开销  
（4）Virtual Host：虚拟主机，用于逻辑隔离。一个虚拟主机里面可以有若干个Exchange和Queue，同一个虚拟主机里面不能有相同名称的Exchange或Queue。  
（5）Exchange：交换器，接收消息，按照路由规则将消息路由到一个或者多个队列。如果路由不到，或者返回给生产者，或者直接丢弃。RabbitMQ常用的交换器常用类型有direct、topic、fanout、headers四种，后面详细介绍。  
（6）Queue：消息队列，用来保存消息，供消费者消费  
（7）Message：消息，应用程序和服务器之间传送的数据，消息可以非常简单，也可以很复杂。有Properties和Body组成。Properties为外包装，可以对消息进行修饰，比如消息的优先级、延迟等高级特性；Body就是消息体内容。  
（8）Binding：绑定，交换器和消息队列之间的虚拟连接，绑定中可以包含一个或者多个RoutingKey。  
（9）RoutingKey：路由键，生产者将消息发送给交换器的时候，会发送一个RoutingKey，用来指定路由规则，这样交换器就知道把消息发送到哪个队列。路由键通常为一个“.”分割的字符串，例如“com.rabbitmq”  
  

7.3、rabbitMQ交换机类型  
1Direct Exchange（直连交换机）  
根据Routing Key(路由键)进行投递到不同队列。如果路由键不匹配，那么就不会发送到任何队列中去。  
  

  
  
2Fanout Exchange  
该类型的交换机会将⼀条消息⼴播到绑定到该交换机的所有队列上，不论你设置的路由键是什么  
如果想让多个消费者消费到数据必须不指定queues，指定交换机  

  
3. Topic Exchange（主题交换机）  
将路由键和某模式进行匹配。此时队列需要绑定要一个模式上。符号“#”匹配一个或多个词，符号“*”匹配不多不少一个词。因此“abc.#”能够匹配到“abc.def.ghi”，但是“abc.*  ” 只会匹配到“abc.def”。  

  
  
4Headers Exchanges（头交换机）  
  
与routingKey无关，匹配机制是匹配消息头中的属性信息。在绑定消息队列与交换机之前声明一个map键值对，通过这个map对象实现消息队列和交换机的绑定。当消息发送到RabbitMQ时会取到该消息的headers与Exchange绑定时指定的键值对进行匹配；如果完全匹配则消息会路由到该队列，否则不会路由到该队列  
匹配规则x-match有下列两种类型： x-match = all ：表示所有的键值对都匹配才能接受到消息 x-match = any ：表示只要有键值对匹配就能接受到消息  

  
  

7.4、rabbitMQ工作模式  
简单模式和工作队列模式都是点对点模式。其他的都是发布与订阅模式  
1简单模式  
一个生产者对应一个消费者。这个模式下一个发生一个接收，不用考虑交换机。  

  
P代表生产者，C代表消费者，红色代表消息队列。P将消息发送到消息队列，C对消息进行处理  

  
2工作队列模式  
一个生产者对应多个消费者，但是一条消息只能有一个消费者获得消息。谁先拿到谁消费  
对于任务过重或任务较多情况使用工作队列可以提高任务处理的速度  
  

  
3发布订阅模式  
生产者将消息发给broker，由交换机将消息转发到绑定此交换机的每个队列，每个绑定交换机的队列都将接收到消息  

  

4路由模式  
生产者发送的消息是以key-value的形式，当消息进入交换机，交换机根据key的不同，将其分配到不同的队列。消费者通过Channel声明一个队列时，需要绑定给队列绑定一个key和一个交换机。  

  

  
5主题模式  
主题模式从某种意义上也算是一种路由，只不过它可以匹配多种符合条件的队列。Topic类型Exchange可以让队列在绑定Routing key 的时候使用通配符  
*可以代替一个单词，#代表没有和多个单词  

  

  

7.5、如何保证消息不丢失？  
1生产者->broker  
confirmCallback 确认模式。(无论成功失败都有返回)  
（1）在配置文件中开启消息确认模式  
（2）通过实现 RabbitTemplate.ConfirmCallback 类来对消息发送结果进行处理  
2broker中  
消息回退：消息从 exchange 到 queue 投递失败有一个 returnCallback 退回模式。（失败时才会有返回）  
rabbitTemplate.setMandatory(true)  
持久化：保证重启过程中，交换机和队列也是持久化的  
3broker ->消费者  
消费者端消息接收确认采用的是ack模式。ACK机制是消费者从RabbitMQ收到消息并处理完成后，反馈给RabbitMQ，RabbitMQ收到反馈后才将此消息从队列中删除 （1）rabbitmq默认的是自动ack，无需添加其他配置 若正常消费成功了，则会自动返回确认ack给队列，队列收到后即可将消息移除。 若消费过程中出现异常，则超过ack心跳时间，会触发重试消费  
（2）手动ack 开启方式简单，只需要放开此配置即可  
acknowledge-mode: manual #设置消费端手动 ack  

7.6、如何保证消息的顺序性  
出现无序的场景：一个队列有多个消费者消费的时候，这些消息被多个线程并发消费，并发是不能保证顺序的  
1mq层面：拆分多个 queue(消息队列)，每个 queue(消息队列) 一个 consumer(消费者)  
2应用层层面：消息实体中增加：版本号 & msgid & parent_msgid，通过 parent_msgid 判断消息的顺序（需要全局存储，记录消息的执行状态）  

7.7、死信队列  
死信，在官网中对应的单词为“Dead Letter”，可以看出翻译确实非常的简单粗暴。那么死信是个什么东西呢？  
  
“死信”是RabbitMQ中的一种消息机制，当你在消费消息时，如果队列里的消息出现以下情况：  
（1）消息被否定确认，使用 channel.basicNack 或 channel.basicReject ，并且此时requeue 属性被设置为false。  
（2）消息在队列的存活时间超过设置的生存时间（TTL)时间。  
（3）消息队列的消息数量已经超过最大队列长度。  
那么该消息将成为“死信”。  
“死信”消息会被RabbitMQ进行特殊处理，如果配置了死信队列信息，那么该消息将会被丢进死信队列中，如果没有配置，则该消息将会被丢弃。  
  

7.8、如何实现延迟队列（DLX 实现）  
实现思路： 我们可以把死信队列就当成延迟队列  
假如一条消息需要延迟 30 分钟执行，我们就设置这条消息的有效期为 30 分钟，同时为这条消息配置死信交换机和死信 routing_key，并且不为这个消息队列设置消费者，那么 30 分钟后，这条消息由于没有被消费者消费而进入死信队列，此时我们有一个消费者就在“蹲点”这个死信队列，消息一进入死信队列，就立马被消费了。  
  

7.9、emqx & MQTT  
mqtt 和 emqx 的关系类比于amqp和 rabbitmq，是协议和实现的关系  
mqtt开销很小，协议交换最小化，以降低网络流量。所以非常在物联网领域（嵌入式设备的运算能力和带宽都相对薄弱）  
  

7.10、如何保证消息不被重复消费（幂等性）  
场景：消息消费成功，事务已经提交，ack时，机器宕机，导致没有ack成功，重新发送，并发送给其他消费者；相当于锁库存被消费了两遍，即库存扣了两遍；  
  
所谓幂等性，就是数据无论操作多少次，所产生的影响跟执行一次是一样的，比如对于读操作来说，无论读取多少次数据，都跟读取一次的数据是一样的，所以读操作是一个幂等性操作，而添加操作，添加多次会有多条记录，因而写操作则是非幂等性操作  
可以通过给消息的某一些属性设置唯一约束，比如增加唯一uuid，添加的时候查询是否存对应的uuid，存在不操作，不存在则添加，那样对于相同的uuid只会存在一条数据。（注：但需要保证查询跟添加的操作必须是原子性操作，可以使用setnx保证原子）  

  

7.11、MQTT  
MQTT是一种轻量级的、基于发布/订阅模式的消息传输协议，主要用于物联网.MQTT协议的设计非常简单,所以带宽的开销很小，很适合小型的嵌入式设备。MQTT显示上没有rabbitmq中交换机，队列的概念，生产者将消息发布到一个主题（Topic）中，而消费者则订阅该主题，以便接收相应的消息，从而实现消息的传递和交互  

8、网络  

8.1、长连接和短连接的区别？  
HTTP的长连接和短连接本质上是TCP长连接和短连接  
长连接： 客户端和服务端建立连接后不进行断开，之后客户端再次访问这个服务器上的内容时，继续使用这一条连接通道。直到服务器超时自动断开链接，或者客户端主动断开链接  
短连接： 客户端和服务端建立连接，发送完数据后立马断开连接。下次要取数据，需要再次建立连接。  
在HTTP/1.0中，默认使用的是短连接。但从 HTTP/1.1起，默认使用长连接。  
Http长连接 和 TCP长连接的区别在于: TCP 的长连接需要自己去维护一套心跳策略。，而Http只需要在请求头加入keep-alive:true即可实现长连接  
  

8.2、cookie和session的区别？  
1存储位置：cookie存储在客户端浏览器中，session存储在服务器端。  
2安全性：cookie可以被客户端修改和窃取，因此不适合存储敏感信息。而session存储在服务器端，只有服务器可以访问它，因此更加安全。  
3存储容量：cookie的存储容量有限，通常只能存储少量的数据。而session可以存储大量的数据，因为它存储在服务器端。（具体容量看设置，cookie默认4kb，session几十兆）  
4过期时间：cookie可以设置过期时间，当过期时，浏览器会自动删除cookie。而session在用户关闭浏览器时会自动过期。  
总的来说，cookie适合用于存储少量的非敏感信息，如用户偏好设置、购物车信息等；而session适合用于存储大量的敏感信息，如用户登录状态、权限等  

9、dubbo  

9.1、dubbo和springCloud区别  
两者都是现在主流的分布式框架，但却存在不少差异：  
●生态环境不同： SpringCloud定位为微服务架构下的一站式解决方案（网关，分布式配置，服务跟踪）；Dubbo 是 SOA 时代的产物，它的关注点主要在于服务的调用和治理  
●调用方式： SpringCloud是采用Http协议做远程调用；Dubbo是基于RPC调用  
●组件差异比较多，例如SpringCloud注册中心一般用Eureka，而Dubbo用的是Zookeeper  

  
  

9.2、dubbo支持哪些协议  
1、dubbo 默认协议：  
●单一 TCP 长连接，Hessian 二进制序列化和 NIO 异步通讯  
●不适合传送大数据包的服务  
2、rmi 协议：  
●采用 JDK 标准的 java.rmi.* 实现，采用阻塞式短连接和 JDK 标准序列化方式  
●对传输数据包不限，消费者和传输者个数相当  
3、hessian 协议：  
●底层 Http 通讯，Servlet 暴露服务，Dubbo 缺省内嵌 Jetty 作为服务器实现  
●通讯效率高于 WebService 和 Java 自带的序列化  
●适用于传输数据包较大，提供者比消费者个数多，提供者压力较大  
4、http 协议：  
●基于 http 表单的远程调用协议，短连接，json 序列化  
●对传输数据包不限，不支持传文件  
5、webservice 协议：  
●基于 Apache CXF 的 frontend-simple 和 transports-http 实现，短连接，SOAP文本序列化  
●可与原生 WebService 服务互操作  
●适用于系统集成、跨语言调用  
6、thrift 协议：  
●对 thrift 原生协议 [2] 的扩展添加了额外的头信息  
●使用较少，不支持传 null 值  
7、基于 Redis实现的 RPC 协议  
8、基于 Memcached 实现的 RPC 协议】  
  

9.3、dubbo负载均衡策略  

也可以在注解上进行配置  
@Service(version = "${product.service.version}",loadbalance="roundrobin")  
1RandomLoadBalance:随机负载均衡。随机的选择一个。是Dubbo的默认负载均衡策略。  
2RoundRobinLoadBalance:轮询负载均衡。轮询选择一个。  
3LeastActiveLoadBalance: 最小活跃数负载均衡，活跃数也就是dubbo的连接数，每当收到一个请求活跃数+1，结束请求活跃数-1，假设如果多台机器的连接数是相同的，如果一台机器性能比较好，处理请求比较快那么活跃数减少的就快，活跃数就少。所以活跃数少的就会获取到的请求会变多，这样就可以合理的使用性能不同的机器了  
4ConsistentHashLoadBalance:一致性哈希负载均衡。相同参数的请求总是落在同一台机器上。  
  

9.4、dubbo集群容错  

|   |   |   |
|---|---|---|
|类型|负载均衡|备注|
|failover|会|失败后会尝试调用其他服务器实例，默认尝试2次， 可以通过设置retries来设置次数。这是dubbo默认的容错机制，由于常常可能因为超时待原因触发异常但远程服务已经完成操作，所以这个类型不应就在数据更新的操作|
|failfast|会|有异常立即返回，不做尝试，我认为可以用在数据更新上，以保证数据的一致性|
|fastsafe|会|有异常会直接忽略，为的是保证调用方接下来的正常运行，一般用于日志收集等与正常流程无关的操作|
|failback|会|失败后会将任务丢到失败队列中，并会异步再次尝试|
|forking|否|同时调用多个服务，取最先返回的结果，可以通过forks设置最大并行数，这比较浪费资源|
|broadcast|否|调用所有可用的服务，任意一个有错都会返回异常|
|Mock|否|调用失败时返回伪造的响应结果|

一般在@DubboService或@DubboReference指定cluster即可，如 @DubboService(cluster = “failover”) //默认重试2次  
  

9.5、dubbo节点角色  
1Container：服务运行容器。  
2Provider：暴露服务的服务提供方。  
3Consumer：调用远程服务的服务消费方。  
4Registry：服务注册与发现的注册中心。  
5Monitor：统计服务的调用次数和调用时间的监控中心。  

  

9.5、dubbo调用过程  
1服务容器 Container 负责启动加载运行服务提供者 Provider。根据配置中的 Registry 地址连接 Registry，在 Registry 注册自己提供的服务。  
2Consumer 在启动时，根据配置文件中的服务引用信息，连接到 Registry，向 Registry 订阅自己所需的服务。  
4Registry 根据服务订阅关系，返回 Provider 地址列表给 Consumer。如果有变更，Registry 会基于长连接推送最新的服务地址信息给 Consumer。  
5Consumer 调用远程服务时，基于负载均衡算法，从缓存的 Provider 地址列表中选择一台进行跨进程调用服务  
6服务 Provider 和 Consumer，会在内存中记录调用次数和调用时间，每分钟发送一次统计数据到 Monitor。  
  

10、springCloud  

10.1、如何保证分布式事务一致性？  
1首先是设计方案尽可能规避分布式事务的场景（相似的业务放在一起，不要过度的拆分）  
2根据业务场景，选择使用柔性事务（ap）还是强事务(cp)  
如果可以允许消息存在一段时间不一致，只要保证最终一致性，可以用本地消息表来做。如果要保证一致性，可以用2pc，具体实现方案有阿里的seata  
3本地消息表（柔性事务）  
不去同步的调用，先将要请求的消息插入到本地的消息表中，消息状态为正在处理，起一个定时任务去查询消息表，将正在处理的消息发送到消息队列，B中消息处理完后，向一个return队列发送一个成功的消息，A订阅了该消息队列，收到成功的消息后将状态该为处理完毕。（被调用方应保证幂等性，如库存系统在减库存前先查流水表，看该订单是否扣过库存，扣过就不执行）  

  

  
  

10.2、架构的演进  
单体-垂直-分布式-SOA-微服务 （详情看springcloud笔记开头部分）  
  

10.3、分布式和微服务有什么关系？  
我理解的分布式是一种系统架构，指一个系统由多个独立的组件组成，这些组件可以在不同的物理位置上运行，从而提升了系统的性能  
而微服务可以看作是分布式的一种实现方案。分布式实现方案有soa，基于rpc远程调用（dubbo），微服务（springcloud）  
  

11、场景题  

11.1、两个50亿url的文件如何找出共同的url  
问题：给A，B两个文件，各存放50亿条URL，每条URL占用64个字节，内存限制为4G，找出A，B中相同的URL。  
  
分析：可以估计每个文件安的大小为50G×64=320G，远远大于内存限制的4G。所以不可能将其完全加载到内存中处理。考虑采取分而治之的方法  
  
方案：分治思想  
1分治思想遍历文件A，对每个url求hash(url)%N，然后将取得的值分布到N个小文件中，每个小文件可以放在内存中。  
2遍历文件B，采取和A相同的方式将url存储到N个小文件中.  
3分布取两个小文件，把一个小文件加入hashset，然后遍历另一个，如果url在哈希表，则为共同url，汇总到结果中。  
有可能出现哈希碰撞，可以采用多个哈希函数减少碰撞概率。  
  

11.2、如何从1000w记录中，找出最热门的10个记录？  
搜索引擎会通过日志文件把用户每次检索使用的所有检索串都记录下来，每个查询串的长度为1-255字节。假设目前一个日志文件中有一千万个记录 (这些查询串的重复度比较高，虽然总数是1千万，但如果除去重复后，不超过了百万个。一个查询串的重复度越高，说明查询它的用户越多，也就是越热门) ，请你统计最热门的1口个查询串，要求使用的内存不能超过1G  
  
1000万条记录，每条记录最大为255Byte，那么日志文件最大有2.5G左右（超过1G）  
如果去重复有100万条记录，占用250M内存。那么我们可以考虑将这些无重复的记录装入内存  
这时我们需要一种数据结构，这种数据结构即能够存储查询串，又能存储查询串的出现次数。  
    第一步、先对这批海量数据预处理，用hashmap存储，key为内容，value为出现次数 。O（N）  
第二步、创建一个长度为10的小根堆，遍历hashmap，如果MinHeap未满，那么往MinHeap中插入这个键值对，如果MinHeap满了，则比较遍历到的元素的count值堆顶的count，如果遍历到元素的count大于堆顶count值，删除堆顶元素，插入当前遍历到的元素。遍历完整个hashmap以后，在MinHeap中存储的就是最热门10个查询串  
  

11.3、20亿个用户统计登录状态  
20亿的用户，频繁改变数据库，io极大，数据库性能可能会被拖垮。  
考虑使用redis，如果用set存储，登录存入set，退出删除。一个用户按4字节，10亿个用户4G内存，太大  
我们使用redis的bitmap来进行存储。  
setbit命今 语法:setbit key offset value 设置或修改key上的偏移量 (offset) 的位 (value) 的值  
10亿用户，1个用户只占用1bit 相比于set占用的4字节，优化了了32倍那么节省后的内存为125MB。  
优点:查找效率高。  
缺点: 结果数据不能重复，数据如果太过分散会造成浪费，只有数据密集才可以  
  

12、数据结构和算法  

12.1、堆  
1定义：堆通常可以被看做是一棵完全二叉树的数组对象  
[完全二叉树](https://so.csdn.net/so/search?q=%E5%AE%8C%E5%85%A8%E4%BA%8C%E5%8F%89%E6%A0%91&spm=1001.2101.3001.7020)，除了树的最后一层结点不需要是满的，其它的每一层从左到右都是满的，如果最后一层结点不是满的，那么要求节点都靠左  
  
2特点  
（1）它是完全二叉树  
（2）它通常用数组来实现。具体方法就是将二叉树的结点按照层级顺序放入数组中  
（3）堆中树的某个结点的父结点与左右子结点对应数组的索引关系：如果一个结点的位置为k，则它的父结点的位置为k/2,而它的两个子结点的位置则分别为2k和2k+1。这样，在不使用指针的情况下，我们也可以通过计算数组的索引在树中上下移动  
（4）每个结点都大于等于它的两个子结点。这里要注意堆中仅仅规定了每个结点大于等于它的两个子结点，但这两个子结点的顺序并没有做规定，跟我们之前学习的二叉查找树是有区别的  
  
3插入与删除的实现  
插入：利用上浮算法进行数据插入，上浮算法就是将待上浮的结点与其父结点比较，如果待上浮的结点大于父结点，则交换位置，循环往复，直至待上浮的结点没有父结点。因此，将待插入的结点放到数组的最后一个元素，然后将插入的结点作为待上浮的结点，通过上浮算法将其放到合适的位置。  
删除：下沉算法就是通过将待下沉的结点k，与其左子结点2k和右子结点2k+1中较大的结点进行比较，如果待下沉的结点小于左子结点和右子结点中较大的结点，则交换位置，循环往复，直至待下沉的结点没有左右子结点即叶子节点时完成下沉  

  

13、设计模式  
1创建型模式：创建型模式关注对象的创建过程，主要包括简单工厂模式、工厂方法模式、抽象工厂模式、单例模式、建造者模式和原型模式。  
2结构型模式：结构型模式关注对象之间的组合和协作关系，主要包括适配器模式、桥接模式、组合模式、装饰者模式、外观模式、享元模式和代理模式。  
3行为型模式：行为型模式关注对象之间的交互和通信，主要包括模板方法模式、策略模式、命令模式、职责链模式、状态模式、观察者模式、中介者模式和访问者模式。  

13.1、单例模式  
1饿汉模式：在类加载时就完成了初始化，但是加载比较慢，获取对象比较快  

2懒汉模式：懒汉式是延时加载,他是在需要的时候才创建对象  

  

13.2、简单工厂模式  
简单工厂模式又称为静态工厂方法模式，它通过一个工厂类来创建不同类型的对象，客户端只需要知道具体产品的名称即可。  
简单工厂模式的优点是简单易用，但是如果需要添加新的产品类型，则需要修改工厂类的代码，违反了开闭原则  

13.3、工厂方法模式  
工厂方法模式通过定义一个抽象的工厂接口和多个具体的工厂类来创建不同类型的对象，每个具体的工厂类只负责创建一种产品类型。客户端只需要知道具体工厂类的名称即可。  
工厂方法模式的优点是更加灵活，可以更方便地添加新的产品类型，但是需要创建多个工厂类，增加了系统的复杂度。  

13.4、抽象工厂模式  
抽象工厂模式通过定义一个抽象的工厂接口和多个具体的工厂类来创建不同类型的对象，每个具体的工厂类负责创建一组相关的产品类型。客户端需要知道具体的工厂类和产品族的名称。  
抽象工厂模式的优点是可以创建一组相关的产品，而且更加灵活，但是需要创建多个工厂类和产品族，增加了系统的复杂度  

13.5、原型模式  
原型模式是一种创建型设计模式，它允许通过克隆现有对象来创建新对象，而不是通过实例化类来创建。这种模式适用于需要创建多个相似对象的情况，因为它可以减少对象创建的开销和复杂性  
原型模式的优点包括：提高了对象创建的效率，减少了重复的代码，简化了对象创建的过程。缺点是如果原型对象的属性较为复杂或包含引用类型的属性，则需要进行深度克隆，否则会影响对象的正确性。  

13.6、装饰者模式  
装饰者模式是一种结构型设计模式，它允许动态地向一个对象添加新的行为，同时又不影响其原有行为。这种模式的关键在于装饰者类和被装饰者类实现相同的接口，使得装饰者对象可以代替被装饰者对象，从而实现透明性。  
在装饰者模式中，有四个角色：  
1抽象组件（Component）  
2具体组件（ConcreteComponent）  
3抽象装饰者（Decorator）：实现抽象组件  
4具体装饰者（ConcreteDecorator）：继承抽象装饰者  
核心思想是：将对象的行为分离出来，使得每个行为都可以被单独地扩展和修改，而不会影响到其他行为。这种分离行为的方式是通过将行为封装到不同的类中，并将这些类组合起来形成一个对象链来实现  

13.7、代理模式  
代理模式是一种结构型设计模式，主要作用是在访问对象时引入一定程度的间接性，以便更好地控制访问和管理对象。  
Java中实现代理模式通常有两种方式：静态代理和动态代理  
静态代理是指在编译时就已经确定代理类和被代理类的关系，代理类和被代理类都必须实现相同的接口或者继承相同的父类。静态代理的优点是简单易懂，缺点是需要为每一个被代理类写一个代理类，增加了代码量  

动态代理是指在运行时动态生成代理类，不需要为每一个被代理类写一个代理类，可以节省代码量。Java中提供了两种动态代理机制：基于接口的动态代理和基于类的动态代理  

13.7、享元模式  
享元模式是一种结构型设计模式，它用于减少系统中对象的数量，从而提高系统的性能和效率。在享元模式中，多个对象共享相同的状态和数据，这些状态和数据通常是不可变的，因此可以被多个对象共享使用  
1抽象享元（Flyweight）：定义享元对象的接口，通常包含一个操作方法。  
2具体享元（ConcreteFlyweight）：实现抽象享元接口，包含内部状态和外部状态两部分，其中内部状态可以被共享，而外部状态需要在使用时传递给享元对象。  
3享元工厂（FlyweightFactory）：用于创建和管理享元对象，通常使用工厂模式来实现。  
在Java中，可以使用HashMap来存储享元对象，使用享元对象的属性作为HashMap的key，将享元对象作为HashMap的value。这样，当需要创建新的对象时，先在HashMap中查找是否已经存在相同属性的对象，如果存在，则返回已有的对象，否则创建新的对象并将其加入HashMap中  

  

13.8、策略模式  
策略模式是一种行为型设计模式，它允许选择算法的不同实现。在策略模式中，算法被封装在单独的类中，并且可以在运行时进行切换，以便在不同的情况下使用不同的算法  
在策略模式中，有三个角色：策略接口、具体策略类和上下文类  
上下文类包含一个策略接口的引用，并且可以在运行时切换具体策略类的实现  

  

13.9、观察者模式  
观察者模式是一种行为型设计模式，它定义了一种一对多的依赖关系，让多个观察者对象同时监听某一个主题对象，当主题对象发生变化时，所有依赖于它的观察者对象都会得到通知并自动更新  
实现观察者模式需要定义两个接口：主题接口和观察者接口。主题接口定义了注册、删除和通知观察者的方法，观察者接口定义了更新自己状态的方法  
最典型的应用场景就是消息订阅系统（如微博）  

14、Linux  

14.1、防火墙常用命令  
Firewalld和iptables都是Linux系统中常用的防火墙软件, Firewalld可以在运行时动态添加、删除和修改规则, 使用更加灵活  

  

14.2、进程号&端口号查看  

15、项目  

15.1、云+2.0系统
### 4.3.3、哨兵模式
**哨兵模式主要是在主从复制模式基础上，增加了自动化的故障恢复功能**。因为主从复制模式下，当master节点宕机后，虽然slave节点会升级成新的master节点，但其他的slave节点配置的master节点的ip也是需要变更的，在主从复制模式下，这种变更是需要人工手动去修改配置文件的。而哨兵模式则完美的解决了这一缺陷，实现了快速的自动恢复，不再需要人工修改

1. 搭建

（1）在服务器上搭建redis服务
（2）在redis目录下，创建哨兵的配置文件，文件名只能是sentinel.conf
（3）编辑sentinel.conf配置文件，设置哨兵的相关属性

```java
–后台运行
daemonize yes
–本机IP
bind 192.168.11.1
–sentinel端口号
port 26372
–指定pid文件
pidfile “/var/run/redis-sentinel.pid”
–指定log文件
logfile “/home/redis/redis/sentinel.log”
–指定工作目录
dir “/home/redis/redis”
–避免脚本重置，默认值yes
sentinel deny-scripts-reconfig yes
–监听节点，monitor 后面依次我需要监听的节点名、节点ip、节点端口、最后的2表示如果有两台服务器认定master已死，则宣传master死记。
sentinel monitor mymaster 192.168.11.152 7001 2
– 30秒连接不上mymaster 节点,则判断定mymaster 已死
sentinel down-after-milliseconds mymaster 30000
– 主备切换时，最多有多少个slave同时对新的master进行同步，这里设置为默认的1
sentinel parallel-syncs mymaster 1
– 3分钟同步没完成算超时
sentinel failover-timeout mymaster 180000
```
（4）先启动master再启动slave，再启动哨兵

### 4.3.4、集群模式
即使使用哨兵，redis每个实例也是全量存储，每个redis存储的内容都是完整的数据，浪费内存且有木桶效应。为了最大化利用内存，可以采用集群，就是分布式存储。即每台redis存储不同的内容。cluster是为了解决单机Redis容量有限的问题，将数据按一定的规则分配到多台机器，提高并发量

## 4.4、redis发布订阅机制

Redis 发布订阅（Pus/Sub）是一种消息通信模式：发送者通过 PUBLISH发布消息，订阅者通过 SUBSCRIBE 订阅接收消息或通过UNSUBSCRIBE 取消订阅。
发布者和订阅者属于客户端，Channel 是 Redis 服务端，发布者将消息发布到频道，订阅这个频道的订阅者则收到消息
> 我们可以使用专业的mq如rabbitmq等来实现这个功能，不过Redis也能实现相同的效果


### 4.4.1、实现

1. 客户端1订阅频道channle1

```java
SUBSCRIBE channel1
```

2. 客户端2在channel1频道发布消息

```java
PUBLISH channel1 "Redis PUBLISH test"
```

3. 客户端1就会收到该消息

## 4.5、redis处理大批量数据
如果我们直接循环要插入的数据，每一条数据通过set方法插入数据库，这势必会消耗大量的网络连接和耗时

> 就是把n个命令通过一个pipe（管道）发送到服务器端，服务器端处理完成以后再返回一个响应结果。而一条一条set需要n次请求n次处理n次响应，而管道只要一次请求n次处理一次响应


1. 代码实现

```java
Jedis jedis = jedisPool.getResource();
Pipeline pip = jedis.pipelined();
List<VehicleInfo> vehicleInfos  = vehicleInfoMapper.selectByParam(param);
for (VehicleInfo vehicleInfo : vehicleInfos) {      
    Map<String, String> keysmap = new HashMap<String, String>();
    //组装数据 - xxx
    keysmap.put("engineFaultsList", JSON.toJSONString(list1));
    //批量插入
    pip.hmset(vehicleInfo.getVehicleSeq()+"", keysmap);
}
pip.sync();//同步
jedis.close();
```

2. 脚本实现

```bash
#! /bin/bash/
for((i=1;i<=1000000;i++))
do
echo "SET key${i} value${i}" >> redis.txt 
done
cat redis.txt | redis-cli --pipe -a password
```

## 4.6、缓存穿透
###  4.6.1、什么是缓存穿透

一般的缓存系统，都是按照key去缓存查询，如果不存在对应的value，就应该去后端系统查找（比如DB）。一些恶意的请求会故意查询不存在的key,请求量很大，就会对后端系统造成很大的压力。这就叫做缓存穿透。

### 4.6.2、如何避免缓存穿透？

1. 对查询结果为空的情况也进行缓存，缓存时间设置短一点，或者该key对应的数据insert了之后清理缓存。
2. 对一定不存在的key进行过滤。
3. 布隆过滤器

> 它是一种类似哈希的数据结构，通过这个数据结构，可以快速的插入和查询，确定某个事件一定不存在或可能存在。特点是占用空间少，缺点是返回的结果是概率性

当一个元素加入集合时，就通过K个hash函数将这个映射成一个位数组中的K个点，把它们置为1。当查询时，只要检查这些点是否全为1，就能判断集合中是否可能存在。
如果k个点有任何一个0，则被检元素一定不在。如果都是1，则很可能存在，这个期望概率是可以设置

## 4.7、缓存雪崩？
### 4.7.1、什么是缓存雪崩

当缓存服务器重启或者大量缓存集中在某一个时间段失效，这样在失效的时候，会给后端系统带来很大压力。导致系统崩溃。

### 4.7.2、如何避免缓存雪崩？

1. 做二级缓存，A1为原始缓存，A2为拷贝缓存，A1失效时，可以访问A2，A1缓存失效时间设置为短期，A2设置为长期
2. 不同的key，设置不同的过期时间，让缓存失效的时间点尽量均匀。


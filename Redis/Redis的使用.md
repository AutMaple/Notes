# Redis 的使用

Redis 尽可能存储不经常变动的数据，如果数据放生变化，需要将resis 中的数据进行清空， 之后在进行导入

## 数据类型

| 类型         |                                        |
| ------------ | -------------------------------------- |
| 字符串类型   | String                                 |
| 哈希类型     | hash ==> map                           |
| 列表类型     | linklist 格式, 支持重复元素            |
| 集合类型     | set 不支持重复元素                     |
| 有序集合类型 | sortedset 不支持重复的元素，且元素有序 |

## 数据类型的存储

1. 字符串类型 ==> string

   存储: set key value

   获取: get key

   删除: del key

2. 哈希类型 ==> hash

   存储：hset key field value

   获取：

   - hget key field ==> 获取指定 field 的值
   - hgetall key ==> 获取 key 中所有的值

   删除：hdel key field

3. 列表类型 ==> list

   存储:

   - lpush key value ==> 从列表左边插入
   - rpush key value ==> 从列表右边插入

   获取:

   - lrange key start end ==> 获取指定范围中的元素，其中，-1 表示最后一个元素

   删除:

   - lpop key ==> 从左边删除 key 中的值
   - rpop key ==> 从右边删除 key 中的值

4. 集合类型 ==> set

   存储: sadd key value

   获取: smembers key ==> 获取集合中所有的元素

   删除: srem key value ==> 删除 set 中的某个元素

5. 有序集合类型 ==> sortedset

   存储: zadd key score value

   获取:

   - zrange key start end ==> 取出 score
   - zrange key start end withscores ==> 取出 score 和对应的 value

   删除: zrem key value

## 常用命令

| 命令     | 作用                |
| -------- | ------------------- |
| keys *   | 获取所有的 key      |
| type key | 查看 key 的数据类型 |
| del key  | 删除指定的 key      |

## Redis 持久化存储

1. RDB – 默认方式
2. AOF – 日志记录方式

# Jedis 的使用

## 连接数据库

```java
// 如果使用空参则使用默认值 地址："localhost“， 端口号： 6379
Jedis jedis = new Jedis(); 
```

## 操作字符串

| 方法                                         | 作用                   |
| -------------------------------------------- | ---------------------- |
| set(String key, String value)                | 存储键值               |
| get(String key)                              | 获取值                 |
| setex(String key, int seconds, String value) | 设置该键值对的存活时间 |

## Jedis 连接池
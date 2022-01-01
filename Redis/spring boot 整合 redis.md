# spring boot 中整合 redis

## 1. 添加依赖

```xml
<!--redis依赖配置-->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

## 2. 添加配置到 springboot 的配置文件中

在 spring 节点下添加配置

```yaml
spring:
  redis:
    host: localhost # redis 服务器地址
    database: 0 # redis 数据库索引
    port: 6379 # redis 数据库监听端口
    password: # 连接 redis 服务器的密码，默认值是为空
    jedis:
      pool:
        max-active: 8 # 连接池最大连接数， 负数表示没有限制
        max-wait: -1ms # 连接池最大阻塞时间，负数表示没有限制
        max-idle: 8 # 连接池中的最大空闲连接
        min-idle: 0 # 连接池中的最小空闲连接
    timeout: 3000ms # 连接超时时间,单位 ms
```

在根节点下添加配置

```yaml
redis:
  key:
    prefix:
      authCode: "portal:authCode"
      expire:
        authCode: 120 # 验证码超时时间
```

3. 设置 redis 服务接口

```java
package com.autmaple.mall.service;

public interface RedisService {
    /**
     * 存储数据
     */
    void set(String key, String value);

    /**
     * 获取数据
     */
    String get(String key);

    /**
     * 设置超时时间
     */
    boolean expire(String key, long expire);

    /**
     * 删除数据
     */
    void remove(String key);

    /**
     * 自增操作
     * @param delta 增长步长
     */
    Long increment(String key, long delta);

}
```

4. 注入StringRedisTemplate，实现RedisService接口

```java
package com.autmaple.mall.service.impl;

import com.autmaple.mall.service.RedisService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

@Service
public class RedisServiceImpl implements RedisService {

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public void set(String key, String value) {
        stringRedisTemplate.opsForValue().set(key, value);
    }

    @Override
    public String get(String key) {
        return stringRedisTemplate.opsForValue().get(key);
    }

    @Override
    public boolean expire(String key, long expire) {
        return Boolean.TRUE.equals(stringRedisTemplate.expire(key, expire, TimeUnit.SECONDS));
    }

    @Override
    public void remove(String key) {
        stringRedisTemplate.delete(key);
    }

    @Override
    public Long increment(String key, long delta) {
        return stringRedisTemplate.opsForValue().increment(key, delta);
    }
}

```
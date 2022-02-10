# RabbitMQ 的使用

## 常用命令

```shell
# 前台启动Erlang VM和RabbitMQ
rabbitmq-server
# 后台启动
rabbitmq-server -detached
# 停止RabbitMQ和Erlang VM
rabbitmqctl stop
# 查看所有队列
rabbitmqctl list_queues
# 查看所有虚拟主机
rabbitmqctl list_vhosts
# 在Erlang VM运行的情况下启动RabbitMQ应用
rabbitmqctl start_app
rabbitmqctl stop_app
# 查看节点状态
rabbitmqctl status
# 查看所有可用的插件
rabbitmq-plugins list
# 启用插件
rabbitmq-plugins enable <plugin-name>
# 停用插件
rabbitmq-plugins disable <plugin-name>
# 添加用户
rabbitmqctl add_user username password
# 列出所有用户：
rabbitmqctl list_users
# 删除用户：
rabbitmqctl delete_user username
# 清除用户权限：
rabbitmqctl clear_permissions -p vhostpath username
# 列出用户权限：
rabbitmqctl list_user_permissions username
# 修改密码：
rabbitmqctl change_password username newpassword
# 设置用户权限：
rabbitmqctl set_permissions -p vhostpath username ".*" ".*" ".*"
# 创建虚拟主机:
rabbitmqctl add_vhost vhostpath
# 列出所以虚拟主机:
rabbitmqctl list_vhosts
# 列出虚拟主机上的所有权限:
rabbitmqctl list_permissions -p vhostpath
# 删除虚拟主机:
rabbitmqctl delete_vhost vhost vhostpath
# 移除所有数据，要在 rabbitmqctl stop_app 之后使用:
rabbitmqctl reset
```

## 启动 RabbitMQ

1. 启动 RabbitMQ 的管理插件

   ```shell
   # 如果需要通过浏览器进入管理界面，需要开启 RabbitMQ 的管理插件
   rabbitmq-plugins enable rabbitmq_management
   ```

2. 开启 RabbitMQ

   ```shell
   systemctl start rabbitmq.service
   ```

## SpringBoot中整合 RabbitMQ

pom.xml

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

application.yaml

```yaml
rabbitmq:
    host: localhost # rabbitmq的连接地址
    port: 5672 # rabbitmq的连接端口号
    virtual-host: /mall # rabbitmq的虚拟host
    username: mall # rabbitmq 的用户名,默认是 guest
    password: mall # rabbitmq 的密码，guest 用户的密码也是 guest
    publisher-confirms: true #如果对异步消息需要回调必须设置为true
```

添加 RabbitMQ 的配置类

```java
package com.macro.mall.tiny.config;

import com.macro.mall.tiny.dto.QueueEnum;
import org.springframework.amqp.core.*;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 消息队列配置
 * Created by macro on 2018/9/14.
 */
@Configuration
public class RabbitMqConfig {

    /**
     * 订单消息实际消费队列所绑定的交换机
     */
    @Bean
    DirectExchange orderDirect() {
        return (DirectExchange) ExchangeBuilder
                .directExchange(QueueEnum.QUEUE_ORDER_CANCEL.getExchange())
                .durable(true)
                .build();
    }

    /**
     * 订单延迟队列所绑定的交换机
     */
    @Bean
    DirectExchange orderTtlDirect() {
        return (DirectExchange) ExchangeBuilder
                .directExchange(QueueEnum.QUEUE_TTL_ORDER_CANCEL.getExchange())
                .durable(true)
                .build();
    }

    /**
     * 订单实际消费队列
     */
    @Bean
    public Queue orderQueue() {
        return new Queue(QueueEnum.QUEUE_ORDER_CANCEL.getName());
    }

    /**
     * 订单延迟队列（死信队列）
     */
    @Bean
    public Queue orderTtlQueue() {
        return QueueBuilder
                .durable(QueueEnum.QUEUE_TTL_ORDER_CANCEL.getName())
                .withArgument("x-dead-letter-exchange", QueueEnum.QUEUE_ORDER_CANCEL.getExchange())//到期后转发的交换机
                .withArgument("x-dead-letter-routing-key", QueueEnum.QUEUE_ORDER_CANCEL.getRouteKey())//到期后转发的路由键
                .build();
    }

    /**
     * 将订单队列绑定到交换机
     */
    @Bean
    Binding orderBinding(DirectExchange orderDirect,Queue orderQueue){
        return BindingBuilder
                .bind(orderQueue)
                .to(orderDirect)
                .with(QueueEnum.QUEUE_ORDER_CANCEL.getRouteKey());
    }

    /**
     * 将订单延迟队列绑定到交换机
     */
    @Bean
    Binding orderTtlBinding(DirectExchange orderTtlDirect,Queue orderTtlQueue){
        return BindingBuilder
                .bind(orderTtlQueue)
                .to(orderTtlDirect)
                .with(QueueEnum.QUEUE_TTL_ORDER_CANCEL.getRouteKey());
    }

}
```


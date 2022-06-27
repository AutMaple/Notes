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

## RabbitMQ 在 java 中的使用

### 发送消息到队列

```java
ConnectionFactory connectionFactory = new ConnectionFactory();
connectionFactory.setHost("localhost");
try(Connection connection = connectionFactory.newConnection();
    Channel channel = connection.createChannel()){
    channel.queueDeclare(QUEUE_NAME, false, false, false,null);
    String message = "Hello World";
    channel.basicPublish("", QUEUE_NAME, null, message.getBytes(StandardCharsets.UTF_8));
    System.out.println("[x] Sent '"+ message + "'");
}
```

### 从消息队列中接收消息

```java
ConnectionFactory factory = new ConnectionFactory();
factory.setHost("localhost");
Connection connection = factory.newConnection();
Channel channel = connection.createChannel();
channel.queueDeclare(QUEUE_NAME, false, false, false, null);
System.out.println("[*] Waiting for messages. To exit press Ctrl+C");
```

Why don't we use a try-with-resource statement to automatically close the channel and the connection? By doing so we would simply make the program move on, close everything, and exit! This would be awkward because we want the process to stay alive while the consumer is listening asynchronously for messages to arrive.

在接收消息的代码中，为什么不使用 try-with-resource 语句来自动关闭 channel 和 connection ? 如果我们使用 try-with-resource 语句的话，代码会正常的运行并关闭所有该关闭的资源并退出，但是这样会变得很麻烦，因为我们想要在异步监听消息到来的时候接收消息程序一直在运行

服务器会将消息异步的传输给消息接收程序，因此需要一个回调函数(在 Java 中回调函数使用接口来实现)来缓存消息，RabbitMQ 提供了一个 DeliverCallback 接口供我们实现

```java
DeliverCallback deliverCallback = (consumerTag, delivery) -> {
    String message = new String(delivery.getBody(), "UTF-8");
    System.out.println(" [x] Received '" + message + "'");
};
channel.basicConsume(QUEUE_NAME, true, deliverCallback, consumerTag -> { });
```

完整的程序

```java
ConnectionFactory factory = new ConnectionFactory();
factory.setHost("localhost");
Connection connection = factory.newConnection();
Channel channel = connection.createChannel();
channel.queueDeclare(QUEUE_NAME, false, false, false, null);
System.out.println("[*] Waiting for messages. To exit press Ctrl+C");
DeliverCallback callback = ((consumerTag, delivery) -> {
    String message = new String(delivery.getBody(), StandardCharsets.UTF_8);
    System.out.println("[x] Receive '" + message + "'");
});
channel.basicConsume(QUEUE_NAME, true, callback, consumerTag ->{});
```

## 工作队列

The main idea behind Work Queues (aka: *Task Queues*) is to avoid doing a resource-intensive task immediately and having to wait for it to complete. Instead we schedule the task to be done later.

工作队列背后的主要思想是避免立即执行资源密集型的任务并等待任务的完成，而是安排这些任务推后执行

 We encapsulate a *task* as a message and send it to a queue. A worker process running in the background will pop the tasks and eventually execute the job. When you run many workers the tasks will be shared between them.

RabbitMQ 将任务封装成一个消息放入到队列中，而后台运行的处理进程将会弹出队列中的任务并执行这些任务，当有多个后台任务处理程序时，这些程序将共享队列中的任务。

One of the advantages of using a Task Queue is the ability to easily parallelise work.  If we are building up a backlog of work, we can just add more workers and that way, scale easily.

使用工作队列的好处之一就是可以轻松地并行工作。如果我们积累了大量的工作，我们只需要添加对应的处理程序，程序规模的控制非常简单

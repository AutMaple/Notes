# AMQP 介绍

AMQP 是 Advanced Message Queue Protocol 的缩写， AMQP 是一种标准，而真正实现了 AMQP 协议的是 RabbitMQ。Spring AMQP 将 Spring 的核心概念用在了基于 AMQP 进行消息处理的项目开发中。Spring AMQP 对消息的发送和接收进行了高度的抽象，方便开发者进行快速的开发。

AMQP 规范规定任何消息中间件都必须提供一个没有名字且类型为 direct 类型的默认 exchange。所有声明的队列都需要绑定到默认 exhcange, 并且 bindingKey 为队列的名字

# ConnectionFactory

Spring AMQP 提供了三个实现类：

 -   `PooledChannelConnectionFactory`
-   `ThreadChannelConnectionFactory`
-   `CachingConnectionFactory`

前两个是在 2.3 版本中添加的

## PooledChannelConnectionFactory

该 factory 管理一个 Connection 和两个 Channel 池，其中一个池管理事务型 Channel，另外一个池管理非事务型 Channel。

## ThreadChannelConnectionFactory

该 factory 管理一个 Connection 和两个 ThreadLocal 对象。其中一个 ThreadLocal 对象用于事务型 Channel，另外一个 ThreadLocal 对象用于非事务型的 Channel。这种实现方式让 ThreadChannelConnectionFactory 拥有了在同一个线程上的所有操作使用的是同一个 Channel 的能力。这对于消息之间有着顺序关系的情形非常有帮助。

```ad-important
title: 注意

为了避免内存泄露，如果程序中使用的是生存期非常短的线程，那一定要调用 factory 的 `closeThreadChannel()` 方法来释放对应 Channel 资源
```

## CachingConnectionFactory

CachingConnectionFactory 建立了一个程序共享的 Connection Proxy。CachingConnectionFactory 能够缓存创建的 Channel, 并且根据 Channel 是否支持事务将这些 Channel 分开进行维护。缓存的默认大小是 25。

从 1.3 版本开始，既可以缓存 Connection 也可以仅缓存 Channel。在 `cacheMode` 为 `CacheMode.CONNECTION` 模式下, 每调用一次 `createConnection()` 方法都会创建一个新的 Connection 或者从缓存池中获取一个闲置 Connection。当要关闭一个 Connection 时，会判断是否达到缓存池大小的上限，如果没有达到，就会将 Connection 放入到缓存池中，否则关闭对应的 Connection。并且 Connection 中的 Channel 也会被缓存起来。

```ad-important
title: 重要

当缓存模式设置成 `CacheMode.CONNECTION` 时，自动在 rabbitmq 中声明 Queue，Binding 等会失效
```

```ad-warning
title: 注意

CachingConnectionFactory 并没有指定可创建的 Connection 的上限，只指定了核心 Connection 数量。从 1.5.5 版本开始，添加了一个 `connectionLimit` 属性，当该属性设置时，会限制 Connection 创建的数量。当 Connection 创建的数量达到超过限制时，会等待 `channelCheckoutTimeLimit` 属性设置的时间，如果在这段时间之内缓存池中有闲置的 Connection, 则使用闲置的 Connection，否则抛出 `AmqpTimeoutException` 异常
```

Starting with version 1.4.2, the `CachingConnectionFactory` has a property called `channelCheckoutTimeout`. When this property is greater than zero, the `channelCacheSize` becomes a limit on the number of channels that can be created on a connection. If the limit is reached, **calling threads block until a channel is available or this timeout is reached**, in which case a `AmqpTimeoutException` is thrown.

## 自定义 ConnectionFactory

If you need to implement your own `ConnectionFactory` for some reason, the `AbstractConnectionFactory` base class may provide a nice starting point.

# BlockedListener

The connection might be blocked for interaction from the broker that corresponds to the [Memory Alarm](https://www.rabbitmq.com/memory.html). Starting with version 2.0, the `org.springframework.amqp.rabbit.connection.Connection` can be supplied with `com.rabbitmq.client.BlockedListener` instances to be notified for connection blocked and unblocked events. In addition, the `AbstractConnectionFactory` emits a `ConnectionBlockedEvent` and `ConnectionUnblockedEvent`, respectively, through its internal `BlockedListener` implementation. These let you provide application logic to react appropriately to problems on the broker and (for example) take some corrective actions.

# MessageConverter

Spring AMQP 通过 MessageConverter 接口来进行消息的序列化和反序列化

```java
public interface MessageConverter {  

	Message toMessage(Object object, MessageProperties messageProperties) throws MessageConversionException;  
	  
	default Message toMessage(Object object, MessageProperties messageProperties, @Nullable Type genericType)  throws MessageConversionException {  
	         return toMessage(object, messageProperties);  
	}  
	
	Object fromMessage(Message message) throws MessageConversionException;  
  
}
```

- toMessage() 方法将 java 对象转化成 Message 对象
- fromMessage() 方法将 Message 对象转化成 java 对象

在 AMQP 中，比较重要的是 fromMessage 方法。

Spring AMQP 提供的默认实现是 `SimpleMessageConverter`。看看 `SimpleMessageConverter` 的 fromMessage 方法

```java
public Object fromMessage(Message message) throws MessageConversionException {  
   Object content = null;  
   MessageProperties properties = message.getMessageProperties();  
   if (properties != null) {  
      String contentType = properties.getContentType();  
      if (contentType != null && contentType.startsWith("text")) {  
         String encoding = properties.getContentEncoding();  
         if (encoding == null) {  
            encoding = this.defaultCharset;  
         }  
         try {  
            content = new String(message.getBody(), encoding);  
         }  
         catch (UnsupportedEncodingException e) {  
            throw new MessageConversionException(  
                  "failed to convert text-based Message content", e);  
         }  
      }  
      else if (contentType != null &&  
            contentType.equals(MessageProperties.CONTENT_TYPE_SERIALIZED_OBJECT)) {  
         try {  
            content = SerializationUtils.deserialize(  
                  createObjectInputStream(new ByteArrayInputStream(message.getBody()), this.codebaseUrl));  
         }  
         catch (IOException | IllegalArgumentException | IllegalStateException e) {  
            throw new MessageConversionException(  
                  "failed to convert serialized Message content", e);  
         }  
      }  
   }  
   if (content == null) {  
      content = message.getBody();  
   }  
   return content;  
}
```

`fromMessage` 的逻辑如下：

- 如果 message 的 properties 为 null，则直接返回 `byte[]` 类型的 body
- 如果 properties 不为 null 且 contentType 为 text，则将 body 转换成 String，然后进行返回
- 如果 properties 不为 null 且 contentType 为 `application/x-java-serialized-object`，则调用反序列化工具进行反序列化

## contentType 属性

- application/octet-stream: 二进制字节数组存储，反序列化时使用 `byte[]` 进行存储
- application/x-java-serialized-object: java 对象序列化格式存储，反序列化时使用 Object 或相应类型进行存储(反序列化时类型应该同包同名，否则会报找不到类异常)
- text/plain: 文本数据类型存储, 反序列化时，使用 String 类型进行存储
- application/json: JSON 格式，反序列化时使用 Object、 相应的类型进行存储

# @RabbitListener

@RabbitListener 注解是用来消费消息的。它既可以放在类上也可以放在方法上。并且通过 @EnableRabbit 注解来启用 @RabbitListener

## 放在类上

把 @RabbitListener 注解放在类上时，需要配合 @RabbitHandler 注解使用。当 @RabbitListener 标注在类时，表示接收到消息时，将消息交给带有 @RabbitHandler 注解的方法进行处理，具体使用那个方法，需要与 `MessageConverter` 转换之后的 Java 对象比对之后才能够确定

```java
@RabbitListener(queues = {"hello.world"})
public class MessageHanlder {

	@RabbitHandler
	public void handler(byte[] message) {
		System.out.println(new String(message));
	}

	@RabbitHandler
	public void handler1(String message) {
		System.out.println(message);	
	}
}
```

## 放在方法上

当 @RabbitListener 放在方法上时，还可以使用 @Payload 和 @Header 注解获取 Message 对象中的 body 和 messageProperties。获取到的属性首先会通过 MessageConverter 进行转换，转换之后才会进行赋值

```java
public class MessgeHandler {

	@RabbitListener(queues = {"hello.world"})
	public void handler(@Payload byte[] message, @Header Map<String, Object> properties){
		System.out.println(new String(message));	
	}
}
```

## 通过 @RabbitListener 声明 Binding

- 通过 @RabbitListener 的 bindings 属性声明 Binding。若 Rabbit 中不存在对应的 Queue, Exchange, RouteKey, 则自动创建，如果存在则抛出异常

## @RabbitListener 的 containerFactory 属性

@RabbitListener 的 containerFactory 属性可以指定一个 `RabbitListenerContainerFactory` 类型的 bean。默认找的 bean 的名字为 `rabbitListenerContainerFactory`。如果 Spring 容器中没有对应的 bean，就会抛出异常

因此，如果我们自己配置了 `RabbtiListenerContainerFactory`, 要么名字为 `rabbitListenerContainerFactory` 或者在使用 @RabbitListener 注解的时候指定 `containerFactory` 属性为我们自定义的 `RabbitListenerContainerFacotry` bean 的名字

```java
@Configuration
public class RabbitMqConfiguration {
	 @Bean
    MessageConverter messageConverter() {
        return new Jackson2JsonMessageConverter();
    }

	@Bean
    ConnectionFactory connectionFactory() {
        CachingConnectionFactory connectionFactory = new CachingConnectionFactory();
        connectionFactory.setHost(rabbitMqProperties.getHost());
        connectionFactory.setPort(rabbitMqProperties.getPort());
        connectionFactory.setUsername(rabbitMqProperties.getUsername());
        connectionFactory.setPassword(rabbitMqProperties.getPassword());
        connectionFactory.setVirtualHost(rabbitMqProperties.getVirtualHost());
        connectionFactory.setChannelCacheSize(5);
        return connectionFactory;
    }

	 @Bean
    RabbitListenerContainerFactory<?> rabbitListenerContainerFactory() {
        SimpleRabbitListenerContainerFactory factory = new SimpleRabbitListenerContainerFactory();
        factory.setConnectionFactory(connectionFactory());
        factory.setMessageConverter(messageConverter());
        return factory;
    }
}
```

# AmqpAdmin

AmqpAdmin 的作用主要是用来管理 Exchange, Queue, Binding 等，起到管理组件的作用。

AmqpAdmin 的 `declareXXX()` 方法在调用之后，会自动在 RabbitMq 中创建对应的结构，如 `declareExchange(...)` 会在 RabbitMq 中创建一个 Exchange。

> AmqpAdmin 会自动寻找 Spring 容器中类型为 Queue, Exchange, Binding 的 Bean，并自动在 rabbitmq 中进行声明。


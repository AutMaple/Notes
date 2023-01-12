# AMQP 介绍

AMQP 是 Advanced Message Queue Protocol 的缩写， AMQP 是一种标准，而真正实现了 AMQP 协议的是 RabbitMQ。Spring AMQP 将 Spring 的核心概念用在了基于 AMQP 进行消息处理的项目开发中。Spring AMQP 对消息的发送和接收进行了高度的抽象，方便开发者进行快速的开发。

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
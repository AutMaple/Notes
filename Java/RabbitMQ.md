# RabbitMQ

## 什么是消息队列

消息指的是两个应用之间传递的数据。数据的格式是多样的，可以是文本字符串，也可以包含嵌入对象。

消息队列(Message Queue) 是在消息传递的过程中用于保存消息的容器。在消息传递的过程中，发送消息的叫做生产者，接收消息的叫做消费者。生产者往消息队列中写入数据，消费者从消息队列中读取数据。生产者只管往消息队列中写入数据，而不用管消息如何被消费；同样地，消费者只管往消息队列中取数据，而不用管数据的来源。

## 消息队列的作用

### 解耦

在项目启动之初来预测将来会碰到什么需求是极其困难的。消息中间件在处理过程中间插入了一个隐含的、基于数据的接口层，两边的处理过程都要实现这一接口，这允许你独立地扩展或修改两边的处理过程，只要确保它们遵守同样的接口约束即可。

假设现在有 A，B，C，D 四个系统，A 需要给 B, C, D 系统发送消息，那么在 A 的代码中就需要加上给 B, C, D 系统发送消息的代码，此时如果删除或者增加了一个系统，又需要添加或者删除对应的代码，这不满足设计模式中可扩展性的原则，设计模式中的可扩展性要求在不改动原先代码的条件下扩展系系统的功能。增加消息队列这个中间件之后，A 系统只需要往消息队列中放消息，其他的系统按照相应的规则取消息即可，增加或者删除其他的系统不影响 A 系统。通过一个消息队列，就降低了这些系统间的耦合性。

### 冗余(存储)

有些情况下，处理数据的过程会失败。消息中间件可以把数据进行持久化直到它们已经被完全处理，通过这一方式规避了数据丢失风险。在把一个消息从消息中间件中删除之前，需要你的处理系统明确地指出该消息已经被处理完成，从而确保你的数据被安全地保存直到你使用完毕

### 扩展性

因为消息中间件解耦了应用的处理过程，所以提高消息入队和处理的效率是很容
易的，只要另外增加处理过程即可，不需要改变代码，也不需要调节参数

### 削峰

在访问量剧增的情况下，应用仍然需要继续发挥作用，但是这样的突发流量并不常见。如果以能处理这类峰值为标准而投入资源，无疑是巨大的浪费。使用消息中间件能够使关键组件支撑突发访问压力，不会因为突发的超负荷请求而完全崩溃

### 可恢复性

当系统一部分组件失效时，不会影响到整个系统。消息中间件降低了进程间的耦合度，所以即使一个处理消息的进程挂掉，加入消息中间件中的消息仍然可以在系统恢复后进行处理

### 顺序保证

在大多数使用场景下，数据处理的顺序很重要，大部分消息中间件支持一定程度上的顺序性

### 缓冲

在任何重要的系统中，都会存在需要不同处理时间的元素。消息中间件通过一个缓冲层来帮助任务最高效率地执行，写入消息中间件的处理会尽可能快速。该缓冲层有助于控制和优化数据流经过系统的速度

### 异步

在很多时候应用不想也不需要立即处理消息。消息中间件提供了异步处理机制，允许应用把一些消息放入消息中间件中，但并不立即处理它，在之后需要的时候再慢慢处理

假设现在有 A，B，C，D 四个系统，A 需要给 B, C, D 系统发送消息。在没有消息队列的情况下，需要将消息成功发送给 B, C, D 系统之后才能够响应给客户端。增加了消息队列之后，只需要将消息成功发送给消息队列就可以响应客户端，至于后续的操作由消息队列完成，大大提高了响应速度。对于非核心的业务，比如发送短信，发送邮件等就可以使用消息队列的方式来实现。

### 核心业务和非核心业务

比如一个用户注册的功能，该功能的核心业务就是将用户的信息存入数据库即可，只要存入了数据库就可以给用户进行响应了，至于注册成功之后发送短信或者发送邮箱给用户，则属于非核心的业务。

### 削峰

削峰是 MQ 很重要的一个特性。比如系统 A 在某个时间段内请求暴增，系统处理不过来。如果没有消息队列，系统可能会崩溃。在这种情况下就可以将请求存入到消息队列中，而消费者只需要根据它的能力消费消息即可。

## RabbitMQ 的作用

You can use Rabbit queues to store jobs and let the broker perform the load balancing and job distribution for you.

我们可以使用 RabbitMQ 来存储任务，并且 RabbitMQ 可以帮助我们分发任务，从而实现负载均衡 

## RabbitMQ 的特点

RabbitMQ 是一款使用 Erlang 语言开发的，实现 AMQP (高级消息队列协议) 的开源消息中间件。该中间件的特点如下：

- 可靠性：支持持久化，传输确认，发布确认等保证了 MQ 的可靠性
- 灵活的消息分发策略：这是 RabbitMQ 的一大特点。在消息进入 MQ 之前由 Exchange(交换机) 来路由消息。分发消息的策略有：简单模式，工作队列模式，发布订阅模式，路由模式，通配符模式。
- 支持集群：多台 RabbitMQ 机器可以组成一个集群，形成一个逻辑的 Broker
- 多种协议：RabbitMQ 支持多种消息队列协议，如 STOMP， MQTT 等。
- 支持多语言客户端： RabbitMQ 几乎支持所有常用的编程语言，包括 Java, .NET, Ruby 等
- 可视化管理界面: RabbitMQ 提供了一个简单易用客户端界面，使得用户可以键控和管理消息 Broker.
- 插件机制：RabbitMQ 提供了许多插件，可以通过插件进行扩展，也可以编写自己的插件

## RabbitMq 的消息模型

![](../Attachment/arch_screen_52.b0519322-16561238609703.png)

| 标志 | 中文名     | 英文名   | 描述                                             |
| ---- | ---------- | -------- | ------------------------------------------------ |
| P    | 生产者     | Producer | 消息的发送者，可以将消息发送到交换机             |
| C    | 消费值     | Consumer | 消息的接受者，从队列中拉取消息进行消费           |
| X    | 交换机     | Exchange | 接收生产者发送的消息，并根据路由发送到指定的队列 |
| Q    | 队列       | Queue    | 存储交换机发送过来的消息                         |
| type | 交换机类型 | type     | direct 表示直接根据路由 (orange/black) 发送消息  |

A message has two parts: a payload and a label. The payload is the data you want to transmit. It can be anything from a JSON array to an MPEG-4 of your favorite iguana Ziggy. RabbitMQ doesn’t care. The label is more interesting. It describes the payload, and is how RabbitMQ will determine who should get a copy of your message.

AMQP only describes the message with a label (an exchange name and optionally a topic tag) and leaves it to Rabbit to send it to interested receivers based on that label.

producers create messages and label them for routing

Consumers attach to a broker server and subscribe to a queue.

a consumer receives a message, **it now only has one part: a payload.** The labels attached to the message don’t get passed along with the payload when the message is routed. 

### Channel 

Before you consume from or publish to Rabbit, you first have to connect to it. By connecting, you’re creating a TCP connection between your app and the Rabbit broker. Once the TCP connection is open (and you’re authenticated), your app then creates an AMQP channel. **This channel is a virtual connection inside the “real” TCP connection**, and it’s over the channel that you issue AMQP commands. E**very channel has a unique ID** assigned to it (your AMQP library of choice will handle remembering the ID for you). Whether you’re publishing a message, subscribing to a queue, or receiving a message, **it’s all done over a channel**. 

RabbitMQ 采用类似 NIO（Non-blocking I/O）的做法，选择 TCP 连接复用，不仅可以减少性能开销，同时也便于管理

每个线程把持一个信道，所以信道复用了 Connection 的 TCP 连接。同时 RabbitMQ 可以确保每个线程的私密性，就像拥有独立的连接一样。当每个信道的流量不是很大时，复用单一的Connection 可以在产生性能瓶颈的情况下有效地节省 TCP 连接资源。但是当信道本身的流量很大时，这时候多个信道复用一个 Connection 就会产生性能瓶颈，进而使整体的流量被限制了。此时就需要开辟多个 Connection，将这些信道均摊到这些 Connection 中，至于这些相关的调优策略需要根据业务自身的实际情况进行调节

#### Why do we need channels?

 you might ask? Why not just issue AMQP commands directly over the TCP connection? The main reason is because **setting up and tearing down TCP sessions is expensive for an operating system**

![image-20220702104904435](../Attachment/image-20220702104904435.png)

NIO，也称非阻塞 I/O，包含三大核心部分：Channel（信道）、Buffer（缓冲区）和 Selector（选择器）。NIO 基于 Channel 和 Buffer 进行操作，数据总是从信道读取数据到缓冲区中，或者从缓冲区写入到信道中。Selector 用于监听多个信道的事件（比如连接打开，数据到达等）。因此，单线程可以监听多个数据的信道。NIO 中有一个很有名的 Reactor 模式，有兴趣的读者可以深入研究

## Exchange 交换机的类型

- direct: 该类型的交换机会将消息转发到 RoutingKey 和 BindingKey 完全匹配的队列中
- topic：该类型的交换机和 direct 类型的交换机类型，也是将消息转发到 RoutingKey 和 BindingKey 匹配的队列中，但是这里的匹配可以是模糊匹配 
- headers: 该类型的交换机不依赖于路由键的匹配规则来路由消息，而是根据发送的消息内容中的 headers 属性进行匹配。
- fanout: 该类型的交换机会将它收到的消息转发给绑定到该交换机的所有队列

### Topic

- RoutingKey 为一个点号 `.` 分隔的字符串（被点号 `.`分隔开的每一段独立的字符
  串称为一个单词），如 `com.rabbitmq.client` 、 `java.util.concurrent`、 `com.hidden.client`；
- BindingKey 和 RoutingKey 一样也是点号 `.` 分隔的字符串；
- BindingKey 中可以存在两种特殊字符串 `*` 和 `#`，用于做模糊匹配，其中 `*` 用于匹配一个单词，`#` 用于匹配若干个单词（可以是零个）

例子：

![image-20220702143732749](../Attachment/image-20220702143732749.png)

- 路由键为 `com.rabbitmq.client` 的消息会同时路由到 Queue1 和 Queue2；
- 路由键为 `com.hidden.client` 的消息只会路由到 Queue2 中；
- 路由键为 `com.hidden.demo` 的消息只会路由到 Queue2 中；
- 路由键为 `java.rabbitmq.demo` 的消息只会路由到 Queue1 中；
- 路由键为 `java.util.concurrent` 的消息将会被丢弃或者返回给生产者（需要设置 mandatory 参数），因为它没有匹配任何路由键

### Headers

在绑定队列和交换器时制定一组键值对，当发送消息到交换器时，RabbitMQ 会获取到该消息的 headers（也是一个键值对的形式），对比其中的键值对是否完全匹配队列和交换器绑定时指定的键值对，如果完全匹配则消息会路由到该队列，否则不会路由到该队列。headers 类型的交换器性能会很差，而且也不实用，基本上不会看到它的存在。

## 应用通过消息中间件通信

![image-20220702110647348](../Attachment/image-20220702110647348.png)

应用程序 A 与应用程序 B 通过使用消息中间件的应用程序编程接口（API，Application Program Interface）发送消息来进行通信。消息中间件将消息路由给应用程序 B，这样消息就可存在于完全不同的计算机上。消息中间件负责处理网络通信，如果网络连接不可用，消息中间件会存储消息，直到连接变得可用，再将消息转发给应用程序 B。灵活性的另一方面体现在，**当应用程序 A 发送其消息时，应用程序 B 甚至可以处于不运行状态，消息中间件将保留这份消息，直到应用程序 B 开始执行并消费消息，这样还防止了应用程序 A 因为等待应用程序 B 消费消息而出现阻塞。**这种异步通信方式要求应用程序的设计与现在大多数应用不同。不过对于时间无关或并行处理的场景，它可能是一个极其有用的方法

## RabbitMQ 用户管理

RabbitMQ 默认的用户名和密码都是: guest。这个账户有限制，默认只能够通过本地网络(localhost) 进行访问，远程网络访问受限。在生产环境下需要另外添加一个用户并设置响应的访问权限。

添加新用户

```shell
$ sudo rabbitmqctl add_user root root123
Adding user "root" ...
Done. Don't forget to grant the user permissions to some virtual hosts! See 'rabbitmqctl help set_permissions' to learn more.

$ sudo rabbitmqctl set_permissions -p / root ".*" ".*" ".*"
Setting permissions for user "root" in vhost "/" ...

$ sudo rabbitmqctl set_user_tags root administrator
Setting tags for user "root" to [administrator] ...
```

## 交换器、路由键、绑定键

![image-20220702122426396](../Attachment/image-20220702122426396.png)

Exchange: 交换器。生产者将消息发送到 Exchange，由交换器将消息路由到一个或者多个队列中。如果路由不到，或许会返回给生产者，或许直接丢弃。

RoutingKey: 路由键，生产者将消息发送给交换器的时候，需要指定 RoutingKey, 用于指定这个消息的路由规则。而这个 Routing Key 需要与交换器类型和绑定键（BindingKey）联合使用才能最终生效。在交换器类型和绑定键（BindingKey）固定的情况下，生产者可以在发送消息给交换器时，通过指定 RoutingKey 来决定消息流向哪里。

Binding: 绑定，RabbitMQ 中通过绑定将交换器与队列关联起来，在绑定的时候一般会指定一个绑定键（BindingKey），这样 RabbitMQ 就知道如何正确地将消息路由到队列了

![image-20220702122747092](../Attachment/image-20220702122747092.png)

生产者将消息发送给交换器时，需要一个 RoutingKey，当 BindingKey 和 RoutingKey 相匹配时，消息会被路由到对应的队列中。在绑定多个队列到同一个交换器的时候，这些绑定允许使用相同的 BindingKey。BindingKey 并不是在所有的情况下都生效，它依赖于交换器类型，比如 fanout 类型的交换器就会无视 BindingKey，而是将消息路由到所有绑定到该交换器的队列中

## AMQP 流转过程

![image-20220702145039776](../Attachment/image-20220702145039776.png)

![image-20220702145123513](../Attachment/image-20220702145123513.png)

## Connection

通过设置参数的方式创建连接

```java
ConnectionFactory factory = new ConnectionFactory();
factory.setUsername(USERNAME);
factory.setPassword(PASSWORD);
factory.setVirtualHost(virtualHost);
factory.setHost(IP_ADDRESS);
factory.setPort(PORT);
Connection conn = factory.newConnection();
```

通过 URI 的方式创建连接

```java
ConnectionFactory factory = new ConnectionFactory();
factory.setUri("amqp://username:password@ipAddress:port/virtualHost");
Connection conn = factory.newConnection();
```

**注意**

Connection 可以用来创建多个 Channel 实例，但是 Channel 实例不能在线程间共享，应用程序应该为每一个线程开辟一个 Channel。某些情况下 Channel 的操作可以并发运行，但是在其他情况下会导致在网络上出现错误的通信帧交错，同时也会影响发送方确认（publisher confirm）机制的运行，所以多线程间共享 Channel 实例是**非线程安全的**

### isOpen 方法

Channel 或者 Connection 中有个 isOpen 方法可以用来检测其是否已处于开启状态（关于 Channel 或者 Connection 的状态可以参考 3.6 节）。但并不推荐在生产环境的代码上使用 isOpen 方法，这个方法的返回值依赖于 shutdownCause的存在，有可能会产生竞争

如 果 在 使 用 Channel 的 时 候 其 已 经 处 于 关 闭 状 态 ， 那 么 程 序 会 抛 出 一 个 `com.rabbitmq.client.ShutdownSignalException`，我们只需捕获这个异常即可。当然同时也要试着捕获 IOException 或者 SocketException，以防 Connection 意外关闭

```java
public void validMethod(Channel channel){
    try {
    	...
    	channel.basicQos(1);
    } catch (ShutdownSignalException sse) {
        // possibly check if channel was closed
        // by the time we started action and reasons for
        // closing it
        ...
    } catch (IOException ioe) {
        // check why connection was closed
        ...
    }
}
```

## exchangeDeclare 方法详解

```java
Exchange.DeclareOk exchangeDeclare(String exchange,
	String type, 
    boolean durable,
	boolean autoDelete,
    boolean internal,
	Map<String, Object> arguments) throws IOException
```

- durable:  设置是否持久化。durable 设置为 true 表示持久化，反之是非持久化。持
  久化可以将交换器存盘，在服务器重启的时候不会丢失相关信息
- autoDelete: 设置是否自动删除。autoDelete 设置为 true 则表示自动删除。自动
  删除的前提是至少有一个队列或者交换器与这个交换器绑定，之后所有与这个交换器绑定的队列或者交换器都与此解绑。注意不能错误地把这个参数理解为：“当与此交换器连接的客户端都断开时，RabbitMQ 会自动删除本交换器
- internal：设置是否是内置的。如果设置为 true，则表示是内置的交换器，客户端程序无法直接发送消息到这个交换器中，只能通过交换器路由到交换器这种方式

### 检测相应的交换机是否存在

```java
Exchange.DeclareOk exchangeDeclarePassive(String name) throws IOException;
```

这个方法在实际应用过程中还是非常有用的，它主要用来检测相应的交换器是否存在。如果存在则正常返回；如果不存在则抛出异常：404 channel exception，同时 Channel 也会被关闭

## queueDeclare 方法详解

```java
Queue.DeclareOk queueDeclare() throws IOException;
Queue.DeclareOk queueDeclare(String queue,
                             boolean durable,
                             boolean exclusive,
                             boolean autoDelete,
                             Map<String, Object> arguments)throws IOException;
```

不带任何参数的 queueDeclare 方法默认创建一个由 RabbitMQ 命名的（类似这种
amq.gen-LhQz1gv3GhDOv8PIDabOXA 名称，这种队列也称之为匿名队列）、排他的、自动删除的、非持久化的队列

- exclusive:  设置是否排他。为 true 则设置队列为排他的。如果一个队列被声明为排
  他队列，该队列仅对首次声明它的**连接可见**，并在连接断开时自动删除。排他队列是基于连接（Connection）可见的，同一个连接的不同信道（Channel）是可以同时访问同一连接创建的排他队列。*首次*是指如果一个连接已经声明了一个排他队列，**其他连接是不允许建立同名的排他队列的**。即使该队列是持久化的，一旦连接关闭或者客户端退出，**该排他队列都会被自动删除**，这种队列适用于一个客户端同时发送和读取消息的应用场景

生产者和消费者都能够使用 queueDeclare 来声明一个队列，但是如果消费者在同一个
信道上订阅了另一个队列，就无法再声明队列了。必须先取消订阅，然后将信道置为“传输”模式，之后才能声明队列

## Queue

RabbitMQ 的消息存储在队列中，交换器的使用并不真正耗费服务器的性能，而队列会

在实际业务应用中，需要对所创建的**队列的流量、内存占用及网卡占用有一个清晰的认知**，预估其平均值和峰值，以便在固定硬件资源的情况下能够进行合理有效的分配

## 消费模式

RabbitMQ 的消费模式分两种：推（Push）模式和拉（Pull）模式。推模式采用Basic.Consume进行消费，而拉模式则是调用 Basic.Get 进行消费

### 推送模式

在推送模式中，可以通过持续订阅的方式来消费消息，使用到的相关类有：
```java
import com.rabbitmq.client.Consumer;
import com.rabbitmq.client.DefaultConsumer;
```

接收消息一般通过实现 Consumer 接口或者继承 DefaultConsumer 类来实现。当调用与 Comsumer 相关 API 方法时，不同的订阅采用不同的消费者标签(ConsumerTag) 来区分彼此。在同一个 Channel 中的消费者也需要通过唯一的消费者标签以作区分

### basicConsume 方法

```java
String basicConsume(String queue, 
                    boolean autoAck,
                    String consumerTag,
                    boolean noLocal,
                    boolean exclusive,
                    Map<String, Object> arguments,
                    Consumer callback) throws IOException
```

- consumerTag: 用来标识不同的消费者
- noLocal: 设置为 true 则表示不能将同一个 Connection 中生产者发送的消息传送给
  这个 Connection 中的消费者

实现复杂的消费者客户端，需要使用到如下的钩子函数

```java
void handleConsumeOk(String consumerTag);
void handleCancelOk(String consumerTag);
void handleCancel(String consumerTag) throws IOException;
void handleShutdownSignal(String consumerTag, ShutdownSignalException sig);
void handleRecoverOk(String consumerTag);
```

- handleShutdownSignal 方法会在 channel 或者 Connection 关闭的时候调用

- handleCancel 和 handleCancelOk 方法在消费者取消订阅的时候调用

通过 channel.basicCancel(consumerTag) 方法也可以取消一个消费者的订阅，不过这行代码的执行顺序是: handleConsumerOk ==> handleDelivery ==> handleCancelOk

和生产者一样，消费者客户端同样需要考虑**线程安全的问题**。消费者客户端的这些 callback 会被分配到与 Channel 不同的线程池上，这意味着消费者客户端可以安全地调用这些阻塞方法，比如 channel.queueDeclare、channel.basicCancel 等。

每个 Channel 都拥有自己独立的线程。最常用的做法是一个 Channel 对应一个消费者,
也就是意味着消费者彼此之间没有任何关联。**当然也可以在一个 Channel 中维持多个消费者, 但是要注意一个问题，如果 Channel 中的一个消费者一直在运行，那么其他消费者的 callback 会被“耽搁”**。

### 拉取模式

通过 channel.basicGet 方法可以单条地获取消息

```java
GetResponse basicGet(String queue, boolean autoAck) throws IOException;
```

**注意**

Basic.Consume 将信道（Channel）置为接收模式，直到取消队列的订阅为止。在接收模式期间，RabbitMQ 会不断地推送消息给消费者，当然推送消息的个数还是会受到 Basic.Qos 的限制。如果只想从队列获得单条消息而不是持续订阅，建议还是使用 Basic.Get 进行消费。但是不能将 Basic.Get 放在一个循环里来代替 Basic.Consume，这样做会严重影响 RabbitMQ 的性能。**如果要实现高吞吐量，消费者理应使用 Basic.Consume 方法**

## 消息的确认与拒绝

消费者在订阅队列时，可以指定 autoAck 参数，当 autoAck 等于 false 时，RabbitMQ 会等待消费者显式地回复确认信号后才从内存（或者磁盘）中移去消息（实质上是先打上删除标记，之后再删除）。当 autoAck 等于 true 时，RabbitMQ 会自动把发送出去的消息置为确认，然后从内存（或者磁盘）中删除，而不管消费者是否真正地消费到了这些消息。

**RabbitMQ 不会为未确认的消息设置过期时间，Rabbit MQ 判断消息是否需要重新投递给消费者的唯一依据是消费该消息的消费者连接是否已经断开**

## 备用交换机(Alternate Exchange)

如果不想设置 mandatory 参数又不想消息丢失，就可以使用备用服务器。我们可以在声明交换机的时候设置对应的参数来启用备用服务器或者是通过策略(Policy) 的方式实现。如果两者同时使用，则前者的优先级更高，会覆盖掉 Policy 的设置

```java
Map<String, Object> args = new HashMap<String, Object>();
args.put("alternate-exchange", "myAe");
channel.exchangeDeclare("normalExchange", "direct", true, false, args);
channel.exchangeDeclare("myAe", "fanout", true, false, null);
channel.queueDeclare("normalQueue", true, false, false, null);
channel.queueBind("normalQueue", "normalExchange", "normalKey");
channel.queueDeclare("unroutedQueue", true, false, false, null);
channel.queueBind("unroutedQueue", "myAe", "");
```

备份交换器其实和普通的交换器没有太大的区别，为了方便使用，**建议设置为 fanout 类型**，如若读者想设置为 direct 或者 topic 的类型也没有什么不妥。需要注意的是，消息被重新发送到备份交换器时的路由键和从生产者发出的路由键是一样的

对于备份交换器，总结了以下几种特殊情况：

- 如果设置的备份交换器不存在，客户端和 RabbitMQ 服务端都不会有异常出现，此时消息会丢失。
- 如果备份交换器没有绑定任何队列，客户端和 RabbitMQ 服务端都不会有异常出现，此时消息会丢失。
- 如果备份交换器没有任何匹配的队列，客户端和 RabbitMQ 服务端都不会有异常出现，此时消息会丢失。
- 如果备份交换器和 mandatory 参数一起使用，那么 mandatory 参数无效

## 过期时间(TTL)

Time To Live(TLL) 即过期时间，RabbitMQ 可以对消息和队列设置 TTL。

### 设置消息的过期时间

目前有两种方法可以设置消息的 TTL**。第一种方法是通过队列属性设置**，队列中所有消息都有相同的过期时间。**第二种方法是对消息本身进行单独设置**，每条消息的 TTL 可以不同。如果两种方法一起使用，则消息的 TTL 以两者之间较小的那个数值为准。消息在队列中的生存时间一旦超过设置的 TTL 值时，就会变成“死信”（Dead Message），消费者将无法再收到该消息

通过在队列属性中设置消息的过期时间需要在 channel.queueDeclare() 方法中加入 x-message-ttl 参数，单位是毫秒

```java
AMQP.BasicProperties.Builder builder = new AMQP.BasicProperties.Builder();
builder.deliveryMode(2);//持久化消息
builder.expiration("60000");//设置 TTL=60000ms
AMQP.BasicProperties properties = builder.build();
channel.basicPublish(exchangeName,routingKey,mandatory,properties,
"ttlTestMessage".getBytes());
```

### 设置队列的过期时间

通过 channel.queueDeclare 方法中的 x-expires 参数可以设置队列被自动删除前处于未被使用状态的时间。未被使用状态的意思是队列上没有任何的消费者，队列也没有被重新声明，并且在过期时间段内也没有调用 Basic.Get 命令。

RabbitMQ 会确保在过期时间到达后将队列删除，但是不保障删除的动作有多及时。在 RabbitMQ 重启后，持久化的队列 的过期时间会被重新计算。比如该参数设置为 1000，则表示该队列如果在 1 秒钟之内未
使用则会被删除。

```java
Map<String, Object> args = new HashMap<String, Object>();
args.put("x-expires", 1800000);
channel.queueDeclare("myqueue", false, false, false, args);
```

## 死信队列

Dead-Letter-Exchange(DLX) 称为死信交换机。当消息再一个队列中变成死信(Dead Message)之后，它能够被重新发送到另外一个交换器中，这个交换器就是 DLX，绑定 DLX 的队列称为死信队列。

正常消息变成 Dead Message 有如下几种情况：

- 消息被拒绝(Basic.Reject/Basic.Nack)， 并且设置 Requeue 参数为 false;
- 消息过期
- 消息数量达到队列最大容量

DLX 是正常的交换机，和普通的交换机没有区别。它能够被任何队列所绑定，实际上就是设置某个队列的属性。当队列中存在 dead message 的时候，RabbitMQ 会自动地将这个消息重新的发布到设置的 DLX 上去，进而被路由到另外一个队列上，即死信队列。可以监听这个队列中的消息以进行相应的处理，这个特性与将消息的 TTL 设置为 0 配合使用可以弥补 immediate 参数
的功能。

### 添加 DLX

通过在 channel.queueDeclare 方法中设置 x-dead-letter-exchange 参数来为这个队列添加 DLX

```java
channel.exchangeDeclare("dlx_exchange", "direct"); //创建 DLX: dlx_exchange
Map<String, Object> args = new HashMap<String, Object>();
args.put("x-dead-letter-exchange", " dlx_exchange ");
//为队列 myqueue 添加 DLX
channel.queueDeclare("myqueue", false, false, false, args);
```

也可以为这个 DLX 指定路由键，如果没有特殊指定，则使用原队列的路由键：

```java
args.put("x-dead-letter-routing-key", "dlx-routing-key");
```

使用示例

```java
channel.exchangeDeclare("exchange.dlx", "direct", true);
channel.exchangeDeclare("exchange.normal", "fanout", true);
Map<String, Object> args = new HashMap<String, Object>();
args.put("x-message-ttl", 10000);
args.put("x-dead-letter-exchange", "exchange.dlx");
args.put("x-dead-letter-routing-key", "routingkey");
channel.queueDeclare("queue.normal", true, false, false, args);
channel.queueBind("queue.normal", "exchange.normal", "");
channel.queueDeclare("queue.dlx", true, false, false, null);
channel.queueBind("queue.dlx", "exchange.dlx", "routingkey");
channel.basicPublish("exchange.normal", "rk",
MessageProperties.PERSISTENT_TEXT_PLAIN, "dlx".getBytes());
```

## 延迟队列

延迟队列存储的对象是对应的延迟消息，所谓“延迟消息”是指当消息被发送以后，并不想让消费者立刻拿到消息，而是等待特定时间后，消费者才能拿到这个消息进行消费。

在 AMQP 协议中，或者 RabbitMQ 本身没有直接支持延迟队列的功能，但是可以通过前面所介绍的 DLX 和 TTL 模拟出延迟队列的功能

在真实应用中，对于延迟队列可以根据延迟时间的长短分为多个等级，一般分为 5 秒、10秒、30 秒、1 分钟、5 分钟、10 分钟、30 分钟、1 小时这几个维度，当然也可以再细化一下

![image-20220702195004028](../Attachment/image-20220702195004028.png)

## 优先级队列

优先级队列可以通过设置队列的 x-max-priority 参数来实现

```java
Map<String, Object> args = new HashMap<String, Object>();
args.put("x-max-priority", 10); // 最高的优先级为10，最低为 0
channel.queueDeclare("queue.priority", true, false, false, args);
```

如果要发送消息给一个优先级队列，需要给消息设置优先级

```java
AMQP.BasicProperties.Builder builder = new AMQP.BasicProperties.Builder();
builder.priority(5);
AMQP.BasicProperties properties = builder.build();
channel.basicPublish("exchange_priority","rk_priority",properties,("messages
").getBytes());
```

**注意：**如果在消费者的消费速度大于生产者的速度且 Broker 中没有消息堆积的情况下，对发送的消息设置优先级也就没有什么实际意义

## RPC 远程过程调用

RPC 的主要功用是让构建分布式计算更容易，在提供强大的远程调用能力时不损失本地调用的语义简洁性

一般在 RabbitMQ 中进行 RPC 是很简单。客户端发送请求消息，服务端回复响应的消息。为了接收响应的消息，我们需要在请求消息中发送一个回调队列，可以使用默认的队列

```java
String callbackQueueName = channel.queueDeclare().getQueue();
BasicProperties props = new BasicProperties.Builder().replyTo(callbackQueueName).build();
channel.basicPublish("", "rpc_queue",props,message.getBytes());
// then code to read a response message from the callback_queue...
```

对于代码中涉及的 BasicProperties 这个类，其包含 14 个属性，这里就用到两个属性。

- replyTo：通常用来设置一个回调队列。
- correlationId：用来关联请求（request）和其调用 RPC 之后的回复（response）

如果像上面的代码中一样，为每个 RPC 请求创建一个回调队列，则是非常低效的。但是幸运的是这里有一个通用的解决方案——可以为每个客户端创建一个单一的回调队列。这样就产生了一个新的问题，对于回调队列而言，在其接收到一条回复的消息之后，它并不知道这条消息应该和哪一个请求匹配。这里就用到 correlationId 这个属性了，我们应该为每一个请求设置一个唯一的 correlationId。之后在回调队列接收到回复的消息时，可以根据这个属性匹配到相应的请求。如果回调队列接收到一条未知 correlationId 的回复消息，可以简单地将其丢弃。

![image-20220702202045570](../Attachment/image-20220702202045570.png)

1. 当客户端启动时，创建一个匿名的回调队列（名称由 RabbitMQ 自动创建，图 4-7 中的回调队列为 amq.gen-LhQz1gv3GhDOv8PIDabOXA）
2. 客户端为 RPC 请求设置 2 个属性：replyTo 用来告知 RPC 服务端回复请求时的目的队列，即回调队列；correlationId 用来标记一个请求
3. 请求被发送到 rpc_queue 队列中
4. RPC 服务端监听 rpc_queue 队列中的请求，当请求到来时，服务端会处理并且把带有结果的消息发送给客户端。接收的队列就是 replyTo 设定的回调队列
5. 客户端监听回调队列，当有消息时，检查 correlationId 属性，如果与请求匹配，那就是结果了

示例代码

```java
public class RPCServer {
    private static final String RPC_QUEUE_NAME = "rpc_queue";
    
    public static void main(String args[]) throws Exception {
        //省略了创建 Connection 和 Channel 的过程，具体可以参考 1.4.4 节
        channel.queueDeclare(RPC_QUEUE_NAME, false, false, false, null);
        channel.basicQos(1);
        System.out.println(" [x] Awaiting RPC requests");
        Consumer consumer = new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag,
                    Envelope envelope,
                    AMQP.BasicProperties properties,
                    byte[] body) throws IOException {
                AMQP.BasicProperties replyProps = new AMQP.BasicProperties
                .Builder()
                .correlationId(properties.getCorrelationId())
                .build();
                String response = "";
                try {
                    String message = new String(body, "UTF-8");
                    int n = Integer.parseInt(message);
                    System.out.println(" [.] fib(" + message + ")");
                    response += fib(n);
                } catch (RuntimeException e) {
                	System.out.println(" [.] " + e.toString());
                } finally {
                    channel.basicPublish("", properties.getReplyTo(),
                    replyProps, 
                    response.getBytes("UTF-8"));
                    channel.basicAck(envelope.getDeliveryTag(), false);
                }
            }
        };
        channel.basicConsume(RPC_QUEUE_NAME, false, consumer);
    }
    private static int fib(int n){
        if (n == 0) return 0;
        if (n == 1) return 1;
        return fib(n - 1) + fib(n - 2);
    }
}
```

```java
public class RPCClient {
    private Connection connection;
    private Channel channel;
    private String requestQueueName = "rpc_queue";
    private String replyQueueName;
    private QueueingConsumer consumer;
    public RPCClient() throws IOException, TimeoutException {
        //省略了创建 Connection 和 Channel 的过程，具体可以参考 1.4.4 节
        replyQueueName = channel.queueDeclare().getQueue();
        consumer = new QueueingConsumer(channel);
        channel.basicConsume(replyQueueName, true, consumer);
    }
    
    public String call(String message) throws IOException,ShutdownSignalException, ConsumerCancelledException,InterruptedException {
        String response = null;
        String corrId = UUID.randomUUID().toString();
        BasicProperties props = new BasicProperties.Builder()
            .correlationId(corrId)
            .replyTo(replyQueueName)
            .build();
        channel.basicPublish("", requestQueueName, props, message.getBytes());
        while(true){
            QueueingConsumer.Delivery delivery = consumer.nextDelivery();
            if(delivery.getProperties().getCorrelationId().equals(corrId)){
                response = new String(delivery.getBody());
                break;
            }
        }
        return response;
    }
    
    public void close() throws Exception{
    	connection.close();
    }
    
	public static void main(String args[]) throws Exception{
        RPCClient fibRpc = new RPCClient();
        System.out.println(" [x] Requesting fib(30)");
        String response = fibRpc.call("30");
        System.out.println(" [.] Got '" + response + "'");
        fibRpc.close();
    }
}
```

## 持久化

RabbitMQ 的持久化分为三部分：交换机的持久化、队列的持久化、消息的持久化

### 交换机持久化

交换器的持久化是通过在声明交换机时将 durable 参数置为 true 实现的。如果交换器不设置持久化，那么在 RabbitMQ 服务重启之后，相关的交换器元数据会丢失，不过消息不会丢失，只是不能将消息发送到这个交换器中了。对一个长期使用的交换器来说，建议将其置为持久化的

### 队列持久化

队列的持久化是通过在声明队列时将 durable 参数置为 true 实现的。如果队列不设置持久化，那么在 RabbitMQ 服务重启之后，相关队列的元数据会丢失，此时数据也会丢失

队列的持久化能保证其本身的元数据不会因异常情况而丢失，**但是并不能保证内部所存储的消息不会丢失**。

**要确保消息不会丢失**，需要将其设置为持久化。通过将消息的投递模式（BasicProperties 中的 deliveryMode 属性）设置为 2 即可实现消息的持久化。前面示例中多次提及的 MessageProperties.PERSISTENT_TEXT_PLAIN 实际上是封装了这个属性

```java
public static final BasicProperties PERSISTENT_TEXT_PLAIN =
new BasicProperties("text/plain",
    null,
    null,
    2,//deliveryMode
    0, null, null, null,
    null, null, null, null,
    null, null);
```

设置了队列和消息的持久化，当 RabbitMQ 服务重启之后，消息依旧存在

只设置队列持久化，重启之后消息会丢失；

只设置消息的持久化，重启之后队列消失，继而消息也丢失。

设置消息持久化而不设置队列的持久化显得毫无意义。

**注意**

可以将所有的消息都设置为持久化，但是这样会严重影响 RabbitMQ 的性能（随机）。写入磁盘的速度比写入内存的速度慢得不只一点点。对于可靠性不是那么高的消息可以不采用持久化处理以提高整体的吞吐量。在选择是否要将消息持久化时，需要在可靠性和吐吞量之间做一个权衡

### 交换机、队列和消息都设置了持久化，消息是否就不会丢失了？

答案是否定的

首先从消费者来说，如果在订阅消费队列时将 autoAck 参数设置为 true，那么当消费者接收到相关消息之后，还没来得及处理就宕机了，这样也算数据丢失。这种情况很好解决，将 autoAck 参数设置为 false，并进行手动确认

其次，在持久化的消息正确存入 RabbitMQ 之后，还需要有一段时间（虽然很短，但是不可忽视）才能存入磁盘之中。RabbitMQ 并不会为每条消息都进行同步存盘（调用内核的 fsync 1 方法）的处理，可能仅仅保存到操作系统缓存之中而不是物理磁盘之中。如果在这段时间内 RabbitMQ 服务节点发生了宕机、重启等异常情况，消息保存还没来得及落盘，那么这些消息将会丢失。

这个问题怎么解决呢？这里可以引入 RabbitMQ 的镜像队列机制，相当于配置了副本，如果主节点（master）在此特殊时间内挂掉，可以自动切换到从节点（slave），这样有效地保证了高可用性，除非整个集群都挂掉。**虽然这样也不能完全保证 RabbitMQ 消息不丢失**，但是配置了镜像队列要比没有配置镜像队列的可靠性要高很多，在实际生产环境中的**关键业务队列一般都会设置镜像队列**

还可以在发送端引入事务机制或者发送方确认机制来保证消息已经正确地发送并存储至 RabbitMQ 中，前提还要保证在调用 channel.basicPublish 方法的时候交换器能够将消息正确路由到相应的队列之中

## 生产者确认

默认情况下发送消息的操作是不会返回任何信息给生产者的，也就是默认情况下生产者是不知道消息有没有正确地到达服务器。如果在消息到达服务器之前已经丢失，持久化操作也解决不了这个问题，因为消息根本没有到达服务器，何谈持久化？RabbitMQ 针对这个问题提供了两种解决方式：

- 通过事务机制实现
- 通过发送方确认(publisher confirm) 机制实现

### 事务机制

abbitMQ 客户端中与事务机制相关的方法有三个 ：

- channel.txSelect: 该方法用于将当前信道设置成事务模式
- channel.txCommit: 该方法用于提交事务
- channel.txRollback: 该方法用于回滚事务

在通过 channel.txSelect 方法开启事务之后，我们便可以发布消息给 RabbitMQ 了，**如果事务提交成功，则消息一定到达了 RabbitMQ 中**，如果在事务提交执行之前由于 RabbitMQ 异常崩溃或者其他原因抛出异常，这个时候我们便可以将其捕获 ， 进而通过执行 channel.txRollback 方法来实现事务回滚

**注意**

这里的 RabbitMQ 中的事务机制与大多数数据库中的事务概念并不相同，需要注意区分。

```java
try {
    channel.txSelect();
    channel.basicPublish(exchange, routingKey, MessageProperties.PERSISTENT_TEXT_PLAIN, msg.getBytes());
    int result = 1 / 0;
	channel.txCommit();
} catch (Exception e) {
    e.printStackTrace();
    channel.txRollback();
}
```

![image-20220703092658148](../Attachment/image-20220703092658148.png)

1. 客户端发送 Tx.Select，将信道置为事务模式；
2. Broker 回复 Tx.Select-Ok，确认已将信道置为事务模式；
3. 在发送完消息之后，客户端发送 Tx.Commit 提交事务；
4. Broker 回复 Tx.Commit-Ok，确认事务提交。

发送多条消息，需要将消息和事务放入到循环中

```java
channel.txSelect();
for(int i = 0; i < LOOP_TIMES; i++) {
    try {
        channel.basicPublish("exchange", "routingKey", null,
        ("messages" + i).getBytes());
        channel.txCommit();
    } catch (IOException e) {
        e.printStackTrace();
        channel.txRollback();
    }
}
```

事务确实能够解决消息发送方和 RabbitMQ 之间消息确认的问题，只有消息成功被 RabbitMQ 接收，事务才能提交成功，否则便可在捕获异常之后进行事务回滚，与此同时可以进行消息重发。**但是使用事务机制会 “吸干” RabbitMQ 的性能**

### 发送方确认机制

生产者将信道设置成 confirm（确认）模式，一旦信道进入 confirm 模式，所有在该信道上面发布的消息都会被指派一个唯一的 ID（从 1 开始），一旦消息被投递到所有匹配的队列之后，RabbitMQ 就会发送一个确认（Basic.Ack）给生产者（包含消息的唯一 ID），这就使得生产者知晓消息已经正确到达了目的地了。**如果消息和队列是可持久化的，那么确认消息会在消息写入磁盘之后发出**。RabbitMQ 回传给生产者的确认消息中的 deliveryTag 包含了确认消息的序号，此外 RabbitMQ 也可以设置 channel.basicAck 方法中的 multiple 参数，表示到这个序号之前的所有消息都已经得到了处理

生产者通过调用 channel.confirmSelect 方法（即 Confirm.Select 命令）将信道设置为 confirm 模式

```java
try {
    channel.confirmSelect();//将信道置为 publisher confirm 模式
    //之后正常发送消息
    channel.basicPublish("exchange", "routingKey", null,
    "publisher confirm test".getBytes());
    if (!channel.waitForConfirms()) {
        System.out.println("send message failed");
        // do something else....
    }
} catch (InterruptedException e) {
	e.printStackTrace();
}
```

如果发送多条消息，只需要将 channel.basicPublish 和 channel.waitForConfirms 方法包裹在循环里面即可

![image-20220703094049876](../Attachment/image-20220703094049876.png)

### 异步 confirm 的实现

异步 confirm 方法的编程实现最为复杂。 在客户端 Channel 接口中提供的 addConfirmListener 方法可以添加 ConfirmListener 这个回调接口， 这个 ConfirmListener 接口包含两个方法：handleAck 和 handleNack ，分别用来处理 RabbitMQ 回传的 Basic.Ack 和 Basic.Nack 。在这两个方法中都包含有一个参数 deliveryTag（在 publisher confirm 模式下用来标记消息的唯一有序序号）。我们需要为每一个信道维护一个 “unconfirm” 的消息序号集合，每发送一条消息，集合中的元素加 1。每当调用 ConfirmListener 中的 handleAck 方法时，“unconfirm”集合中 删掉相应的一条(multiple 设置为 false)或者多条(multiple 设置为 true)记录。从程序运行效率上来看，**这个 “unconfirm” 集合最好采用有序集合 SortedSet 的存储结构**。事实上，Java 客户端 SDK中的 waitForConfirms 方法也是通过 SortedSet 维护消息序号的

```java
channel.confirmSelect();
channel.addConfirmListener(new ConfirmListener() {
    public void handleAck(long deliveryTag, boolean multiple) throws IOException {
        System.out.println("Nack, SeqNo: " + deliveryTag + ", multiple: " + multiple);
        if (multiple) {
            confirmSet.headSet(deliveryTag - 1).clear();
        } else {
            confirmSet.remove(deliveryTag);
        }
    }
    public void handleNack(long deliveryTag, boolean multiple) throws IOException {
        if (multiple) {
            confirmSet.headSet(deliveryTag - 1).clear();
        } else {
            confirmSet.remove(deliveryTag);
        }
        //注意这里需要添加处理消息重发的场景
    }
});
//下面是演示一直发送消息的场景
while (true) {
    long nextSeqNo = channel.getNextPublishSeqNo();
    channel.basicPublish(ConfirmConfig.exchangeName, ConfirmConfig.routingKey,MessageProperties.PERSISTENT_TEXT_PLAIN, ConfirmConfig.msg_10B.getBytes());
    confirmSet.add(nextSeqNo);
}
```

**强烈建议使用异步 confirm 的方式**

### 事务机制 VS 发送方确认机制

事务机制在一条消息发送之后会使发送端阻塞，以等待 RabbitMQ 的回应，之后才能继续发送下一条消息。

**发送方确认机制最大的好处在于它是异步的**，一旦发布一条消息，生产者应用程序就可以在等信道返回确认的同时继续发送下一条消息，当消息最终得到确认之后，生产者应用程序便可以通过回调方法来处理该确认消息，如果 RabbitMQ 因为自身内部错误导致消息丢失，就会发送一条 nack（Basic.Nack）命令，生产者应用程序同样可以在回调方法中处理该 nack 命令

publisher confirm 模式是每发送一条消息后就调用 channel.waitForConfirms 方法，之后等待服务端的确认，这实际上是一种串行同步等待的方式。事务机制和它一样，发送消息之后等待服务端确认，之后再发送消息。两者的存储确认原理相同，尤其对于持久化的消息来说，两者都需要等待消息确认落盘之后才会返回（调用 Linux 内核的 fsync 方法）。在同步等待的方式下，publisher confirm 机制发送一条消息需要通信交互的命令是 2 条：Basic.Publish 和 Basic.Ack；事务机制是 3 条:Basic.Publish、Tx.Commmit/.Commit-Ok（或者 Tx.Rollback/.Rollback-Ok），事务机制多了一个命令帧报文的交互，所以 QPS 会略微下降。

事务机制和 publisher confirm 机制两者是互斥的，不能共存

事务机制和 publisher confirm 机制确保的是消息能够正确地发送至 RabbitMQ，这里的 “发送至 RabbitMQ” 的含义是指消息被正确地发往至 RabbitMQ 的交换器，如果此交换器没有匹配的队列，那么消息也会丢失。所以在使用这两种机制的时候要确保所涉及的交换器能够有匹配的队列。更进一步地讲，发送方要配合 mandatory 参数或者备份交换器一起使用来提高消息传输的可靠性。

## 消息分发

当 RabbitMQ 队列拥有多个消费者时，队列收到的消息将以轮询（round-robin）的分发方式发送给消费者。**每条消息只会发送给订阅列表里的一个消费者**

在订阅消费队列之前，消费端程序调用了 channel.basicQos(5)，之后订阅了某个队列进行消费。RabbitMQ 会保存一个消费者的列表，每发送一条消息都会为对应的消费者计数，如果达到了所设定的上限，那么 RabbitMQ 就不会向这个消费者再发送任何消息。直到消费者确认了某条消息之后，RabbitMQ 将相应的计数减 1，之后消费者可以继续接收消息，直到再次到达计数上限。这种机制可以类比于 TCP/IP 中的“滑动窗口”。

## 消息的顺序性

在不使用任何 RabbitMQ 的高级特性，也没有消息丢失、网络故障之类异常的情况发生，并且只有一个消费者的情况下，最好也只有一个生产者的情况下可以保证消息的顺序性。如果有多个生产者同时发送消息，无法确定消息到达 Broker 的前后顺序，也就无法验证消息的顺序性

如果要保证消息的顺序性，需要业务方使用 RabbitMQ 之后做进一步的处理，比如在消息体内添加全局有序标识（类似 Sequence ID）来实现。

## QueueingConsumer 的缺陷

QueueingConsumer 本身有几个大缺陷，需要读者在使用时特别注意。首当其冲的就是内存溢出的问题，如果由于某些原因，队列之中堆积了比较多的消息，就可能导致消费者客户端内存溢出假死，于是发生恶性循环，队列消息不断堆积而得不到消化。

在客户端调用 channel.basicConsume 方法订阅队列的时候，RabbitMQ 会持续地将消息发往 QueueingConsumer 中，QueueingConsumer 内部使用 LinkedBlockingQueue 来缓存这些消息

这个内存溢出的问题可以使用 Basic.Qos 来得到有效的解决，Basic.Qos 可以限制某个消费者所保持未确认消息的数量，也就是间接地限制了QueueingConsumer 中的 LinkedBlockingQueue 的大小。注意一定要在调用 Basic.Consume 之前调用 Basic.Qos 才能生效

- QueueingConsumer 会拖累同一个 Connection 下的所有信道，使其性能降低；
- 同步递归调用 QueueingConsumer 会产生死锁；
- RabbitMQ 的自动连接恢复机制（automatic connection recovery）不支持 QueueingConsumer 的这种形式；
- QueueingConsumer 不是事件驱动的

**为了避免不必要的麻烦，建议在消费的时候尽量使用继承 DefaultConsumer 的方式**

## 去重机制

 RabbitMQ 有没有去重的机制来保证“恰好一次”呢？答案是并没有，不仅是 RabbitMQ，目前大多数主流的消息中间件都没有消息去重机制，也不保障“恰好一次”。**去重处理一般是在业务客户端实现**，比如引入 GUID（Globally Unique Identifier）的概念。针对 GUID，如果从客户端的角度去重，那么需要引入集中式缓存，必然会增加依赖复杂度，另外缓存的大小也难以界定。建议在实际生产环境中，业务方根据自身的业务特性进行去重，比如业务消息本身具备幂等性，或者借助 Redis 等其他产品进行去重处理。

## 虚拟主机 Virtual Host

每一个 RabbitMQ 服务器都能创建虚拟的消息服务器，我们称之为虚拟主机（virtual host），简称为 vhost。每一个 vhost 本质上是一个独立的小型 RabbitMQ 服务器，拥有自己独立的队列、交换器及绑定关系等，并且它拥有自己独立的权限。vhost 就像是虚拟机与物理服务器一样，它们在各个实例间提供逻辑上的分离，为不同程序安全保密地运行数据，它既能将同一个 RabbitMQ 中的众多客户区分开，又可以避免队列和交换器命名冲突。**vhost 之间是绝对隔离的**，无法将 vhost1 中的交换器与 vhost2 中的队列进行绑定，这样既保证了安全性，又可以确保可移植性。如果在使用 RabbitMQ 达到一定规模的时候，建议用户对业务功能、场景进行归类区分，并为之分配独立的 vhost。

vhost 是 AMQP 概念的基础，客户端在连接的时候必须制定一个 vhost。RabbitMQ 默认创建的 vhost 为“/”，

可以使用如下命名创建 vhost

```shell
$ rabbitmqctl add_vhost <vhost_name>
```

删除 vhost

```shell
$ rabbitmqctl delete_vhost <vhost_name>
```

查看 vhost

```shell
$ rabbitmqctl list_vhosts [vhostinfoitem...]
```

如

```shell
$ sudo rabbitmqctl list_vhosts name tracing
Listing vhosts ...
name    tracing
/       false
/mall   false
```

### 权限控制

在 RabbitMQ 中，权限控制则是以 vhost 为单位的。当创建一个用户时，用户通常会被指派给至少一个 vhost，并且只能访问被指派的 vhost 内的队列、交换器和绑定关系等

相关的授予权限命令为：`rabbitmqctl set_permissions [-p vhost] {user} {conf} {write} {read}` 。其中各个参数的含义如下所述。

- vhost：授予用户访问权限的 vhost 名称，可以设置为默认值，即 vhost 为“/”。
- user：可以访问指定 vhost 的用户名。
- conf：一个用于匹配用户在哪些资源上拥有可配置权限的正则表达式。
- write：一个用于匹配用户在哪些资源上拥有可写权限的正则表达式。
- read：一个用于匹配用户在哪些资源上拥有可读权限的正则表达式

注：可配置指的是队列和交换器的创建及删除之类的操作；可写指的是发布消息；可读指与消息有关的操作，包括读取消息及清空整个队列等。

使用示例

授予 root 用户可访问虚拟主机 vhost1，并在所有资源上都具备可配置、可写及可读的权限

```shell
$ rabbitmqctl set_permissions -p vhost1 root ".*" ".*" ".*"
Setting permissions for user "root" in vhost "vhost1"
```

授予 root 用户可访问虚拟主机 vhost2，在以 queue 开头的资源上具备可配置权限，并在所有资源上拥有可写、可读的权限

```shell
$ rabbitmqctl set_permissions -p vhost2 root "^queue.*" ".*" ".*"
Setting permissions for user "root" in vhost "vhost2"
```

### 用户角色

用户的角色分为 5 种类型:

- none：无任何角色。新创建的用户的角色默认为 none
- management：可以访问 Web 管理页面
- policymaker：包含 management 的所有权限，并且可以管理策略（Policy）和参数（Parameter）
- monitoring：包含 management 的所有权限，并且可以看到所有连接、信道及节点相关的信息
- administartor：包含 monitoring 的所有权限，并且可以管理用户、虚拟主机、权限、策略、参数等 ，administator 代表了最高的权限

## 消息路由

**不同于队列那样拥有自己的进程，交换器其实只是一个名称和绑定列表**。当消息发布到交换器时，实际上是由所连接的信道将消息上的路由键同交换器的绑定列表进行比较，然后再路由消息

## RabbitMQ 集群

RabbitMQ 集群对延迟非常敏感，应当只在本地局域网内使用。在广域网中不应该使用集群，而应该使用 Federation 或者 Shovel 来代替

RabbitMQ 中的每一个节点，不管是单一节点系统或者是集群中的一部分，要么是内存节点，要么是磁盘节点。内存节点将所有的队列、交换器、绑定关系、用户、权限和 vhost 的元数据定义都存储在内存中，而磁盘节点则将这些信息存储到磁盘中。单节点的集群中必然只有磁盘类型的节点，否则当重启 RabbitMQ 之后，所有关于系统的配置信息都会丢失。不过在集群中，可以选择配置部分节点为内存节点，这样可以获得更高的性能。

RabbitMQ 只要求在集群中至少有一个磁盘节点，所有其他节点可以是内存节点。当节点加入或者离开集群时，它们必须将变更通知到至少一个磁盘节点。如果只有一个磁盘节点，而且不凑巧的是它刚好崩溃了，那么集群可以继续发送或者接收消息，但是不能执行创建队列、交换器、绑定关系、用户，以及更改权限、添加或删除集群节点的操作了。也就是说，如果集群中唯一的磁盘节点崩溃，集群仍然可以保持运行，但是直到将该节点恢复到集群前，你无法更改任何东西。所以在建立集群的时候应该保证有两个或者多个磁盘节点的存在。在内存节点重启后，它们会连接到预先配置的磁盘节点，下载当前集群元数据的副本。当在集群中添加内存节点时，确保告知其所有的磁盘节点（内存节点唯一存储到磁盘的元数据信息是集群中磁盘节点的地址）。只要内存节点可以找到至少一个磁盘节点，那么它就能在重启后重新加入集群中。

## Federation

Federation 插件的设计目标是使 RabbitMQ 在不同的 Broker 节点之间进行消息传递而无须建立集群，该功能在很多场景下都非常有用：

- Federation 插件能够在不同管理域（可能设置了不同的用户和 vhost，也可能运行在不同版本的 RabbitMQ 和 Erlang 上）中的 Broker 或者集群之间传递消息。
- Federation 插件基于 AMQP 0-9-1 协议在不同的 Broker 之间进行通信，并设计成能够容忍不稳定的网络连接情况。
- 一个 Broker 节点中可以同时存在联邦(Federation)交换器（或队列）或者本地交换器（或队列），只需要对特定的交换器（或队列）创建 Federation 连接（Federation link）。
- Federation 不需要在 N 个 Broker 节点之间创建 $O(N^2)$ 个连接（尽管这是最简单的使用方式），这也就意味着 Federation 在使用时更容易扩展。

Federation 插件可以让多个交换器或者多个队列进行联邦。一个联邦交换器（federated exchange）或者一个联邦队列（federated queue）接收上游（upstream）的消息，这里的上游是指位于其他 Broker 上的交换器或者队列。联邦交换器能够将原本发送给上游交换器（upstream exchange）的消息路由到本地的某个队列中；联邦队列则允许一个本地消费者接收到来自上游队列（upstream queue）的消息。

## Shovel

与 Federation 具备的数据转发功能类似，Shovel 能够可靠、持续地从一个 Broker 中的队列（作为源端，即 source）拉取数据并转发至另一个 Broker 中的交换器（作为目的端，即 destination）。作为源端的队列和作为目的端的交换器可以同时位于同一个 Broker 上，也可以位于不同的 Broker 上

Shovel 的主要优势在于：

- 松耦合。Shovel 可以移动位于不同管理域中的 Broker（或者集群）上的消息，这些 Broker（或者集群）可以包含不同的用户和 vhost，也可以使用不同的 RabbitMQ 和 Erlang 版本。
- 支持广域网。Shovel 插件同样基于 AMQP 协议在 Broker 之间进行通信，被设计成可以容忍时断时续的连通情形，并且能够保证消息的可靠性。
- 高度定制。当 Shovel 成功连接后，可以对其进行配置以执行相关的 AMQP 命令。

**当某个队列中的消息堆积严重时，比如超过某个设定的阈值，就可以通过 Shovel 将队列中的消息移交给另一个集群**

# SpringBoot 集成 RabbitMQ

## 常用注解

| 注解            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| @RabbitListener | 该注解放在方法上，当接收到 RabbitMQ 指定消息队列的消息时，调用其修饰的方法进行处理。该注解适用于 push 这种被动的的模式。 |
|                 |                                                              |
|                 |                                                              |

 ## 配置消息转换器

默认情况下，消息转换是通过 SimpleMessageConverter 来实现的，它能够将简单类型（如String）和 Serializable 对象转换成 Message 对象。但是，Spring 为 RabbitTemplate 提供了多个消息转换器，内容如下：

- Jackson2JsonMessageConverter：使用 Jackson 2 JSON 实现对象和 JSON 的相互转换。
- MarshallingMessageConverter：使用 Spring 的 Marshaller 和 Unmarshaller 进行转换。
- SerializerMessageConverter：使用 Spring 的 Serializer 和 Deserializer 转换 String 和任意种类的原生对象。
- SimpleMessageConverter：转换 String、字节数组和 Serializable 类型。
- ContentTypeDelegatingMessageConverter：基于 contentType 头信息，将转换功能委托给另外一个 MessageConverter。
- MessagingMessageConverter：将消息转换功能委托给另外一个 MessageConverter，并将头信息的转换委托给 AmqpHeaderConverter。

对于基于JSON的转换，我们可以按照如下的方式来配置 Jackson2JsonMessageConverter：
```java
@Bean
public MessageConverter messageConverter() {
	return new Jackson2JsonMessageConverter();
}
```

Spring Boot 的自动配置功能会发现这个 bean，并将它注入 RabbitTemplate 中，替换默认的消息转换器

## MessageProperties 对象

在使用 convertAndSend() 的时候，我们无法快速访问 MessageProperties 对象。不过，此时 MessagePostProcessor 可以帮助我们：

```java
@Override
public void sendOrder(Order order) {
    rabbit.convertAndSend("tacocloud.order.queue", order, new MessagePostProcessor() {
        @Override
        public Message postProcessMessage(Message message) throws AmqpException {
            MessageProperties props = message.getMessageProperties();
            props.setHeader("X_ORDER_SOURCE", "WEB");
            return message;
        }
    });
}
```


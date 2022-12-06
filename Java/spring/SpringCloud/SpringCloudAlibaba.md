# SpringCloud 常用的注解

| 注解                   | 描述                                                         |
| ---------------------- | ------------------------------------------------------------ |
| @EnableDiscoveryClient | 标注在 Spring 启动类上,表明将该应用注册到注册中心上          |
| @EnableFeignClients    | 开启 OpenFeign 来调用远程服务，该注解会扫描该注解所修饰的类所在的包中被 @FeignClient 注解修饰的接口，并生成对应接口的实现类注入到 Spring 容器中 |
| @FeignClien            | 该注解的 value 参数用于指定被调用的服务在注册中心的名字，表明该接口的实现类要调用对应的服务 |
| @SentinelResource      | 该注解用来标识资源是否被限流、降级                           |
| @SentinelRestTemplate  | 该注解用于放置在 RestTemplate 上，从而达到熔断，服务降级的效果的效果 |
| @RefreshScope          | 该注解用于刷新配置                                           |

# 注册中心 Nacos

## Nacos VS Spring Cloud 

Nacos = Spring Cloud Eureka + Spring Cloud Config

Nacos 可以与 Spring, Spring Boot, Spring Cloud 集成，并能代替 Spring Cloud Eureka, Spring Cloud Config。

- 通过 Nacos Server 和 spring-cloud-starter-alibaba-nacos-config 实现配置的动态变更。

- 通过 Nacos Server 和 spring-cloud-starter-alibaba-nacos-discovery 实现服务的注册与发现。

## Nacos 的使用

在 Nacos 中，不论是服务提供者还是服务消费者，都需要注册到 Nacos 中，如果服务消费者不注册到 Nacos 上，那么它将无法调用服务提供者提供的服务

微服务之间通过 HTTP 来进行通信，因此还需要 Web 模块

### 服务提供者

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

```yaml
spring:
  application:
    name: nacos-provider # 当前服务在 Nacos 中的名字
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 # Nacos Server 的 IP 地址和端口号
server:
  port: 8081
```

NacosProviderApplication.java

```java
@SpringBootApplication
@EnableDiscoveryClient // 将服务注册到注册到注册中心中
public class NacosProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(NacosProviderApplication.class, args);
    }
}
```

HelloController

```java
@RestController
@RequestMapping("/hello")
public class HelloController {
    @GetMapping("/{username}")
    public String sayHello(@PathVariable String username){
        return "Hello, I'm : " + username;
    }
}
```

### 服务消费者

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>       
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

NacosConsumerApplication.java

```java
@SpringBootApplication
@EnableDiscoveryClient // 将当前应用注册到注册中心中
@EnableFeignClients // 开启 OpenFeign 来调用远程服务，并且扫描该类所在的包及其字包下带有 @FeignClient 注解的接口，并生成对应接口的实现类交给 Spring 容器，底层本质上使用的应该是 RestTemplate
public class NacosConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(NacosConsumerApplication.class, args);
    }
}
```

IHelloService.java

```java
@FeignClient(value = "nacos-provider")
@RequestMapping("/hello") // 路径要和服务提供者对应的 Controller 匹配，不然发送 Http 请求的时候可能找不到对应的资源
public interface IHelloService {
    @GetMapping("/{username}")
    String hello(@PathVariable("username") String username); // @PathVaraible("username") 不可以省略，否则会启动失败
}
```

HelloController.java

```java
@RestController
@RequestMapping("/hello1") // hello1 得和 IHelloService 中的 hello 有所区分
public class HelloController {
    @Autowired
    private IHelloService helloService;

    @GetMapping("/{username}")
    public String hello(@PathVariable String username) {
        return helloService.hello(username);
    }
}
```

## 临时实例和非临时实例

nacos 将服务分成临时实例和非临时实例两种，如果不配置，默认的服务就是临时实例，如果要配置非临时实例需要通过如下配置：

```yaml
spring:
  cloud:
    nacos:
      discovery:
        ephemeral: false # 该服务为非临时实例
```

### 临时实例和非临时实例的区别

- 临时实例需要主动发送心跳包给 nacos, 在指定时间段内 nacos 没有收到临时实例的心跳包，则会将临时实例进行剔除

- 非临时实例则不需要进行心跳检测，由 nacos 主动询问非临时实例的状态

## 消息推送

nacos 采取 pull 和 push 两种方式进行服务获取和服务变更通知。服务消费者每隔一段时间到 nacos 中拉取服务列表更新状态，同时，如果 nacos 发现服务列表发生变化，nacos 会主动向服务消费者推送最新的服务列表，从而让服务列表的变更更快的被服务消费者所感知

## Nacos 和 Eureka 对比

### 共同点

1. 都支持服务注册和服务拉取
2. 都支持服务提供者用心跳方式进行健康检测

### 区别

1. Nacos 支持服务端主动检测服务提供者的状态：临时实例采用心跳检测的方式，非临时实例采用主动检测的方式
2. 临时实例心跳不正常会被剔除，非临时实例则不会被剔除
3. Nacos 支持在服务列表变更时主动将变更推送给服务消费者，从而让更新及时的被服务消费者所感知到
4. Nacos 集群默认采用 AP 方式，当集群中存在非临时实例时，采用 CP 模式；Eureka 采用 AP 模式

# 配置中心 Nacos

[官方文档](https://spring-cloud-alibaba-group.github.io/github-pages/hoxton/zh-cn/index.html#_spring_cloud_alibaba_nacos_config)

使用 Spring Cloud Alibaba Nacos Config，可基于 Spring Cloud 的编程模型快速接入 Nacos 配置管理功能

Nacos Config 使用 DataId 和 GROUP 确定一个配置

配置中心的配置需要写在 bootstrap.yaml 文件中

Nacos Config 通过 namesapce > group > data id  三种级别来实现不同场景下的配置文件的切换

```properties
spring.cloud.nacos.config.namespace=b3404bc0-d7dc-4855-b519-570ed34b62d7
spring.cloud.nacos.config.group=DEVELOP_GROUP
```

data id 默认的规则是：`dataId = ${spring.cloud.nacos.config.prefix} + '-' + ${profile} + . + ${spring.cloud.nacos.config.file-extension}`：

- `${spring.cloud.nacos.config.prefxi}` 的默认值是: `spring.application.name`
- `${profile}` 为 `${spring.profiles.active}` 设置的值，如 dev,test 等
- `${spring.cloud.nacos.config.file-extension}` 的默认值是：`properties`

因此如果默认什么都不设置，应用会到 nacos 配置中心中寻找 data id 为 `${spring.applicaiotn.name}.properties` 且 group 为 DEFAULT_GROUP 的配置文件

所以在 nacos 配置中心设置 data id 时，需要带上对应配置文件类型的扩展名，如：nacos-consumer.yaml, nacos-consumer.properties。否则的话匹配不上。

多个共享配置间的一个优先级的关系我们约定：按照配置出现的先后顺序，即后面的优先级要高于前面的。

| 注解          | 描述                                               |
| ------------- | -------------------------------------------------- |
| @RefreshScope | 该注解所修饰的 Bean 可以在运行时动态的更新它的属性 |

## 配置的优先级

在 Spring Boot 中有两种上下文配置，一种是 bootstrap，另一种是 application。bootstrap 是应用程序的父上下文，也就是说 bootstrap 的加载优先于 application。 由于在加载远程配置之前，需要读取 Nacos 配置中心的服务地址信息，所以 Nacos 服务地址等属性配置需要放在 bootstrap 配置文件中。bootstrap 中的配置项不能够被 application 中的同名的配置项所覆盖。

bootstrap 配置文件常用的场景：

- 引导配置，用于连接到 Spring Cloud 的配置中心，加载应用真正的配置
- 固定一些不能够被覆盖的配置，默认情况下，本地的配置文件不能覆盖远程配置

Nacos Config 目前提供了三种配置能力从 Nacos 拉取相关的配置

- A: 通过 `spring.cloud.nacos.config.shared-dataids` 支持多个共享 Data Id 的配置
- B: 通过 `spring.cloud.nacos.config.ext-config[n].data-id` 的方式支持多个扩展 Data Id 的配置
- C: 通过内部相关规则(应用名、应用名 + Profile )自动生成相关的 Data Id 配置

当三种方式共同使用时，他们的一个优先级关系是:A < B < C

# bootstrap 文件默认不加载的问题

spring cloud 2020.0.0 之后的版本包括 2020.0.0, 默认不加载 bootstrap 文件，如果要加载 bootstrap 文件，需要导入相关的依赖：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```

## Spring Cloud Config VS Spring Cloud Alibaba Nacos Config

SPring Cloud Alibaba Config 是 Spring Cloud config 中 Config Server 和 Config Client 的替代品。也就是说，我们可以在 Nacos 中进行配置，然后微服务从 Nacos 中拉取配置进行进行设置

## 配置的存储

Nacos Server 的数据存储默认采用的是 Derby 数据库，除此之外，还支持 Mysql 数据库

## 配置的管理和更新

在 Spring 启动时，会把配置加载到 Environment 中。当创建一个 Bean 时，可以从 Environment 中把一些属性通过 @Value 的形式进行注入。

而在 Spring Cloud 中要实现统一配置管理并且动态刷新配置，需要解决的问题有：

- 如何将远程服务器上的配置加载到 Environment 中
- 配置变更时，如何将新的配置更新到 Environment 中，保证配置变更时，可以进行属性值的动态刷新

## 配置热更新

配置热加载的实现方式有两种：

1. 使用 @RefreshScope 注解

   在使用了 @Value 注解的类上，加上 @RefreshScope 注解即可，之后在 nacos 配置中心中修改配置之后，服务中的配置就会自动更新

2. 使用 @ConfigurationProperties 注解, 推荐使用这种方式，该方式不需要使用 @RefreshScope 注解：

   ```java
   @ConfigurationProperties(prefix="pattern")
   @Component
   @Getter
   @Setter
   public class PatternProperties {
       private String dateformat;
   }
   
   
   @RestController
   @RequestMapping("/user")
   public class UserController {
       @Autowired
       private PatternProperties properties;
       
       @GetMapping("/pattern")
       public String pattern() {
           return properties.getDateformat();
       }
   }
   ```

## 配置文件的优先级

>  服务名-profile.yaml > 服务名.yaml > 本地配置

配置中心的配置的优先级大于本地环境，带有 profile 的配置文件的优先级大于不带 profile 配置文件的优先级

# Nacos 多级存储模型

对于大型的项目，同一个服务会部署在不同的区域，如一个在深圳，一个在上海。服务之间的调用尽可能在同区域之间进行调用，服务跨区域的调用延迟太高。Nacos 为了解决这个问题，提出了多级存储模型：

> 服务 => 集群 => 服务实例                                            

服务之间的调用遵循：优先调用同区域的服务，当同区域的服务不可用时，才会跨区域进行调用，并且跨区域进行调用时，会给出对应的警告。

## 配置集群的方式

```yaml
spring:
  cloud:
    nacos:
      discovery:
        cluster-name: hz # 集群的名称
```

# Feign 的最佳实践

将 Feign 相关的接口，实体类等都抽取到一个模块中去，当其他的微服务需要使用对应的 Feign API 时，引入封装好的模块即可

需要注意的是，如果将 Feign 相关的接口抽取到一个独立的模块中去之后，Feign 相关的模块也就不在 SpringBootApplication 的扫描范围之内了，也就是说不会在运行时生成 Feign 接口的动态代理类了，解决的办法如下：

1. 指定 FeignClient 所在的包

   ```java
   @EnableFeignClient(basePackages="com.autmaple.feign.clients")
   ```

或者是：

2. 指定 FeignClient 对应的字节码，只导入需要的 FeignClient

   ```java
   @EnableFeignClient(clients = {UserClient.class})
   ```

# Sentinel

[官方文档](https://github.com/alibaba/Sentinel/wiki/%E6%96%B0%E6%89%8B%E6%8C%87%E5%8D%97)

[Sentinel](https://github.com/alibaba/Sentinel) 具有以下特征:

- **丰富的应用场景**： Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、实时熔断下游不可用应用等。
- **完备的实时监控**： Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况。
- **广泛的开源生态**： Sentinel 提供开箱即用的与其它开源框架/库的整合模块，例如与 Spring Cloud、Dubbo、gRPC 的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。
- **完善的 SPI 扩展点**： Sentinel 提供简单易用、完善的 SPI 扩展点。您可以通过实现扩展点，快速的定制逻辑。例如定制规则管理、适配数据源等。

可以将 Sentinel 和 OpenFeign 结合起来使用从而实现服务熔断和降级

Sentinel 实现限流是对某一个资源进行限流，而熔断则是微服务间调用时如果出现其他微服务不可用时的一个保护措施。这两者的设置并不冲突

## 将 Sentinel 配置放置在配置中心

原理图

![Remote push rules to config center](../../../Attachment/53381986-a0b73f00-39ad-11e9-90cf-b49158ae4b6f.png)

1. 我们直接在配置中心创建规则，配置中心将规则推送到客户端；
2. Sentinel 控制台也从配置中心去获取配置信息。

### 配置步骤

修改application.yml配置文件，添加Nacos数据源配置：

```yaml
spring:
  cloud:
    sentinel:
      datasource:
        ds1:
          nacos: # 使用 nacos 作为配置中心
            server-addr: localhost:8848 # 配置中心的地址
            dataId: ${spring.application.name}-sentinel # 得和配置中心的一样
            groupId: DEFAULT_GROUP # 得和配置中心的一样
            data-type: json
            rule-type: flow
```

添加的配置信息如下

```json
[
    {
        "resource": "/rateLimit/byUrl",
        "limitApp": "default",
        "grade": 1,
        "count": 1,
        "strategy": 0,
        "controlBehavior": 0,
        "clusterMode": false
    }
]
```

相关参数解释：

- resource：资源名称；
- limitApp：来源应用；
- grade：阈值类型，0表示线程数，1表示QPS；
- count：单机阈值；
- strategy：流控模式，0表示直接，1表示关联，2表示链路；
- controlBehavior：流控效果，0表示快速失败，1表示Warm Up，2表示排队等待；
- clusterMode：是否集群。

## Sentinel 和 Hystrix 保护技术的对比

|                | Sentinel                                       | Hystrix                       |
| -------------- | ---------------------------------------------- | ----------------------------- |
| 隔离策略       | 信号量隔离                                     | 线程池隔离/信号量隔离         |
| 熔断降级策略   | 基于慢调用或异常比例                           | 基于失败比率                  |
| 实时指标实现   | 滑动窗口                                       | 滑动窗口(基于 RxJava)         |
| 规则配置       | 支持多种数据源                                 | 支持多种数据源                |
| 扩展性         | 多个扩展点                                     | 插件的形式                    |
| 基于注解的支持 | 支持                                           | 支持                          |
| 限流           | 基于 QPS，支持关于调用关系的限流               | 有限的支持                    |
| 流量整形       | 支持慢启动，匀速排队模式                       | 不支持                        |
| 系统自适应保护 | 支持                                           | 不支持                        |
| 控制台         | 开箱即用，可配置规则，查看秒级监控，机器发现等 | 不完善                        |
| 常见框架适配   | Servlet, Spring Cloud, Dubbo,gRPC 等           | Servlet, Spring Cloud Netfilx |

## 雪崩问题

服务调用链路中的某个服务出现故障，从而引起整个链路中所有为服务都不可用的一种现象，这种现象称其为雪崩

### 解决方案

- 超时处理：设定超市时间，请求超过一定时间没有响应就返回错误信息，不会无休止的等待。该种方式并不能够从根本上的解决问题。
- 舱壁模式：限定每个业务所能够使用的线程数，避免耗尽整个计算机的资源，因此也叫线程隔离
- 熔断降级：由断路器统计业务执行的异常比例，如果超出阈值则会熔断该业务，拦截访问该业务的一切请求，快速失败，从而快速释放资源，避免了资源被消耗殆尽。
- 流量控制：限制业务访问的 QPS(Query Per Second)，避免服务因为流量的突增而出现故障

## 限流的算法

### 计数器算法

计数器算法是一种比较简单的限流实现算法，在指定的周期内累加访问次数，当访问次数达到设定的阈值时，触发限流的策略，当进入下一个时间周期时，进行访问次数的清零。这种算法可以放在短信发送上，例如一分钟内同一个用户只能够发送一次短信。

该算法有一个临界问题：当限制 1 分钟内只处理 100 个请求，此时如果在 58s 是有 100 个请求到来，在 1:02s 时又到来 100 个请求，那么在 4s 内就需要处理 200 个请求，超过了阈值

### 滑动窗口算法

滑动窗口算法就是为了解决计数器算法的临界问题。滑动窗口是一种流量控制技术，在 TCP 网络协议中，就采用了滑动窗口算法来解决网络拥塞的情况。

滑动窗口算法的原理是在固定窗口中分割出多个小时间窗口，分别在每个小时间窗口中记录访问次数，然后根据时间将窗口往前滑动并删除过期的小时间窗口，最终只需要同级滑动窗口范围内的所有小时间窗口总的计数即可

Sentinel 就是采用滑动窗口算法来实现限流的

### 令牌桶限流算法(Token Bucket)

令牌桶是网络流量整形(Traffic Shapping) 和速率限制(Rate Limiting) 中最常使用的一种算法。对于每一个请求，都需要从令牌桶中获取一个令牌，如果没有获取令牌，则需要出发限流策略。系统会以一个恒定的速率往固定容量的令牌桶中放入令牌，如果此时有客户端请求过来，则需要从令牌桶中拿到令牌以获得访问资源。如果令牌桶满了，则丢弃令牌

![基于 Redis 使用令牌桶算法实现流量控制](../../../Attachment/token-bucket-02.png)

假设令牌生成的速度为每秒 10 个，也就是等同于 QPS(Query Per Second) = 10,此时在请求获取令牌的时候，会存在三种情况：

- 请求速度大于令牌生成的速度，那么令牌很快就会被消耗完，或许再进来的请求就会被限流
- 请求速度等于令牌生成的速度，此时流量处于平稳状态。
- 请求速度小于令牌生成的速度，说明此时系统的并发数并不高，请求能够被正常处理

由于令牌桶有固定的大小，当请求速度小于令牌生成的速度时，令牌很快就会被填满。所以令牌桶能够处理突发流量，也就是在短时间内新增的流量系统能够正常处理，这是令牌桶的特性。

### 漏桶限流算法

漏桶限流算法的主要作用是控制数据注入网络的速率，平滑网络上的突发流量。

![漏桶算法和令牌桶算法，区别到底在哪里](../../../Attachment/9f645abbc8477ff790ab663b4d75f21e.png)

在漏桶限流算法内部维护一个容器，这个容器会以恒定的速度出水，不管上面的水流速度多块，漏桶漏水的速度始终不变。实际上消息中间件就是使用了漏桶限流的思想，不管生产者的请求量有多大，消息的处理能力取决于消费者

在漏桶限流算法中，存在如下几种可能的情况：

- 请求速度大于漏桶流出水滴的速度，也就是请求数超出当期服务所能够处理的极限，将会出发限流策略
- 请求速度小于或者等于漏桶流出水滴的作用，也就相当于服务端的处理能力正好满足客户端的请求量，将正常执行

漏桶限流算法和令牌桶限流算法的实现原理相差不大，最大的区别是漏桶限流算法无法处理短时间内的突发流量，漏桶限流算法是一种恒定速度的限流算法。比如设置桶的大小都是100，填充令牌的速度是 10个/s，漏水的速度是 10滴/s, 如果在某一时刻，突然出现 100 个请求，此时令牌桶限流可以直接处理，但是漏桶限流算法只能够每秒解决 10 个请求。

## 服务降级的指标

服务降级需要有一个参考指标，一般来说，有以下几种常见的方案：

- 平均响应时间：比如 1s 内持续进入 5 个请求，对应时刻的平均响应时间均超过阈值，那么接下来在一个固定的时间窗口内，对这个方法的访问都会自动熔断。
- 异常比例：当某个方法每秒调用所获得的异常总数的比例超过设置的阈值时，该资源会自动进入降级状态，也就是在接下来的一个固定时间窗口中，对该方法的调用会自动返回。
- 异常数量：和异常比例类似，当某个方法在指定时间窗口内获得的异常数量超过阈值时，会自动触发熔断

## Sentinel 资源保护规则

Sentinel 支持多种保护规则：流量控制规则，熔断降级规则，系统保护规则，来源访问控制规则，热点参数规则

### 流量控制

#### 流控模式

在 Sentinel 控制台中添加流控规则的时候，有三种可选的流控模式：

- 直接模式：统计当前资源的请求，触发阈值时，对当前资源直接进行限流，这是默认的流控模式
- 关联模式：统计与当前资源相关的另一个资源，触发阈值时，对当前资源进行限流
- 链路模式：统计从指定链访问到本资源的请求，触发阈值时，对指定链路进行限流

##### 关联模式

该种模式下，对 A 资源配置关联流控规则：A 关联 B。该配置的含义就是对 B 进行统计，当 B 的访问超出阈值时，就对 A 作出限制。

使用场景：比如用户支付时，需要修改订单的状态，同时用户要查询订单。查询和修改操作会争抢数据库锁，产生竞争。业务需求就是有限支付和更新订单的业务，因此当修改订单业务出发阈值时，需要对查询业务进行限流。

当满足下面的条件时，就可以使用关联规则：

- 两个有竞争关系的资源
- 一个优先级高，一个优先级低

小技巧：给 A 限流，就给 A 配置关联规则。

##### 链路模式

只针对从指定链路访问到本资源的请求做统计，判断是否超出阈值。该种方式主要是对不同的来源进行一个限制

例如有两条请求链路：

- /test1 -> /common
- /test2 -> /common

如果只希望统计从 /test2 进入到 /common 的请求，则可以为 /common 配置流控规则

需求：有查询订单和创建订单的业务，两者都需要查询商品。统计从查询订单进入到查询商品的请求，并对查询商品的业务进行限流。

步骤：

1. 在 OrderService 中添加一个 queryGoods() 方法，不用实现业务
2. 在 OrderController 添加一个 /order/query 的端点，并在该端点中调用 OrderService 的 queryGoods() 方法
3. 在 OrderController 中添加一个 /order/save 的端点，并在该端点中调用 OrderService 的 queryGoods() 方法
4. 给 OrderService 的 queryGoods() 方法配置限流规则。

**注意**：

Sentinel 默认只能够监听 Controller 中的链路，如果需要监控 Service 层中的资源，则需要在 Service 层对应的资源上(即方法上)加上 @SentinelResource 注解。

同时 Sentinel 默认会对 Controller 中的方法做 context 整个，从而导致链路模式的流控失效，需要修改 Application.yaml：

```yaml
spring:
  cloud:
    sentinel:
      web-context-unify: false # 关闭 context 整合
```

#### 流控效果

- warm-up: 预热模式，该模式是应对服务冷启动的一种方案。请求阈值初始值是 `threshold /coldFactor` ，持续指定时长后，逐渐提高到 threshold 值，而 coldFactor 的默认值是 3.

  例如：如果设置 QPS 为 10，预热时间为 5s 的话，初始的阈值就是 10 / 3 = 3. 然后在指定的 5s 时间间隔中逐步增长到 10。

- 排队等待：当请求超过 QPS 阈值时，快速失败和 warm up 都会直接拒绝新的请求并抛出异常。而排队等待则是让所有请求进入一个队列中，然后按照阈值允许的时间间隔，依次执行。后来的请求必须等待前面的请求执行完成，如果请求预期的等待时间超出最大时长，则会被拒绝，直接失败。**该种流控方式可以做到流量整形的效果。**

  例如：QPS = 5， 意味着没 200ms 处理一个请求; timeout = 2000, 意味着预期等待超过 2000ms 的请求会被直接拒绝并抛出异常。

#### 热点参数限流

在 Sentinel 中可以对资源中的某些参数值配置限流规则，这种模式的应用场景就是：可以为热点数据配置更多的资源

**注意：** Sentinel 的热点参数限流默认对 SpringMVC 不起作用。如果要对 SpringMVC 起作用，需要在对应的 endpoint 中添加 @SentinelResource 注解。

### 基于并发数和 QPS 的流量控制

Sentinel 流量控制统计有两种类型，通过 Grade 属性来控制：

- 并发线程数(FLOW_GRADE_TRHEAD)
- QPS(FLOW_GRADE_QPS)

#### 并发线程数

并发线程数限流用来保护业务线程不被耗尽。比如 A 服务调用 B 服务，而 B 服务因为某种原因导致服务不稳定或者响应延迟，那么对于 A 服务来说，他的吞吐量会下降，也意味着占用跟更多的线程(线程阻塞后一致为释放),极端情况下造成线程池耗尽。

针对这种问题，一种常见的解决方案是通过不同业务逻辑使用不同的线程池来隔离业务自身的资源抢夺问题，但是这个方案同样会造成线程数量过多带来的线程上下文切换的问题

Sentinel 并发线程数限流就是统计当期请求的上下文线程数量，如果超出阈值，新的请求就会被拒绝。

#### QPS 的流量控制行为

当 QPS 超过阈值时，就会触发流量控制行为，这种行为是通过 controlBehavior 来设置的，它包含：

- 直接拒绝(RuleConstant.CONTROLE_BEHAVIOR_DEFAULT)
- Warm Up(RuleConstant.CONTROLE_BEHAVIOR_WARM_UP)，冷启动或者预热
- 匀速排队(RuleConstant.CONTROLE_BEHAVIOR_RATE_LIMITER)
- 冷启动 + 匀速排队 (RuleConstant.CONTROL_BEHAVIOR_WARM_UP_RATE_LIMITER)

直接拒绝是默认的流量控制方法， 也就是请求流量超出阈值时，直接抛出一个 FlowException

Warm Up 是一种冷启动(预热)的方式，当流量突然增大时，也就意味着系统从空闲状态突然切换到繁忙状态，有可能瞬间把系统压垮。当我们希望请求处理的数量逐步递增，并在一个预期时间之后达到处理请求的最大值时，Warm Up 流控方式就能够达到我们想要的效果

匀速排队的方法会严格控制请求通过的间隔时间，也就是让请求以匀速的速率通过，其实相当于漏桶限流算法。例如当 QPS = 2 时，表示每个 500ms 才允许下一个请求。这种方式的好处就是可以处理间隔性突发流量

## 隔离和熔断降级

不管是隔离还是降熔断降级，都是对 **客户端(调用方)** 的保护

### 熔断

熔断的核心就是断路器，断路器会统计异常请求的次数和频率，然后根据开发人员的配置判断是否需要熔断对应的服务。并且可以配置熔断的时间，在指定的熔断时间之后，尝试开放被熔断的服务。

#### 熔断策略

1. 慢调用：即某次请求的处理时间超过阈值，则认为是一次慢调用
2. 异常比例：统计指定时间内异常比例，根据异常比例来进行熔断
3. 异常数: 统计指定时间内的异常数，根据异常数来进行熔断。

### 降级

在 SpringCloud 中，微服务的调用都是通过 Feign 来实现的，因此做客户端保护必须整合 Feign 和 Sentinel，步骤：

1. 开启 Feign 的 Sentinel 功能

```yaml
feign:
  sentinel:
    enabled: true # 开启 Feign 对 sentinel 的支持
```

2. 给 FeignClient 配置失败后的降级逻辑
   1. FallbackClass，无法对远程调用的异常做处理
   2. FallbackFactory, 可以对远程调用的异常做处理(**推荐**)

####  FallbackFactory 的使用

1. 实现 FallbackFactory

   ```java
   @Slf4j
   public class UserClientFallbackFactory implements FallbackFactory<UserClient> {
       @Override
       public UserClient create(Throwable cause) {
           // 在该实现类中实现降级的逻辑
           return new UserClient() {
               @Override
               public User getUser(Integer id) {
                   // 记录异常信息
                   log.error("查询用户失败", cause);
                   return new User();
               }
           };
       }
   }
   ```

2. 将 UserClientFallbackFactory 交给 Spring 容器，可以通过 @Bean 的方式注入到容器中

3. 在 FeignClient 注解中加上 fallbackFactory 参数

   ```java
   @FeignClient(name = "userservice", fallbackFactory = UserClientFallbackFactory.class)
   @RestController
   public interface UserClient {
       @GetMapping("/userservice/user/getUser")
       User getUser(@RequestParam("id") Integer id);
   }
   ```

### 隔离

隔离的方式有两种：

- 线程池隔离
- 信号量隔离(Sentinel 的默认实现)

#### 线程池隔离与信号量隔离对比

- 线程池隔离的优点是支持主动超时，支持异步调用，缺点是线程的额外开销大，主要使用的场景是低扇出
- 信号量隔离的优点是轻量，缺点是不支持主动超时和异步调用，主要使用的场景是高频调用，高扇出的场景

**在 Sentinel 中指定线程数，其实指定的是信号量。**

## 熔断和限流的对比

- 限流是限制正常请求的请求频率，通过限流可以根据机器的处理能力设置合理的阈值，从而最大化的利用机器资源
- 熔断是对异常请求的一种保护措施，避免服务雪崩发生的一种手段。

## 授权规则

Sentinel 中的授权规则可以对调用方的来源进行控制，有白名单和黑名单两种方式

- 白名单：来自(origin)在白名单之内的调用者允许访问
- 黑名单：来自(origin)在黑名单之内的调用者不允许访问

实现原理是通过 RequestOriginParser 接口，从请求中提取对应的值，可以自行定义(如通过 Gateway 为请求的请求头中添加一个 Origin 字段)。然后与配置中的白名单和黑名单进行比对，进而决定是否放行。

## 自定义异常结果

默认情况下，发生限流、降级、授权拦截时，都会抛出异常到调用方。如果要自定义出现异常时的返回结果，需要实现 BlockExceptionHandler，并将实现类交给 Spring 容器。

```java
public interface BlockExceptionHandler {
    /**
    * 处理请求被限流，降级，授权拦截时抛出的异常：BlockException
    */
    void handle(HttpServletRequest request, 
                HttpServletResponse response, BlockException e) throws Exception;
}
```

常见的 BlockException

| 异常                 | 描述             |
| -------------------- | ---------------- |
| FlowException        | 限流异常         |
| ParamFlowException   | 热点参数限流异常 |
| DegrageException     | 降级异常         |
| AuthorityException   | 授权异常         |
| SystemBlockException | 系统规则异常     |

## Sentinel 实现服务熔断

### Sentinel 的属性介绍

- grade: 熔断策略，支持秒级 RT(Response Time)，秒级异常比例，分钟级异常数。默认值是秒级 RT。
- timeWindow: 熔断降级的时间窗口，单位是 s。也就是触发熔断降级之后多长时间内自动熔断，如 5s，这表示在这 5s 内的请求都会自动熔断，5s 钟过后，恢复正常。
- rtSlowRequestAmount: 在 RT 模式下，1s 内持续多少个请求的平均 RT 超过阈值后触发熔断，默认值是 5
- minRequestAmount: 出发异常熔断的最小请求数，请求数小于该值时，即时异常比例超出阈值也不会出发熔断，默认值是 5。

### 配置限流资源

手动配置流控规则，可以借助 Sentinel 的 InitFunc SPI(Service Provider Interface) 扩展接口来实现，只需要自己实现 InitFunc 接口，并在 init 方法中编写规则加载的逻辑即可

```java
public class FlowRuleInitFunc implements InitFunc {
    @Override
    public void init() throws Exception {
        List<FlowRule> rules = new ArrayList<>();
        FlowRule rule = new FlowRule();
        rule.setCount(1);
        rule.setResource("hello");
        rule.setGrade(RuleConstant.FLOW_GRADE_QPS);
        rule.setLimitApp("default");
        rules.add(rule);
        FlowRuleManager.loadRules(rules);
    }
}
```

SPI 是扩展点机制，如果需要被 Sentinel 加载，那么还要在 resource 目录下创建 `META-INF/services/com.alibaba.csp.sentinel.init.InitFunc` 文件，文件内容就是自定义扩展点的全路径：

```text
com.autmaple.consumer.FlowRule.FluwRuleInitFunc
```

### 自定义限流异常

在默认情况下， URL 出发限流后会直接返回：

```text
Blocked By Sentinel (Flow limiting)
```

如果想自定义返回结果，可以实现 BlockExceptionHandler 并且重写 handle 方法

```java
@Component // 注入到 Spring 容器当中
public class CustomUrlBlockHandler implements BlockExceptionHandler {

    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, BlockException e) throws Exception {
        response.setHeader("Content-Type", "application/json;charset=UTF-8");
        String message = "{\"code\": 999, \"msg\": \"访问人数过多\"";
        response.getWriter().write(message);
    }
}
```

如果想在降级后直接跳转到一个页面，可以通过如下的配置实现:

```yaml
spring:
  cloud:
    sentinel:
      block-page: "/error" # 该路径得和 Controller 中的某一个路径相匹配，匹配之后回返回对应的 Controller 方法的返回结果
```

### URL 资源清洗

Sentinel 中 HTTP 服务的限流默认由 Sentinel-Web-Servlet 包中的 CommonFilter 来实现，从代码中可以看到，这个Filter 会把每个不同的 URL 都作为不同的资源来处理。在下面这段代码中，提供了一个携带 {id} 参数的 REST 风格的 API,对于每一个不同的 {id}, URL 也都不一样，所以在默认情况下 Sentinel 会把所有的 URL 当作资源来进行流控。

```java
@RestController
public class UrlCleanController{
    @GetMapping("/clean/[id}")
    public String clean(@PathVariable("id")int id){
        return "Hello clean";
    }
}
```

这会导致两个问题：

- 限流统计不准确，实际需求是控制 clean 方法总的 QPS, 结果统计的是每个 URL 的 QPS。
- 导致 Sentinel 中资源数量过多，默认资源数量的阈值是 6000，对于多出的资源规则将不会生效。

针对这个问题可以通过 UrlCleaner 接口来实现资源清洗，也就是对于 `/clean/{id}` 这个URL, 我们可以统一归集到 `/clean/*` 资源下，具体配置代码如下，实现 UrlCleaner 接口，并重写 clean 方法即可。

```java
@Service
public class CustomerUrlCleaner implements UrlCleaner {
    @Override
    public String clean(String originUrl) {
        if(StringUtils.isEmpty(originUrl))
            return originUrl;
        if(originUrl.startsWith("/clean/")){
            return "/clean/*";
        }
        return originUrl;
    }
}
```

### Sentinel 热点限流

热点数据表示经常访问的数据，在有些场景中我们希望针对这些访问频次非常高的数据进行限流，比如针对一段时间内频繁访问的用户 IP 地址进行限流，或者针对频繁访问的某个用户 ID 进行限流。

![Sentinel系列之热点限流](../../../Attachment/9c410cd0533d48119dc514af60079d4e.png)

# Spring Cloud Gateway

Gateway 是在 Spring 生态系统之上构建的API网关服务，基于 Spring 5，Spring Boot 2 和 Project Reactor等技术。Gateway 旨在提供一种简单而有效的方式来对 API 进行路由，以及提供一些强大的过滤器功能， 例如：熔断、限流、重试等。

Sentinel 提供了热点参数限流的策略，它是一种特殊的限流，在普通限流的基础上对同一个受保护的资源区**根据请求中的参数分别处理**，该策略只对包含热点参数的资源调用生效。

热点限流在以下场景中使用较多:

- 服务网关层：例如防止网络爬虫和恶意攻击，一种常用方法就是限制爬虫的 IP 地址，客户端 IP 地址就是一种热点参数。
- 写数据的服务：例如业务系统提供写数据的服务，数据会写入数据库之类的存储系统。存储系统的底层会加锁写磁盘上的文件，部分存储系统会将某一类数据写入同一个文件。如果底层写同一文件，会出现抢占锁的情况，导致出现大量超时和失败。出现这种情况时一般有两种解决办法：修改存储设计、对热点参数限流。

Sentinel 通过 LRU 策略结合滑动窗口机制来实现热点参数的统计，其中，LRU 策略可以统计单位时间内最常访问的热点数据，滑动窗口机制可以协助统计每个参数的 QPS。

Sentinel 会根据请求的参数来判断哪些是热点参数，然后通过热点参数限流规则，将 QSP 超过设定阈值的请求阻塞。

## Spring Cloud Gateway 特性

- 基于Spring Framework 5, Project Reactor 和 Spring Boot 2.0 进行构建；
- 动态路由：能够匹配任何请求属性；
- 可以对路由指定 Predicate（断言）和 Filter（过滤器）；
- 集成 Hystrix 的断路器功能；
- 集成 Spring Cloud 服务发现功能；
- 易于编写的 Predicate（断言）和 Filter（过滤器）；
- 请求限流功能；
- 支持路径重写

## 相关概念

- Route（路由）：路由是构建网关的基本模块，它由 ID，目标 URI，一系列的断言和过滤器组成，如果断言为 true 则匹配该路由；
- Predicate（断言）：指的是 Java 8 的 Function Predicate。 输入类型是 Spring 框架中的ServerWebExchange。 这使开发人员可以匹配 HTTP 请求中的所有内容，例如请求头或请求参数。如果请求与断言相匹配，则进行路由；
- Filter（过滤器）：指的是 Spring 框架中 GatewayFilter 的实例，使用过滤器，可以在请求被路由前后对请求进行修改。

## 配置方式

有 yaml 和 java bean 两种方式设置路由:

yaml 方式

```yaml
service-url:
  user-service: http://localhost:8201
spring:
  cloud:
    gateway:
      routes:
        - id: path_route #路由的ID
          uri: ${service-url.user-service}/user/{id} #匹配后路由地址
          predicates: # 断言，路径相匹配的进行路由
            - Path=/user/{id}
```

java bean 方式

```java
/**
 * Created by macro on 2019/9/24.
 */
@Configuration
public class GatewayConfig {

    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
        return builder.routes()
                .route("path_route2", r -> r.path("/user/getByUsername")
                        .uri("http://localhost:8201/user/getByUsername"))
                .build();
    }
}
```

## Gateway 配置 lb 协议失效的问题

在 spring cloud 2020 之后的版本中，缺少负载均衡器：Ribbon, 因此如果想让 Gateway 使用 lb 协议的，需要添加负载均衡依赖:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

## Route Predicate 的使用

Spring Cloud Gateway 包括许多内置的 Route Predicate 工厂。 所有这些 Predicate 都与 HTTP 请求的不同属性匹配。 多个 Route Predicate 工厂可以进行组合

| Predicate  | 作用                                                         |
| ---------- | ------------------------------------------------------------ |
| After      | 在指定时间之后的请求会匹配该路由                             |
| Before     | 在指定时间之前的请求会匹配改路由                             |
| Between    | 在指定时间范围内的请求会匹配该路由                           |
| Cookie     | 带有指定 Cookie 的请求会匹配该路由                           |
| Header     | 带有指定请求头参数的请求会匹配该路由                         |
| Host       | 带有指定 Host 的请求会匹配该路由                             |
| Method     | 带有指定请求方法的请求会匹配该路由，如 Get，Post 等          |
| Path       | 匹配指定路径的请求会匹配该路由，路径的写法和 GetMapping 等注解的写法一致 |
| Query      | 带有指定查询参数的请求会匹配该路由                           |
| RemoteAddr | 从指定远程地址发起的请求会匹配该路由                         |
| Weight     | 使用权重来进行路由的转发                                     |

## Route Filter 的使用

路由过滤器可用于修改 HTTP 请求和返回的 HTTP 响应，**路由过滤器只能指定路由进行使用**。Spring Cloud Gateway 内置了多种路由过滤器，他们都由 GatewayFilter 的工厂类来产生

| Filter              | 作用                                                         |
| ------------------- | ------------------------------------------------------------ |
| AddRequestParameter | 给请求添加查询参数(Query Parameter)                          |
| StripPrefix         | 去除匹配路径的前 n 级目录，如 StripPrefix = 1, 匹配的路径为 /api/user/1，则转发的路径为 /user/1 |
| PrefixPath          | 与 StripPrefix 相反，该 Filter 用于给路径添加前缀,如 PrefixPath=/api,匹配的路径为 /user/1, 则转发的路径为 /api/user/1 |
| Hystrix             | Hystrix 过滤器允许你将断路器功能添加到网关路由中，使你的服务免受级联故障的影响，并提供服务降级处理。 |
| RequestRateLimiter  | RequestRateLimiter 过滤器可以用于限流，使用 RateLimiter 实现来确定是否允许当前请求继续进行，如果请求太大默认会返回 HTTP 429 - 太多请求状态。 |
| Retry               | 对路由请求进行重试的过滤器，可以根据路由请求返回的 HTTP 状态码来确定是否进行重试 |

Spring Cloud Gateway 可以配置注册中心进行使用，并且如果要使用负载均衡，配置的 uri 需要使用 lb 协议，如： uri: lb://user-service, user-service 是微服务在注册中心的名字

## 默认过滤器

DefaultFilter 是全局过滤器的一种，默认对全部的请求器过滤的作用。配置的方式是通过 application.yaml 文件的方式进行配置。

```yaml
```



## 全局过滤器

全局过滤器的作用是处理一切进入网关的请求和微服务的响应，于 GatewayFilter 的作用一样。区别在于 GatewayFilter 通过配置定义，处理逻辑固定，而 GlobalFilter 的逻辑需要自己写代码实现。

GlobalFilter 接口的定义如下：

```java
public interface GlobalFilter {
    /**
     * 处理当前请求，有必要的话通过 {@link GatewayFilterChain} 将请求交给下一个过滤器处理
     * 
     * @param exchange 请求上下文，通过该参数可以获取到 request 和 response 相关的信息
     * @param chain 全局过滤器链，用于将请求交给其他的过滤器处理
     * @return {@code Mono<Void>} 返回表示当前过滤器业务结束
     */
    Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain);
}
```

具体实现的例子如下：

```java
@Order(-1)
@Component
public class AuthorizationFilter implements GlobalFilter {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        ServerHttpRequest request = exchange.getRequest();
        MultiValueMap<String, String> queryParams = request.getQueryParams();
        String authorization = queryParams.getFirst("authorization");
        if ("admin".equals(authorization))
            return chain.filter(exchange);
        exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
        return exchange.getResponse().setComplete();

    }
}
```

> @Order 中的值越小，优先级越高，因此 int 类型的最小值优先级最高，并且 @Order 的默认值为 int 类型的最大值，

## Predicate 和 GatewayFilter 的比较

首先从执行的顺序来看：Predicate 先于 GatewayFilter 执行，先通过 Predicate 判断请求是否允许被路由，只有请求被允许路由时，请求才会交给配置文件中配置的过滤器去处理

配置了 GlobalFilter, Predicate 也是优先于 GlobalFilter 先执行

## 过滤器链的执行顺序

Spring Cloud Gateway 中存在三种过滤器：特定路由的过滤器，默认过滤器以及全局过滤器。

请求在被路由后，Gateway 会将配置给当前路由的 GatewayFilter, DefaultFilte 和 GlobalFilter 合并到一个过滤器链中，并将过滤器链中的过滤器排序后依次执行每个过滤器。在将 GlobalFilter 整合到过滤器链的过程中，使用了适配器模式，将 GlobalFilter 适配成了一个 GatewayFilter

同时，路由过滤器和默认过滤器默认是没有声明 order，这两种过滤器的顺序为在配置文件中声明的顺序，由 Spring 为他们指定，顺序从 1 开始。

如果多个过滤器的 order 值一样是，执行的顺序为：默认过滤器 > 路由过滤器 > GlobalFilter

## 跨域问题处理

在 Spring Cloud Gateway 中实现跨域非常的简单，只需要在 application.yaml 文件中进行配置即可：

```yaml
spring:
  cloud:
    gateway:
      globalcors:
        add-to-simple-url-handler-mapping: true # 解决 OPTIONS 请求被拦截的问题
        cors-configurations:
          '[/**]':
            allowOrigins: # 允许哪些网站进行跨域请求
              - "http://localhost:8090"
              - "http://www.autmaple.com"
            allowMethods: # 允许跨域请求的方法
              - "GET"
              - "POST"
              - "DELETE"
              - "PUT"
              - "OPTIONS"
            allowHeaders: "*" # 允许在请求中携带的请求头信息
            allowCredentials: true # 是否允许携带 Cookie
            max-age: 360000 # 此次跨域检查的有效期, 在有效期内，浏览器将不再发起询问
```

# 分布式事务

分布式事务问题也叫分布式数据一致性问题，简单来说就是如何在分布式场景中保证多个节点数据的一致性。分布式事务产生的核心原因在于存储资源的分布性，比如多个数据库，或者 MySQL 和 Redis 两种不同存储设备的数据一致性等。在实际应用中，我们应该尽可能地从设计层面去避免分布式事务的问题，因为任何一种解决方案都会增加系统的复杂度。

## X/Open 分布式事务模型

X/Open DTP(X/Open Distributed Transaction Processing Reference Model)是 X/Open 这个组织定义的一套分布式事务的标准。这个标准提出了使用两阶段提交(2PC,Two-Phase-Commit)来保证分布式事务的完整性。X/Open DTP 中包含以下三种角色:

- AP: Application,表示应用程序。
- RM: Resource Manager, 表示资源管理器，比如数据库。
- TM:Transaction Manager,表示事务管理器，一般指事务协调者，负责协调和管理事务，提供 AP 编程接口或管理 RM。可以理解为 Spring 中提供的 Transaction Manager。

![分布式事务处理之DTP模型与XA规范&2PC协议与3PC协议的区别](../../../Attachment/0f350a5b2e5a47f5a81e287a9a192f29.png)

上图中所展示的角色和关系与本地事务的原理基本相同，唯一不同的在于，如果此时 TM 代表数据库，那么 TM 需要能够管理多个数据库的事务，大致实现步骤如下：

- 配置 TM,把多个 RM 注册到 TM ,相当于 TM 注册 RM 作为数据源。
- AP 从 TM 管理的 RM 中获取连接，如果 RM 是数据库则获取 JDBC 连接。
- AP 向 TM 发起一个全局事务，生成全局事务ID(XID),XID 会通知各个 RM。
- AP 通过第二步获得的连接直接操作 RM 完成数据操作。这时，AP在每次操作时会把 XID 传递给 RM。
- AP 结束全局事务，TM 会通知各个 RM 全局事务结束。
- 根据各个 RM 的事务执行结果，执行提交或者回滚操作

在原本的单机事务下，会存在跨库事务的可见性问题，导致无法实现多节点事务的全局可控。 而 TM 就是一个全局事务管理器，它可以管理多个资源管理器的事务。TM 最终会根据各个分支事务的执行结果进行提交或者回滚，如果注册的所有分支事务中任何一个节点事务执行失败，为了保证数据的一致性，TM 会触发各个 RM 的事务回滚操作。

需要注意的是，TM 和多个 RM 之间的事务控制，是基于 XA 协议(XA Specification)来完成的。XA 协议是 X/Open 提出的分布式事务处理规范，也是分布式事务处理的工业标准，它定义了 xa_ 和 ax_ 系列的函数原型及功能描述、约束等。目前 Oracle、MySQL、DB2 都实现了 XA 接口，所以它们都可以作为 RM。

![在这里插入图片描述](../../../Attachment/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dyczEyMA==,size_16,color_FFFFFF,t_70.png)

## 两阶段提交协议

两阶段提交协议的执行流程如下:

- 准备阶段：事务管理器(TM)通知资源管理器(RM)准备分支事务，记录事务日志，并告知事务管理器的准备结果。

- 提交/回滚阶段：如果所有的资源管理器(RM)在准备阶段都明确返回成功，则事务管理器(TM)向所有的资源管理器(RM)发起事务提交指令完成数据的变更。反之，如果任何一个资源管理器(RM)明确返回失败，则事务管理器(TM)会向所有资源管理器(RM)发送事务回滚指令。完整的执行流程

![两阶段与三阶段提交协议- 程序员大本营](../../../Attachment/49b779d2615bb78b71fce4af2f05f2bf.png)

两阶段提交将一个事务的处理过程分为投票和执行两个阶段，它的优点在于充分考虑到了分布式系统的不可靠因素，并且采用非常简单的方式（两阶段提交）就把由于系统不可靠而导致事务提交失败的概率降到最小。当然，它也并不是完美的，存在以下缺点:

- 同步阻塞：所有参与者(RM)都是事务阻塞型的，对于任何一次指令都必须要有明确的响应才能继续进行下一步，否则会处于阻塞状态，占用的资源一直被锁定。
- 过于保守：任何一个节点失败都会导致数据回滚。
- 事务协调者的单点故障：如果协调者在第二阶段出现了故障，那么其他的参与者(RM)会一直处于锁定状态。
- “脑裂”导致数据不一致问题：在第二阶段中，事务协调者向所有参与者(RM)发送 commit 请求后，发生局部网络异常导致只有一部分参与者(RM)接收到了 commit 请求，这部分参与者(RM) 收到请求后会执行 commit 操作，但是未收到 commit 请求的节点由于事务无法提交，导致数据出现不一致问题。

## 三阶段提交协议

三阶段提交协议是两阶段提交协议的改进版本，它利用超时机制解决了同步阻塞的问题，三阶段提交协议的具体描述如下:

- CanCommit(询问阶段)：事务协调者向参与者发送事务执行请求，询问是否可以完成指令，参与者只需要回答是或者不是即可，不需要做真正的事务操作，这个阶段会有超时中止机制。
- PreCommit(准备阶段)：事务协调者会根据参与者的反馈结果决定是否继续执行，如果在询问阶段所有参与者都返回可以执行操作，则事务协调者会向所有参与者发送 PreCommit 请求，参与者收到请求后写 redo 和 undo 日志，执行事务操作但是不提交事务，然后返回 ACK 响应等待事务协调者的下一步通知。如果在询问阶段任意参与者返回不能执行操作的结果，那么事务协调者会向所有参与者发送事务中断请求。
- DoCommit(提交或回滚阶段)：这个阶段也会存在两种结果，仍然根据上一步骤的执行结果来决定 DoCommit 的执行方式。如果每个参与者在 PreCommit 阶段都返回成功，那么事务协调者会向所有参与者发起事务提交指令。反之，如果参与者中的任一参与者返回失败，那么事务协调者就会发起中止指令来回滚事务。

![08 对比两阶段提交，三阶段协议有哪些改进？.md](../../../Attachment/CgqCHl66P8OAOon7AALWZvqApaI286.png)

三阶段提交协议和两阶段提交协议相比有一些不同点：

- 增加了一个 CanCommit 阶段，用于询问所有参与者是否可以执行事务操作并且响应，它的好处是，可以尽早发现无法执行操作而中止后续的行为。
- 在准备阶段之后，事务协调者和参与者都引入了超时机制，一旦超时，事务协调者和参与者会继续提交事务，并且认为处于成功状态，因为在这种情况下事务默认为成功的可能性
  比较大。

实际上，一旦超时，在三阶段提交协议下仍然可能出现数据不一致的情况，当然概率是比较小的。另外，**最大的好处就是基于超时机制来避免资源的永久锁定**。需要注意的是，不管是两阶段提交协议还是三阶段提交协议，都是数据一致性解决方案的实现，我们可以在实际应用中灵活调整。比如 ZooKeeper 集群中的数据一致性，就用到了优化版的两阶段提交协议，优化的地方在于，它不需要所有参与者在第一阶段返回成功才能提交事务，而是利用少数服从多数的投票机制来完成数据的提交或者回滚。

## 分布式事务问题的常见解决方案

电商领域等互联网场景下，基于 CP 的强一致性方案在数据库性能和系统处理能力上会存在一定的瓶颈。所以在互联网场景中更多采用柔性事务，所谓的柔性事务是遵循 BASE 理论来实现的事务模型，它有两个特性：基本可用、柔性状态。

### TCC 补偿性方案

TCC(Try-Confirm-Cancel)是一种比较成熟的分布式数据一致性解决方案，它实际上是把个完整的业务拆分为如下三个步骤:

- Try: 这个阶段主要是对数据的校验或者资源的预留。
- Confirm: 确认真正执行的任务，只操作 Try 阶段预留的资源。
- Cancel: 取消执行，释放 Try 阶段预留的资源。

其实 TCC 是一种两阶段提交的思想，第一阶段通过 Try 进行准备工作，第二阶段 Confirm/Cancel 表示 Try 阶段操作的确认和回滚。在分布式事务场景中，每个服务实现 TCC 之后，就作为其中的一个资源，参与到整个分布式事务中。然后主业务服务在第一阶段中分别调用所有 TCC 服务的 Try 方法。最后根据第一个阶段的执行情况来决定对第二阶段的 Confirm 或者 Cancel。TCC 执行流程如下图所示：

![TCC-transaction学习| Ling's blog](../../../Attachment/20180604001625_695.jpg)

对于 TCC 的工作机制，我们举一个比较简单的例子。在一个理财 App 中，用户通过账户余额购买一个理财产品，这里涉及两个事务操作：

- 在账户服务中，对用户账户余额进行扣减。
- 在理财产品服务中，对指定理财产品可申购金额进行扣减。

这两个事务操作在微服务架构下分别对应的是两个不同的微服务，以及独立的数据库操作，在 TCC 的工作机制中，首先针对账户服务和理财产品服务分别提供 Try、Confirm 和 Cancel 三个方法。

- 在账户服务的 Try 方法中对实际申购金额进行冻结，Confirm 方法把 Try 方法冻结的资金进行实际的扣减，Cancel 方法把 Try 方法冻结的资金进行解冻。
- 理财产品服务的 Try 方法中将本次申购的部分额度进行冻结，Confirn 方法把 Try 方法中冻结的额度进行实际扣减，Cancel 方法把 Try 方法中冻结的额度进行释放。

在一个主业务方法中，分别调用这两个服务对外提供的处理方法（资金扣减、理财产品可申购额度扣减)，这两个服务做实际业务处理时，会先调用 Try 方法来做资源预留，如果这两个方法处理都正常，TCC 事务协调器就会调用 Confirm 方法对预留资源进行实际应用。否则 TCC 事物协调器一旦感知到任何一个服务的 Try 方法处理失败，就会调用各个服务的 Cancel 方法进行回滚，从而保证数据的一致性。

在一些特殊情况下，比如理财产品服务宕机或者出现异常，导致该服务并没有收到 TCC 事务协调器的 Cancel 或者 Confirm 请求，怎么办呢？没关系，TCC 事务框架会记录一些分布式事务的操作日志，保存分布式事务运行的各个阶段和状态。TCC 事务协调器会根据操作日志来进行重试，以达到数据的最终一致性。

需要注意的是，TCC 服务支持接口调用失败发起重试，所以 TCC 暴露的接口都需要满足幂等性。

### 基于可靠性消息的最终一致性方案

基于可靠性消息的最终一致性是互联网公司比较常用的分布式数据一致性解决方案，它主要利用消息中间件(Kafka、RocketMQ 或 RabbitMQ)的可靠性机制来实现数据一致性的投递。以电商平台的支付场景为例，用户完成订单的支付后不需要同步等待支付结果，可以继续做其他事情。但是对于系统来说，大部分是在发起支付之后，等到第三方支付平台提供异步支付结果通知，再根据结果来设置该订单的支付状态。并且如果是支付成功的状态，大部分电商平台基于营销策略还会给账户增加一定的积分奖励。所以，当系统接收到第三方返回的支付结果时，需要更新支付服务的支付状态，以及更新账户服务的积分余额，这里就涉及两个服务的数据一致性问题。从这个场景中可以发现这里的**数据一致性并不要求实时性，所以我们可以采用基于可靠性消息的最终一致性方案来保证支付服务和账户服务的数据一致性**。如图所示，支付服务收到支付结果通知后，先更新支付订单的状态，再发送一条消息到分布式消息队列中，账户服务会监听到指定队列的消息并进行相应的处理，完成数据的同步。

![image-20220802202351287](../../../Attachment/image-20220802202351287.png)

在上图的解决方案中，我们不难发现一些问题，就是支付服务的本地事务与发送消息这个操作的原子性问题，具体描述如下：

先发送消息，再执行数据库事务，在这种情况下可能会出现消息发送成功但是本地事务更新失败的情况，仍然会导致数据不一致的问题。

```text
begin transaction;
sendMsg();
updateStatus();
commit transaction;
```

先执行数据库事务操作，再发送消息，在这种情况下可能会出现 MQ 响应超时导致异常，从而将本地事务回滚，但消息可能已经发送成功了，也会存在数据不一致的问题。

```text
begin transaction;
updateStatus();
sendMsg();
commit transaction;
```

以上问题也有很多成熟的解决方案，以 RocketMQ 为例，它提供了事务消息模型, 具体的执行逻辑如下：

- 生产者发送一个事务消息到消息队列上，消息队列只记录这条消息的数据，此时消费者无法消费这条消息。
- 生产者执行具体的业务逻辑，完成本地事务的操作。
- 接着生产者根据本地事务的执行结果发送一条确认消息给消息队列服务器，如果本地事务执行成功，则发送一个 Commit 消息，表示在第一步中发送的消息可以被消费，否则，消息队列服务器会把第一步存储的消息删除。
- 如果生产者在执行本地事务的过程中因为某些情况一直未给消息队列服务器发送确认，那么消息队列服务器会定时主动回查生产者获取本地事务的执行结果，然后根据回查结果来决定这条消息是否需要投递给消费者。
- 消息队列服务器上存储的消息被生产者确认之后，消费者就可以消费这条消息，消息消费完成之后发送一个确认标识给消息队列服务器，表示该消息投递成功。

在 RocketMQ 事务消息模型中，事务是由生产者来完成的，消费者不需要考虑，因为消息队列可靠性投递机制的存在，如果消费者没有签收该消息，那么消息队列服务器会重复投递，从而实现生产者的本地数据和消费者的本地数据在消息队列的机制下达到最终一致。

不难发现，在 RocketMQ 的事务消息模型中最核心的机制应该是**事务回查**，实际上查询模式在很多类似的场景中都可以应用。在分布式系统中，由于网络通信的存在，服务之间的远程通信除成功和失败两种结果外，还存在一种未知状态，比如网络超时。**服务提供者可以提供一个查询接口向外部输出操作的执行状态，服务调用方可以通过调用该接口得知之前操作的结果并进行相应的处理。**

### 最大努力通知型

最大努力通知型和基于可靠性消息的最终一致性方案的实现是类似的，它是一种比较简单的柔性事务解决方案，也比较适用于对数据一致性要求不高的场景，最典型的使用场景是支付宝支付结果通知:

- 商户先创建一个支付订单，然后调用支付宝发起支付请求。

- 支付宝唤醒支付页面完成支付操作，支付宝同样会针对该商户创建一个支付交易，并且根据用户的支付结果记录支付状态。

- 支付完成后触发一个回调通知给商户，商户收到该通知后，根据结果修改本地支付订单的状态，并且返回一个处理状态给支付宝。
- 针对这个订单，在理想状态下支付宝的交易状态和商户的交易状态会在通知完成后达到最终一致。但是由于网络的不确定性，支付结果通知可能会失败或者丢失，导致商户端的支付订单的状态是未知的。所以最大努力通知型的作用就体现了，如果商户端在收到支付结果通知后没有返回一个“SUCCESS”状态码，那么这个支付结果回调请求会以衰减重试机制（逐步拉大通知的间隔）继续触发，比如 1 min、5 min、10 min、30 min…直到达到最大通知次数。如果达到指定次数后商户还没有返回确认状态，怎么处理呢？
- 支付宝提供了一个交易结果查询接口，可以根据这个支付订单号去支付宝查询支付状态，然后根据返回的结果来更新商户的支付订单状态，这个过程可以通过定时器来触发，也可以通过人工对账来触发。

从上述分析可以发现，所谓的最大努力通知，就是在商户端如果没有返回一个消息确认时，支付宝会不断地进行重试，直到收到一个消息确认或者达到最大重试次数。

不难发现它的实现机制与事务消息模型的消费者消费模型类似，在消费者没有向消息中间件服务器发送确认之前，这个消息会被重复投递，确保消息的可靠性消费。

## 分布式事务框架 Seata

Seata 是一款开源的分布式事务解决方案，致力于在微服务架构下提供高性能和简单易用的分布式事务服务。它提供了 AT、TCC 、Saga 和 XA 事务模式，为开发者提供了一站式的分布式事务解决方案。

### AT 模式

AT 模式是 Seata 最主推的分布式事务解决方案，它是基于 XA 演进而来的一种分布式事务模式，所以它同样分为三大模块，分别是 TM、RM 和 TC,其中 TM 和 RM 作为 Seata 的客户端与业务系统集成，TC 作为 Seata 的服务器独立部署。TM 表示事务管理器(Transaction Manager)它负责向 TC 注册一个全局事务，并生成一个全局唯一的 ID。在 AT 模式下，每个数据库资源被当作一个 RM(Resource Manager),在业务层面通过 JDBC 标准的接口访问 RM 时，Seata 会对所有请求进行拦截。每个本地事务进行提交时，RM 都会向 TC(Transaction Coordinator,事务协调器)注册一个分支事务。Seata 的 AT 事务模式如图所示：

![分布式事务与Seate框架（3）——Seata的AT模式实现原理- ITNewBee](../../../Attachment/1352849-20210623233710656-104557209.png)

具体执行流程如下：

- TM 向 TC 注册全局事务，并生成全局唯一的 XD。
- RM 向 TC 注册分支事务，并将其纳入该 XID 对应的全局事务范围。
- RM 向 TC 汇报资源的准备状态。
- TC 汇总所有事务参与者的执行状态，决定分布式事务是全部回滚还是提交。
- TC 通知所有 RM 提交/回滚事务。

AT 模式和 XA 一样，也是一个两阶提交事务模型，不过和 XA 相比，做了很多优化

### Saga 模式

Saga 模式又称为长事务解决方案，它是由普林斯顿大学的 Hector Garcia-Molina 和 Kenneth Salem 提出的，主要描述的是在没有两阶段提交的情况下如何解决分布式事务问题。其核心思想是：把一个业务流程中的长事务拆分为多个本地短事务，业务流程中的每个参与者都提交真实的提交给该本地短事务，当其中一个参与者事务执行失败，则通过补偿机制补偿前面已经成功的参与者。

![基于Saga的分布式事务调度落地](../../../Attachment/20220514143646165251020629373.png)

另外，Saga 提供了以下两种补偿恢复方式。

- 向后恢复，也就是上图中提到工作模式，如果任一子事务执行失败，则把之前执行的结果逐一撤销。
- 向前恢复，也就是不进行补偿，而是对失败的事务进行重试，这种方式比较适合于事务必须要执行成功的场景。

#### Saga 的优劣势

和 XA 或者 TCC 相比，它的优势包括：

- 一阶段直接提交本地事务；没有锁等待，性能较高
- 在事件驱动的模式下，短事务可以异步执行：补偿机制的实现比较简单。

缺点是 Saga 并不提供原子性和隔离性支持，隔离性的影响是比较大的，比如用户购买一个商品后系统赠送一张优惠券，如果用户已经把优惠券使用了，那么事务如果出现异常要回滚时就会出现问题。

#### Saga 的协调模式

Saga 有两种常用的协调模式

- 事件/编排式：把 Saga 的决策和执行顺序逻辑分布在 Saga 的每一个参与者中，它们通过交换事件的方式来进行沟通。
- 命令/协同式：把 Saga 的决策和执行顺序逻辑集中在一个 Saga 控制类中，它以命令/回复的方式与每项服务进行通信，告诉它们应该执行哪些操作。

##### 事件/编排式

在基于事件的编排模式中，第一个服务执行完一个本地事务之后，发送一个事件。这个事件会被一个或者多个服务监听，监听到事件的服务再执行本地事务并发布新的事件，此后一直延续这种事件触发模式，直到该业务流程中最后一个服务的本地事务执行结束，才意味着整个分布式长事务也执行结束.

![saga - 掘金](../../../Attachment/aa3286c8a35b4ef2b018680a3c921486tplv-k3u1fbpfcp-zoom-in-crop-mark1956000.png)

- 订单服务创建新的订单，把订单状态设置为待支付，并发布一个 ORDER CREATE EVENT 事件。
- 库存服务监听到 ORDER CREATE EVENT 事件后，执行本地的库存冻结方法，如果执行成功，则发布一个 ORDER PREPARED EVENT 事件。
- 支付服务监听 ORDER PREPARED EVENT 事件后，执行账户扣款方法，并发布 PAY ORDER EVENT 事件。
- 最后，积分服务监听 PAY ORDER EVENT 事件，增加账户积分，并更新订单状态为成功。

上述任一步骤执行失败，都会发送一个失败的事件，每个服务需要监听失败的情况根据实际需求进行逐一回滚。

##### 命令/协同式

命令/协同式需要定义一个 Saga 协调器，负责告诉每一个参与者该做什么，Saga 协调器以命令/回复的方式与每项服务进行通信

![image-20220802211804940](../../../Attachment/image-20220802211804940.png)

命令/协同式的实现步骤如下：

- 订单服务首先创建一个订单，然后创建一个订单 Saga 协调器，启动订单事务。
- Saga 协调器向库存服务发送冻结库存命令，库存服务通过 Order Saga Reply Queue 回复执行结果。
- 接着，Saga 协调器继续向支付服务发起账户扣款命令，支付服务通过 Order Saga ReplyQueue 回复执行结果。
- 最后，Saga 协调器向积分服务发起增加积分命令，积分服务回复执行结果。

需要注意的是，订单 Saga 协调器必须提前知道 “创建订单事务” 的所有流程( Seata 是通过基于 JSON 的状态机引擎来实现的)，并且在整个流程中任何一个环节执行失败，它都需要向每个参与者发送命令撤销之前的事务操作。

# 网关

## 网关的作用

- 请求的转发以及服务的整合
- 对所有请求进行统一的认证、鉴权、限流、熔断、日志
- 协议转化。针对后端多种不同的协议，在网关层统一处理后以 HTTP 对外提供服务。用过 Dubbo 框架的应该知道，针对 Dubbo 服务还需要提供一个 Web 应用来进行协议转化.
- 统一错误码处理。
- 请求转发，并且可以基于网关实现内、外网隔离。

**网关的本质是对请求进行路由转发，以及对请求进行前置和后置的过滤**。

- 请求的转发和路由：接收客户端的所有请求，并将请求转发到后端的微服务中。因为微服务的粒度比较细，所以 API 网关又类似于门面模式，对多个微服务进行功能整合，提供唯一的业务接口给客户端。
- 过滤：网关会拦截所有的请求来完成一系列的横切工作，比如鉴权、限流。

## 统一认证和鉴权

在单体应用中，客户端身份认证及访问权限的控制比较简单，只需要在服务端通过 session 保存该用户信息即可。但是在微服务架构下，单体应用被拆分成多个微服务，鉴权的过程就会变得很复杂。

- 首先要解决的问题是，原来单体应用中的 session 方式己经无法用于微服务场景。
- 其次就是如何实现对每个微服务进行鉴权。

当然，对于第一个问题，目前已经有非常多的成熟解决方案了，比如 AccessToken、Oauth(开放API)等。对于第二个问题，我们可以把鉴权的功能抽离出一个统一认证服务，所有的微服务在被访问之前，先访问该认证服务进行鉴权。这种解决方案看似合理，但是在实际应用中，一个业务场景中可能会调用多个微服务，就会造成一次请求需要进行多次鉴权操作，增加了网络通信开销。

![image-20220803205517835](../../../Attachment/image-20220803205517835.png)

增加 API 网关之后，在网关层进行请求拦截，获取请求中附带的用户身份信息，调用统一认证中心对请求进行身份认证，在确认了身份之后再检查是否有资源的访问权限。

### OAuth2 授权

![3个注解，优雅的实现微服务鉴权](../../../Attachment/20220601092534165404673473229.png)

## 灰度发布

所谓灰度发布，就是指将要发布的功能先开放给一小部分用户使用，把影响范围控制在一个非常小的范围，比如 A/B Test 就是一种灰度发布方式，即一部分用户继续使用 A 功能，另外一小部分用户使用新的 B 功能。通过对使用 B 功能的用户进行满意度调查，以及对新发布的代码的性能和稳定性指标进行评测，逐步放大该新版本的投放，直到全量或者回滚该版本。

网关是所有客户端请求的入口，因此在网关层可以通过灰度规则进行部分流量的路由，从而实现灰度发布。如图10-4所示，网关对请求进行拦截之后，会根据分流引擎配置的分流规则进行请求的路由。

![image-20220803210728228](../../../Attachment/image-20220803210728228.png)

## Spring Cloud Gateway

Spring Cloud Gateway 启动时基于 Netty Server 监听一个指定的端口（该端口可以通过 server.port 属性自定义)。**当客户端发送一个请求到网关时，网关会根据一系列 Predicate 的匹配结果来决定访问哪个 Route 路由，然后根据过滤器链进行请求的处理**。过滤器链可以在请求发送到后端服务器之前和之后执行，也就是首先执行 Pre 过滤器链，然后将请求转发到后端服务器，最后执行 Post 过滤器链。

Predicate 是 Java8 提供的一个函数式接口，它允许接收一个参数并返回一个布尔值，可以用于条件过滤、请求参数的校验。

```java
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);
}
```

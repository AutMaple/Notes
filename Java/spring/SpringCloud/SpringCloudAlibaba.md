# SpringCloud 常用的注解

| 注解                   | 描述                                                         |
| ---------------------- | ------------------------------------------------------------ |
| @EnableDiscoveryClient | 标注在 Spring 启动类上,表明将该应用注册到注册中心上          |
| @EnableFeignClients    | 开启 OpenFeign 来调用远程服务，该注解会扫描该注解所修饰的类所在的包中被 @FeignClient 注解修饰的接口，并生成对应接口的实现类注入到 Spring 容器中 |
| @FeignClien            | 该注解的 value 参数用于指定被调用的服务在注册中心的名字，表明该接口的实现类要调用对应的服务 |
| @SentinelResource      | 该注解用来标识资源是否被限流、降级                           |

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

# 配置中心 Nacos

[官方文档](https://spring-cloud-alibaba-group.github.io/github-pages/hoxton/zh-cn/index.html#_spring_cloud_alibaba_nacos_config)

使用 Spring Cloud Alibaba Nacos Config，可基于 Spring Cloud 的编程模型快速接入 Nacos 配置管理功能

Nacos Config 使用 DataId 和 GROUP 确定一个配置

配置中心的配置需要写在 bootstrap.yaml 文件中

Nacos Config 通过 namesapce > group > data id  三种级别来实现不同场景下的配置文件的切换

```properties
spring.cloud.nacos.config.namespace=b3404bc0-d7dc-4855-b519-570ed34b62d7
spring.cloud.nacos.config.group=DEVELOP_GROUP
spring.cloud.nacos.shared.dataids=global.yaml,app-common.yaml,app-local-common.yaml # 多个应用共享配置的 data id 列表
spring.cloud.nacos.config.shared-dataids=bootstrap-common.properties,all-common.properties # 来支持多个共享 Data Id 的配置，多个之间用逗号隔开
spring.cloud.nacos.config.refreshable-dataids=bootstrap-common.properties # 来支持哪些共享配置的 Data Id 在配置变化时，应用中是否可动态刷新， 感知到最新的配置值，多个 Data Id 之间用逗号隔开。如果没有明确配置，默认情况下所有共享配置的 Data Id 都不支持动态刷新。
```

注意：data id 需要带上对应配置文件类型的扩展名，如：nacos-consumer.yaml, nacos-consumer.properties。否则的话匹配不上

多个共享配置间的一个优先级的关系我们约定：按照配置出现的先后顺序，即后面的优先级要高于前面的。

## 配置的优先级

Nacos Config 目前提供了三种配置能力从 Nacos 拉取相关的配置

- A: 通过 `spring.cloud.nacos.config.shared-dataids` 支持多个共享 Data Id 的配置
- B: 通过 `spring.cloud.nacos.config.ext-config[n].data-id` 的方式支持多个扩展 Data Id 的配置
- C: 通过内部相关规则(应用名、应用名 + Profile )自动生成相关的 Data Id 配置

当三种方式共同使用时，他们的一个优先级关系是:A < B < C

# Sentinel

[Sentinel](https://github.com/alibaba/Sentinel) 具有以下特征:

- **丰富的应用场景**： Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、实时熔断下游不可用应用等。
- **完备的实时监控**： Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况。
- **广泛的开源生态**： Sentinel 提供开箱即用的与其它开源框架/库的整合模块，例如与 Spring Cloud、Dubbo、gRPC 的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。
- **完善的 SPI 扩展点**： Sentinel 提供简单易用、完善的 SPI 扩展点。您可以通过实现扩展点，快速的定制逻辑。例如定制规则管理、适配数据源等。

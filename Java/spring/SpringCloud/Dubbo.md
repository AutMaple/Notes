# 容错模式

- Failover Cluster：失败自动切换，当出现失败，自动切换其他服务器。通常用于读操作，但重试会带来更长延迟。可通过 retries="2" 来设置重试次数(不含第一次)。

- Failfast Cluster：快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的写操作，比如新增记录。

- Failsafe Cluster：失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。

- Failback Cluster：失败自动回复，后台记录失败请求，定时重发。通常用于消息通知操作。

- Forking Cluster：并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过`forks="2"`来设置最大并行数。

- Broadcast Cluster：广播调用所有提供者，逐个调用，任意一台报错则报错。通常用于通知所有提供者更新缓存或日志等本地资源信息。

## 配置方式

只需要在指定 @DubboSerice 注解上添加一个 cluster 参数即可

```java
@DubboService(cluster = "failfast")
public class HelloServiceImpl implements IHelloService {
    @Value("${dubbo.application.name}")
    private String serviceName;

    @Override
    public String sayHello(String username) {
        return String.format("[%s]: Hello, %s", serviceName, username);
    }
}
```

在实际的应用中查询语句容错策略建议使用默认的 Failover Cluster；而增删改操作建议使用 Failfast Cluster 或者 Failover Cluster(retries=“0”) 策略，防止出现数据重复添加等其他问题！**建议在设计接口的时候把查询接口方法单独做成一个接口提供查询**

# 负载均衡

负载均衡可以分为硬件负载均衡和软件负载均衡，硬件负载均衡比较常见的就是 F5，软件负载均衡目前比较主流的就是 Nginx.

在 Dubbo 中提供了 4 种负载均衡策略，默认的负载均衡策略是 random。

- Random LoadBalance，随机算法。可以针对性能较好的服务器设置较大的权重值，权重值越大，随机的概率也会越大。
- RoundRobin LoadBalance, 轮询。按照公约后的权重设置轮询比例
- LeastActive LoadBalance,最少活跃调用书。处理较慢的节点收到的请求相对于其他的节点会少一些
- ConsistentHash LoadBalance, 一致性 Hash。相同参数的请求总是发送到同一个服务服务提供者

## 配置方式

在 @DobboService 注解上加上 loadBalance 参数

```java
@DubboService(cluster = "failfast", loadbalance = "roundrobin")
public class HelloServiceImpl implements IHelloService {
    @Value("${dubbo.application.name}")
    private String serviceName;

    @Override
    public String sayHello(String username) {
        return String.format("[%s]: Hello, %s", serviceName, username);
    }
}
```


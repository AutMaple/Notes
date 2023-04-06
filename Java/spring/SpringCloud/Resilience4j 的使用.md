# 常用注解
| 注解            | 描述         |
| --------------- | ------------ |
| @CircuitBreaker | 断路器模式   |
| @Bulkhead       | 舱壁模式     |
| @Retry          | 重试模式     |
| @RateLimiter    | 速率限制模式 |

这些拦截器作用的顺序：

@RateLimiter -> @Retry -> @CircuitBreaker -> @Bulkhead

![](../../../Attachment/Pasted%20image%2020230406152606.png)

# CircuitBreaker 模式

application.yaml

```yaml
resilience4j:  
  circuitbreaker:  
    instances:  
      licenseService:  
        register-health-indicator: true  
        sliding-window-size: 5  
        permitted-number-of-calls-in-half-open-state: 1  
        wait-duration-in-open-state: 10s  
        failure-rate-threshold: 50  
        record-exceptions:  
          - org.springframework.web.client.HttpServerErrorException  
          - java.io.IOException  
          - java.util.concurrent.TimeoutException  
          - org.springframework.web.client.ResourceAccessException
```

# Bulkhead 的模式

application.yaml

```yaml
resilience4j:
	bulkhead: # 信号量类型配置的参数  
	  instances:  
	    licenseService:  
	      max-concurrent-calls: 1  
		  max-wait-duration: 100ms
	thread-pool-bulkhead: # 线程池方式配置的参数  
	  instances:  
	    bulkheadLicenseService:  
	      max-thread-pool-size: 1  
	      core-thread-pool-size: 1  
	      queue-capacity: 1  
	      keep-alive-duration: 60000ms
```

# RateLimiter 模式

```yaml
resilience4j:
  ratelimiter:
    instances:
      licenseService:
        register-health-indicator: true
        timeout-duration: 100ms
        limit-refresh-period: 1s
        limit-for-period: 1
```

`limit-refresh-period` 和 `limit-for-period` 参数设置请求的速率. 上诉配置的意思是： 每 1s 处理一个请求.

# Retry 模式

application.yaml

```yaml
resilience4j:
  retry:
    instances:
      licenseService:
        max-attempts: 3
        wait-duration: 1000ms
        retry-exceptions: # 如果程序遇到如下的异常会进行重试
          - java.lang.Exception
```
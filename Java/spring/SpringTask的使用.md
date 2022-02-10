# SpringTask 的使用

## SpringTask 是什么

SpringTask 是 Spring 3.0 之后自带的一个功能，用于执行定时任务，可以将它看成是一个轻量级的 Quartz, 功能虽然没有 Quartz 那般强大，但是使用起来非常简单，容易上手, 无需增加额外的依赖，可以直接使用

## 如何使用

只要在对应的配置类上添加上 `@EnableScheduling` 注解，就可以开启定时任务。 在相应的方法上加上 `@Scheduled` 注解，则表示该方法是一个定时任务，@Scheduled 注解的值是 cron 表达式。

## cron 表达式

在 spring 4.x 中已经不支持 7 个参数的 cron 表达式了，要求必须是 6 个参数。cron 表达式格式如下：

```text
{Seconds} {Minutes} {Hours} {DayOfMonth} {Month} {DayOfWeek}
```

- Seconds：必填项，允许的值范围是0-59，支持的特殊符号包括 `,``-``*``/` ，`,`表示特定的某一秒才会触发任务，`-`表示一段时间内会触发任务，`*`表示每一秒都会触发，`/`表示从哪一个时刻开始，每隔多长时间触发一次任务。
- Minutes：必填项，允许的值范围是0-59，支持的特殊符号和秒一样，含义类推
- Hours：必填项，允许的值范围是0-23，支持的特殊符号和秒一样，含义类推
- DayOfMonth：必填项，允许的值范围是1-31，支持的特殊符号相比秒多了`?`，表示与{星期}互斥，即意味着若明确指定{星期}触发，则表示{日期}无意义，以免引起冲突和混乱。
- Month：必填项，允许的值范围是1-12（JAN-DEC），支持的特殊符号与秒一样，含义类推
- DayOfWeek：必填项，允许值范围是1~7 (SUN-SAT),1代表星期天（一星期的第一天），以此类推，7代表星期六，支持的符号相比秒多了`?`，表达的含义是与{日期}互斥，即意味着若明确指定{日期}触发，则表示{星期}无意义。

#### Cron格式中每个时间元素的说明

| 时间元素   | 可出现的字符  | 有效数值范围                 |
| ---------- | ------------- | ---------------------------- |
| Seconds    | , - * /       | 0-59                         |
| Minutes    | , - * /       | 0-59                         |
| Hours      | , - * /       | 0-23                         |
| DayofMonth | , - * / ? L W | 0-31                         |
| Month      | , - * /       | 1-12                         |
| DayofWeek  | , - * / ? L # | 1-7或SUN-SAT, 从星期天开始算 |

#### Cron格式中特殊字符说明

| 字符 | 作用                                          | 举例                                                         |
| ---- | --------------------------------------------- | ------------------------------------------------------------ |
| ,    | 列出枚举值                                    | 在Minutes域使用5,10，表示在5分和10分各触发一次               |
| -    | 表示触发范围                                  | 在Minutes域使用5-10，表示从5分到10分钟每分钟触发一次         |
| *    | 匹配任意值                                    | 在Minutes域使用*, 表示每分钟都会触发一次                     |
| /    | 起始时间开始触发，每隔固定时间触发一次        | 在Minutes域使用 5/10,表示5分时触发一次，每10分钟再触发一次   |
| ?    | 在 DayofMonth 和 DayofWeek 中，用于匹配任意值 | 在 DayofMonth 域使用?, 表示每天都触发一次                    |
| #    | 在 DayofMonth 中，确定第几个星期几            | 1#3 表示第三个星期日                                         |
| L    | 表示最后                                      | 在DayofWeek中使用5L,表示在最后一个星期四触发                 |
| W    | 表示有效工作日(周一到周五)                    | 在DayofMonth使用5W，如果5日是星期六，则将在最近的工作日4日触发一次 |

## 示例代码

```java
package com.autmaple.mall.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableScheduling;

@Configuration
@EnableScheduling // 开启 SpringTask 的功
public class SpringTaskConfig {

}

```

```java
package com.autmaple.mall.component;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class OrderTimeOutCancelTask {
    private static final Logger LOGGER = LoggerFactory.getLogger(OrderTimeOutCancelTask.class);

    /**
     * cron表达式：Seconds Minutes Hours DayOfMonth Month DayOfWeek [Year]
     * 每10分钟扫描一次，扫描设定超时时间之前下的订单，如果没支付则取消该订单
     */
    @Scheduled(cron = "0 0/10 * ? * ?")
    private void cancelTimeOutOrder(){
        LOGGER.info("取消订单，并根据 sku 编号释放锁定库存");
    }
}
```


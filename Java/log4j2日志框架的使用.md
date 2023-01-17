# 日志级别

All < Trace < Debug < Info < Warn < Error < Fatal < OFF

log4j2 默认会在 classpath 目录下寻找 log4j.json、log4j.jsn、log4j2.xml 等名称的文件，如果都没有找到，则会按默认配置输出，也就是输出到控制台。

# 配置文件名和配置文件所在的位置

log4j 2.x 版本不再支持像 1.x 中的 `.properties` 后缀的文件配置方式，2.x 版本配置文件后缀名只能为 `.xml` , `.json` 或者 `.jsn`。系统选择配置文件的优先级(从先到后)如下：

1. `.classpath` 下的名为 `log4j2-test.json` 或者 `log4j2-test.jsn` 的文件。
2.  `.classpath` 下的名为 `log4j2-test.xml` 的文件。
3.  `.classpath` 下名为 `log4j2.json` 或者 `log4j2.jsn` 的文件。
4. `.classpath` 下名为 `log4j2.xml` 的文件。

一般默认使用 `log4j2.xml` 进行命名。如果本地要测试，可以把 `log4j2-test.xml` 放到 `classpath`，而正式环境使用`log4j2.xml`，则在打包部署的时候不要打包 `log4j2-test.xml` 即可。

## 默认配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
<Appenders>
    <Console name="Console" target="SYSTEM_OUT">
        <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" />
    </Console>
</Appenders>
<Loggers>
    <Root level="error">
        <AppenderRef ref="Console" />
    </Root>
</Loggers>
</Configuration>
```

# 日志输出占位符

| 占位符     | 描述                                                                             |
| ---------- | -------------------------------------------------------------------------------- |
| `%d`       | 输出时间，后可接时间输出的样式，如 `%d{yyyy-MM-dd HH:mm:ss, SSS}`                |
| `%t`       | 输出当前线程的名称                                                               |
| `%p`       | 输出日志等级                                                                     |
| `%-5level` | 输出日志级别，`-5` 表示左对齐并且固定输出 5 个字符，如果不足则在右边使用空格补齐 |
| `%logger`  | 输出 logger 的名称，                                                             |
| `%msg`     | 日志信息，即我们传入的信息                                                       |
| `%n`       | 换行                                                                             |
| `%F`       | 输出日志语句所在类的文件名                                                       |
| `%L`       | 输出日志语句在代码中的行号                                                       |
| `%M`       | 输出方法名                                                                       |
<<<<<<< HEAD
| `%l`       | 输出日志语句所在的位置，包括类名、方法名、文件名、行号                           |
| `%%`       | 输出一个 %                                                                       |
| `%C`       | 输出日志输出语句所在类的全限定类名                                                                   | 

# Arbiters(裁决)

Arbiters 根据运行的环境来判断是否需要将 Appender 作为配置的一部分。Arbiters 会在配置文件对应的 Node Tree 转换成 Configuration 之前，获取运行环境中的一些属性，并判断是否需要将它的子节点添加的 Node Tree 中:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
<!--Configuration后面的 status，这个用于设置 log4j2 自身内部的信息输出，可以不设置，当设置成trace时，你会看到log4j2内部各种详细输出-->
<!--monitorInterval：该参数用于设置重新读取配置文件的间隔，单位秒-->
<Configuration name="ConfigTest" status="ERROR" monitorInterval="5">
    <Appenders>
	    <!-- 获取启动程序时，通过 -D 选项设置的参数名为 env 的值-->
		<!-- 如果 env 的值为 dev 就将 Console 节点加入到 Node Tree 中 -->
        <SystemPropertyArbiter propertyName="env" propertyValue="dev">
            <Console name="Out">
                <PatternLayout pattern="%m%n"/>
            </Console>
        </SystemPropertyArbiter>
    </Appenders>
    <Loggers>
        <Logger name="org.apache.test" level="trace" additivity="false">
            <AppenderRef ref="Out"/>
        </Logger>
        <Root level="info">
            <AppenderRef ref="Out"/>
        </Root>
    </Loggers>
</Configuration>
```

# 根据环境挑选配置文件

开发项目时，经常出现一个项目多个环境的情况，而不同的环境对日志的要求是不一样的。

SpringBoot 项目可以通过如下方式进行配置：

application.yaml 文件：

```yaml
spring:
  profiles:
    active: dev
```

application-dev.yaml 文件：

```yaml
logging:  
  config: classpath:log4j2-dev.xml
```
=======
| `%l`       | 输出日志语句所在的位置，包括类名、方法名、文件名、行号                           | 
| `%%`       | 输出一个 %                                                                       |
>>>>>>> aa47c46f5e3cae56f94a12bfa6d02f1d0095bb56

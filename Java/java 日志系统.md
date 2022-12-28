# 日志标准

- JCL： Apache 基金会所属的项目，是一套 Java 日志接口，之前叫 Jakarta Commons Logging，后更名为 Commons Logging
- SLF4J: 一套简单的 Java 日志门面，本身是一个标准，没有任何实现

# SLF4J

SLF4J： Simple Logging Facade For Java. SLF4J 的思想比较简单，使用了 Facade 模式，Slf4j 本身只提供了一个slf4j-api-<version>.jar 包，这个 jar 中主要是日志的抽象接口，jar 中本身并没有对抽象出来的接口做实现。

对于不同的实现，只需要提供对应的桥接器即可。如 logback 实现，则提供 logback 实现的桥接器；要使用 log4j 则提供 log4j 实现的桥接器

通过这种方式，项目可以灵活的选用项目使用的日志框架，而不需要改动对应的代码。因此，在阿里的开发手册上才有这么一条:

> 强制：应用中不可直接使用日志系统（log4j、logback）中的 API ，而应依赖使用日志框架 SLF4J 中的 API 。使用门面模式的日志框架，有利于维护和各个类的日志处理方式的统一。

```ad-warning
title: 注意

如果 classpath 中存在多个桥接器，会直接报错
```

## SLF4J API 包

```xml
<dependencies>
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.7.30</version>
    </dependency>
</dependencies>
```

该 jar 包中只要接口，没有实现。
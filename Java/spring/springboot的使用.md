# springboot 的使用

当访问路径 `/` 时，springboot 默认显示 static 文件夹下的 index.html 文件

## 常用注解

| 注解                           | 描述                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| @EnableConfigurationProperties | 将配置了 @ConfigurationProperties 注解的类注入到 IOC 容器中  |
| @ConfigurationProperties       | 声明类的属性通过 application.yaml 配置文件中对应的属性来进行初始化，比较重要的一个参数就是 prefix |
| @Value                         | 将 application.yml 文件中指定的值注入到该注解所修饰的字段上  |
| @ComponentScan                 | 使用该注解时什么参数都不传时，默认只会扫描该注解所修饰的类所在的包及其子包 |
| @AutoWired                     | @Autowired 提供这样的规则，首先它会根据类型找到对应的 Bean，如果对应类型的 Bean 不是唯一的，那么它会根据其属性名称和 Bean 的名称进行匹配。如果匹配得上，就会使用该 Bean；如果还无法匹配，就会抛出异常。 |
| @Primary                       | 声明类的优先级，当有多个相同类型的 Bean 时，优先使用该注解所修饰的类的 Bean |
| @Quelifier                     | 注入指定 name 的 Bean                                        |
| @PropertySource                | 读取指定路径的配置文件,加上 classpath 前缀表示到类路径下去找对应的配置文件，并且默认找不到配置文件会报错，可以设置 ignoreResourceNotFound 为 true |
| @Conditional                   | 该注解用于条件装配，只有满足某些条件时，才会将 Bean 装配到容器中, 指定的参数需要实现 Condition 接口，该接口的 matches 方法返回的布尔值用于判断是否将 Conditional 注解修饰的类装配到 Spring 容器中 |
| @Scope                         | 该注解用于定义 Bean 的作用域范围                             |
| @Profile                       | 该注解用于根据环境使用不同的配置文件                         |

## @EnableConfigurationProperties

该注解的作用就是让配置了 @ConfigurationProperties 注解的类生效

#### 说明

如果一个类只配置了 @ConfigurationProperties 注解而没有配置 @Component 注解, 即没有声明该类是一个 Bean 交给容器管理，那么在 IOC 容器中就获取不到 properties 文件转化成的 Bean 对象。

说白了 @EnableConfigurationProperties 相当于把配置 @ConfigurationProperties 的类进行了一次注入。并经过测试发现，不使用 @EnableConfigurationProperties 注解而使用 @Component 注解同样能够正确的进行访问，但是如果这两个注解中的任何一个都没有配置到对应的类上就会报错

## 测试

application.yaml

```yaml
swagger:
  title: "AutMaple的 API 测试"
  description: "Hello AutMaple"
  version: "1.0"
```

Swagger2Attribute.java 这个类用 applicaiton.yaml 中对应名称的属性来对类中的属性进行初始化，通过对应属性的 setter 方法进行赋值操作

```java
package com.autmaple.mall.config;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@ConfigurationProperties(prefix = "swagger")
@Component
//@EnableConfigurationProperties(Swagger2Attribute.class) 这个注解的作用和 @Component 注解的作用是一样的
public class Swagger2Attribute {
    private String title;
    private String description;
    private String version;

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getVersion() {
        return version;
    }

    public void setVersion(String version) {
        this.version = version;
    }
}
```

## Bean 的生命周期

Bean 的生命周期大致分为 Bean 定义、Bean 的初始化、Bean 的生存期和 Bean 的销毁 4 个部分。其中 Bean 定义过程大致如下：

1. Spring 通过我们的配置，如@ComponentScan 定义的扫描路径去找到带有@Component 的类，这个过程就是一个资源定位的过程。
2. 一旦找到了资源，那么它就开始解析，并且将定义的信息保存起来。注意，此时还没有初始化 Bean，也就没有 Bean 的实例，它有的仅仅是 Bean 的定义。
3. 然后就会把 Bean 定义发布到 Spring IoC 容器中。此时，IoC 容器也只有 Bean 的定义，还是没有 Bean 的实例生成。

完成了这 3 步只是一个资源定位并将 Bean 的定义发布到 IoC 容器的过程，还没有 Bean 实例的生成，更没有完成依赖注入。在默认的情况下，Spring 会继续去完成 Bean 的实例化和依赖注入，这
样从 IoC 容器中就可以得到一个依赖注入完成的 Bean。但是，有些 Bean 会受到变化因素的影响，这时我们倒希望是取出 Bean 的时候完成初始化和依赖注入，换句话说就是让那些 Bean 只是将定义发
布到 IoC 容器而不做实例化和依赖注入，当我们取出来的时候才做初始化和依赖注入等操作。

## Bean 的作用域

| 作用域        | 使用范围         | 作用域描述                                                   |
| ------------- | ---------------- | ------------------------------------------------------------ |
| singleton     | 所有 Spring 应用 | 默认值，IoC 容器只存在单例                                   |
| prototypes    | 所有 Spring 应用 | 每当从 Ioc 容器中取出一个 Bean, 则创建一个新的 Bean          |
| session       | Spring Web 应用  | HTTP 会话                                                    |
| application   | Spring Web 应用  | Web 工程生命周期                                             |
| request       | Spring Web 应用  | Web 工程单次请求                                             |
| globalSession | Spring Web 应用  | 在一个全局的 HTTP Session 中, 一个 Bean 定义对应一个实例。实践中基本不使用 |

常用的就是前 4 种, 对于 application 作用域，完全可以使用单例来替代

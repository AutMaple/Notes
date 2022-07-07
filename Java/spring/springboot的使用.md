# springboot 的使用

当访问路径 `/` 时，springboot 默认显示 static 文件夹下的 index.html 文件

## 常用注解

| 注解                           | 描述                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| @EnableConfigurationProperties | 将配置了 @ConfigurationProperties 注解的类注入到 IOC 容器中  |
| @ConfigurationProperties       | 声明类的属性通过 application.yaml 配置文件中对应的属性来进行初始化，比较重要的一个参数就是 prefix |
|                                |                                                              |

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

Swagger2Config.java

```java
package com.autmaple.mall.config;

import com.alibaba.druid.sql.visitor.functions.Concat;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2 // 开启Swagger
public class Swagger2Config {

    @Autowired
    Swagger2Attribute swagger2Attribute;

    @Bean
    public Docket createRestApi(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                //为当前包下controller生成API文档
                .apis(RequestHandlerSelectors.basePackage("com.autmaple.mall.controller"))
                //为有@Api注解的Controller生成API文档
//                .apis(RequestHandlerSelectors.withClassAnnotation(Api.class))
                //为有@ApiOperation注解的方法生成API文档
//                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo(){
        Contact contact = new Contact("AutMaple", "https://blog.autmaple.com", "autmaple609@qq.com");
        return new ApiInfoBuilder()
                .title(swagger2Attribute.getTitle())
                .description(swagger2Attribute.getDescription())
                .contact(contact)
                .version(swagger2Attribute.getVersion())
                .build();

    }
}
```


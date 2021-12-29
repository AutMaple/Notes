# Swagger-ui 的使用

Swagger-UI是HTML, Javascript, CSS的一个集合，可以动态地根据 java 注解生成在线 API 文档

项目运行后查看的地址：http://localhost:8080/swagger-ui.html

## 常用注解

| 注解               | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| @Api               | 用于修饰 Controller 类，标识这个类是 Swagger 的资源，生成 Controller 类相关信息的文档 |
| @ApiOperation      | 用于修饰 Controller 类中的方法，说明该接口的作用，生成接口方法相关的文档 |
| @APiParam          | 用于修饰接口中的参数，说明参数的作用，生成接口参数相关文档信息 |
| @ApiModel          | 修饰实体类，标记该类是 Swagger 的解析类 注解放在实体类上，不是放在路径处理方法中,当处理方法中含有实体类参数或着方法的返回值是实体类时，会自动被 Swagger 收集并生成对应的 API 文档 |
| @ApiModelProperty  | 用于修饰实体类的属性，当实体类是请求参数或返回结果时，直接生成相关文档信息 |
| @ApiIgnore         | 修饰类，标识不对这个类生成 API 文档                          |
| @ApiImplicitParams | 用在路径处理方法上，对方法中对应参数的说明，通过里面内嵌 @ApiImplicitParam 注解对每一个参数进行说明，这两个注解合起来用于解释每一个基本数据类型的参数，如果需要对方法中的实体类的参数进行说明，需要使用 @ApiModel 注解 |
| @ApiImplicitParam  | 对每一个参数进行详细的说明                                   |
| @ApiResponses      | 对返回的数据进行说明，配合 @ApiResponse 注解使用，用法和 ApiImplicitParams 注解类型 |
| @ApiResponse       | 对返回的数据进行详细的说明                                   |

### ApiImplicitParam 的参数说明和使用

- name： 对方法中的哪个参数进行说明
- value： 参数的说明
- required: 是否是必须要传递的参数，默认值是 true
- paramType: 参数放的位置
  - header：参数通过 @RequestHeader 注解获取
  - query：参数通过 @RequestParam 注解获取
  - path： 参数通过 @PathVariable 注解获取
  - body：
  - form：
- dataType: 参数的类型，默认是 String，还可以是 Integer
- defaultValue: 参数的默认值

```java
    @ApiImplicitParams(value = {
            @ApiImplicitParam(name = "pageNum", value = "页码", defaultValue = "1", required = true, paramType = "query"),
            @ApiImplicitParam(name = "pageSize", value = "每页品牌的数量", required = true, paramType = "query")
    })
    @RequestMapping(value = "/list", method = RequestMethod.GET)
    @ResponseBody
    public CommonResult<CommonPage<PmsBrand>> listBrand(@RequestParam(value = "pageNum", defaultValue = "1")
                                                                Integer pageNum,
                                                        @RequestParam(value = "pageSize", defaultValue = "3")
                                                                Integer pageSize) {
        List<PmsBrand> brandList = demoService.listBrand(pageNum, pageSize);
        return CommonResult.success(CommonPage.restPage(brandList));
    }

```

## 整合 Swagger-UI

在 pom.xml 中添加如下依赖

```xml
<!--Swagger-UI API文档生产工具-->
<dependency>
  <groupId>io.springfox</groupId>
  <artifactId>springfox-swagger2</artifactId>
  <version>2.7.0</version>
</dependency>
<dependency>
  <groupId>io.springfox</groupId>
  <artifactId>springfox-swagger-ui</artifactId>
  <version>2.7.0</version>
</dependency>
```

## 配置 Swagger-UI

配置 Swagger 需要使用 java 配置类来进行配置, 需要在配置 Swagger 的 bean 实例: Docket

Docket 各方法的作用

| 方法                        | 描述                                       |
| --------------------------- | ------------------------------------------ |
| apiInfo(ApiInfo apiInfo)    | 设置 Swagger UI 界面中的一些东西，如标题等 |
| groupName(String groupName) | 设置组的名称                               |
| ApiSelectorBuilder select() | 返回一个 api 扫描规则配置器                |

Swagger 对生成 API 文档的范围有三种不同选择，根据 Docket 这个 bean 进行配置

1. 生成指定包下面的类的 API 文档 -> `RequestHandlerSelectors.basePackage("com.autmaple.mall.controller")`
2. 选择被指定注解修饰的类生成 API 文档 -> `RequestHandlerSelectors.withClassAnnotation(Api.class)`
3. 选择被指定注解修饰的方法生成 API 文档 -> `RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class)`

```java
package com.autmaple.mall.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2 // 开启Swagger
public class Swagger2Config {

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
                .paths(PathSelectors.any()) // 表示选择哪些请求路径生成 api 文档
                .build();
    }

    private ApiInfo apiInfo(){
        return new ApiInfoBuilder()
                .title("SwaggerUi展示")
                .description("Mall-tiby")
                .contact("macro")
                .version("1.0")
                .build();
    }
}
```

## 修改MyBatis Generator注释的生成规则

CommentGenerator(自定义的一个类) 为 MyBatis Generator 的自定义注释生成器

- 修改 addFieldComment 方法使其生成 Swagger 的 @ApiModelProperty 注解来取代原来的方法注释

- 添加 addJavaFileComment 方法，使其能在 import 中导入@ApiModelProperty，否则需要手动导入该类，在需要生成大量实体类时，是一件非常麻烦的事。

```java
package com.autmaple.mall.mbg;

import org.mybatis.generator.api.IntrospectedColumn;
import org.mybatis.generator.api.IntrospectedTable;
import org.mybatis.generator.api.dom.java.CompilationUnit;
import org.mybatis.generator.api.dom.java.Field;
import org.mybatis.generator.api.dom.java.FullyQualifiedJavaType;
import org.mybatis.generator.internal.DefaultCommentGenerator;
import org.mybatis.generator.internal.util.StringUtility;

import java.util.Properties;

/**
 * 自定义注释生成器
 */
public class CommentGenerator extends DefaultCommentGenerator {
    private boolean addRemarkComments = false;
    private static final String EXAMPLE_SUFFIX="Example";
    private static final String API_MODEL_PROPERTY_FULL_CLASS_NAME="io.swagger.annotations.ApiModelProperty";

    /**
     * 设置用户配置的参数
     */
    @Override
    public void addConfigurationProperties(Properties properties) {
        super.addConfigurationProperties(properties);
        this.addRemarkComments = StringUtility.isTrue(properties.getProperty("addRemarkComments"));
    }

    /**
     * 给字段添加注释
     */
    @Override
    public void addFieldComment(Field field, IntrospectedTable introspectedTable,
                                IntrospectedColumn introspectedColumn) {
        String remarks = introspectedColumn.getRemarks();
        //根据参数和备注信息判断是否添加备注信息
        if(addRemarkComments&&StringUtility.stringHasValue(remarks)){
//            addFieldJavaDoc(field, remarks);
            //数据库中特殊字符需要转义
            if(remarks.contains("\"")){
                remarks = remarks.replace("\"","'");
            }
            //给model的字段添加swagger注解
            field.addJavaDocLine("@ApiModelProperty(value = \""+remarks+"\")");
        }
    }

    /**
     * 给 实体类的字段添加注释
     */
    private void addFieldJavaDoc(Field field, String remarks) {
        //文档注释开始
        field.addJavaDocLine("/**");
        //获取数据库字段的备注信息
        String[] remarkLines = remarks.split(System.getProperty("line.separator"));
        for(String remarkLine:remarkLines){
            field.addJavaDocLine(" * "+remarkLine);
        }
        addJavadocTag(field, false);
        field.addJavaDocLine(" */");
    }

    @Override
    public void addJavaFileComment(CompilationUnit compilationUnit) {
        super.addJavaFileComment(compilationUnit);
        //只在model中添加swagger注解类的导入
        if(!compilationUnit.isJavaInterface()&&!compilationUnit.getType().getFullyQualifiedName().contains(EXAMPLE_SUFFIX)){
            compilationUnit.addImportedType(new FullyQualifiedJavaType(API_MODEL_PROPERTY_FULL_CLASS_NAME));
        }
    }
}
```


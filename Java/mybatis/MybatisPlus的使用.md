# MybatisPlus 的使用

## 整合到 springboot 中

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.3.2</version>
</dependency>
```

添加该依赖之后不需要在添加 mybatis 的依赖，在该依赖中已经包括了 mybaits

## 自动生成代码

### 添加生成器的依赖

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.4.1</version>
</dependency>
```

### 添加模板引擎依赖

MyBatis-Plus 支持 Velocity（默认）、Freemarker、Beetl，用户可以选择自己熟悉的模板引擎，如果都不满足您的要求，可以采用自定义模板引擎。

Velocity（默认）：

```xml
<!-- https://mvnrepository.com/artifact/org.apache.velocity/velocity-engine-core -->
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity-engine-core</artifactId>
    <version>2.3</version>
</dependency>
```

### 生成代码

将该文件放在项目中，放在 java 包下即可，最后执行代码就行

```java
package com.autmaple.blogapi;

import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.*;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;


public class DaoGenerator {
    public static void main(String[] args) {
        // 代码生成器
        AutoGenerator mpg = new AutoGenerator();

        //1. 全局配置
        GlobalConfig gc = new GlobalConfig();
        // 获取项目所在的磁盘路径
        String projectPath = System.getProperty("user.dir");
        // 设置代码生成放置的位置
        gc.setOutputDir(projectPath + "/src/main/java");
        // 设置作者
        gc.setAuthor("AutMaple");
        // 设置生成的mapper接口的名称
        gc.setMapperName("%sMapper"); // 生成的所有Dao 类都以Mapper结尾
        // 设置生成的service接口的名称
        gc.setServiceName("%sService");
        // 设置service实现类的名称
        gc.setServiceImplName("%sServiceImpl");
        // 设置controller的名称
        gc.setControllerName("%sController");
        mpg.setGlobalConfig(gc);

        //2. 数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://localhost:3306/blog?useUnicode=true&useSSL=false&characterEncoding=utf8");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");
        dsc.setUsername("root");
        dsc.setPassword("root");
        mpg.setDataSource(dsc);

        //3. 包配置
        PackageConfig pc = new PackageConfig();
        pc.setModuleName("blogapi");
        pc.setParent("com.autmaple"); // 将生成的代码放在 com.autmaple.blogapi包下
        mpg.setPackageInfo(pc);

        //4. 策略配置
        StrategyConfig strategy = new StrategyConfig();
        // 设置驼峰命名规则
        strategy.setNaming(NamingStrategy.underline_to_camel);
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
        // 去掉表的前缀
        //strategy.setTablePrefix("t_");
        // 去掉表字段中的前缀
        /strategy.setFieldPrefix("...")
        mpg.setStrategy(strategy);

        // 执行代码
        mpg.execute();
    }
}
```

## 配置分页插件

```java
package com.autmaple.blog.config;

import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
@MapperScan("com.autmaple.blog.mapper")
public class MybatisPlusConfig {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor(){
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
        mybatisPlusInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
        return mybatisPlusInterceptor;
    }
}
```

### 分页的使用

```java
int pageNum = 1;
int pageSize = 5;

// 泛型 Blog 表示对哪张表进行分页
IPage<Blog> page = new Page<>(pageNum, pageSize);
IPage<Blog> ipages = blogMapper.selectPage(page, null);

// 总页数
long pages = ipages.getPages();
System.out.println(pages);

// 数据库中的数据
List<Blog> records = ipages.getRecords();
System.out.println(records);

// 总记录数
long total = ipages.getTotal();
System.out.println(total);

// 每页的大小
long size = ipages.getSize();
System.out.println(size);
```

## 增删改查

### 更新操作 select

### 插入操作 insert

### 更新操作 update

```java
User user = new User("AutMaple609");
user.setPassword("111000");
UpdateWrapper<User> wrapper = new UpdateWrapper<>();
// eq 表示等于 ==> where username = 'AutMaple' and uid = 1
wrapper.eq("username", "AutMaple").eq("uid", 1);
userMapper.update(user, wrapper);
```

### 删除操作 delete

## 配置文件

```yaml
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl // 将执行的完整 sql 语句进行输出
```


# Mybatis Generator 的使用

[Mybatis Generator 官网](https://mybatis.org/generator/)

接下来使用 Java 的方式运行 Mybatis Generator，其他的方式运行 Mybatis Generator 可以到 [https://mybatis.org/generator/running/running.html](https://mybatis.org/generator/running/running.html) 进行查看

## Mybatis的配置文件

### pom.xml 添加的依赖

```xml
 <!-- MyBatis 生成器 -->
<dependency>
  <groupId>org.mybatis.generator</groupId>
  <artifactId>mybatis-generator-core</artifactId>
  <version>1.3.3</version>
</dependency>
```

### generator.properties

```properties
jdbc.driverClass=com.mysql.cj.jdbc.Driver
jdbc.connectionURL=jdbc:mysql://localhost:3306/mall?useUnicode=true&characterEncoding=utf-8&serverTimeZone=Asia/Shanghai&useSSL=false
jdbc.userId=root
jdbc.password=root
```

### generatorConfig.xml

Mybatis Generator 配置文件官网：[https://mybatis.org/generator/configreference/xmlconfig.html](https://mybatis.org/generator/configreference/xmlconfig.html)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <properties resource="generator.properties"/>
    <context id="MySqlContext" targetRuntime="MyBatis3" defaultModelType="flat">
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>
        <property name="javaFileEncoding" value="UTF-8"/>
        <!-- 为模型生成序列化方法-->
        <plugin type="org.mybatis.generator.plugins.SerializablePlugin"/>
        <!-- 为生成的Java模型创建一个toString方法 -->
        <plugin type="org.mybatis.generator.plugins.ToStringPlugin"/>
        <!--可以自定义生成model的代码注释-->
        <commentGenerator type="com.autmaple.mall.mbg.CommentGenerator">
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
            <property name="suppressDate" value="true"/>
            <property name="addRemarkComments" value="true"/>
        </commentGenerator>
        <!--配置数据库连接-->
        <jdbcConnection driverClass="${jdbc.driverClass}"
                        connectionURL="${jdbc.connectionURL}"
                        userId="${jdbc.userId}"
                        password="${jdbc.password}">
            <!--解决mysql驱动升级到8.0后不生成指定数据库代码的问题-->
            <property name="nullCatalogMeansCurrent" value="true" />
        </jdbcConnection>
        <!--指定生成model的路径-->
        <javaModelGenerator targetPackage="com.autmaple.mall.mbg.model" targetProject="src/main/java"/>
        <!--指定生成mapper.xml的路径-->
        <sqlMapGenerator targetPackage="com.autmaple.mall.mbg.mapper" targetProject="src/main/resources"/>
        <!--指定生成mapper接口的的路径-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.autmaple.mall.mbg.mapper"
                             targetProject="src/main/java"/>
        <!--生成全部表tableName设为%-->
        <table tableName="pms_brand">
            <generatedKey column="id" sqlStatement="MySql" identity="true"/>
        </table>
    </context>
</generatorConfiguration>
```

### Generator.java

```java
package com.autmaple.mall.mbg;

import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.internal.DefaultShellCallback;

import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;

public class Generator {
    public static void main(String[] args) throws Exception {
        //MBG 执行过程中的警告信息
        List<String> warnings = new ArrayList<>();
        //当生成的代码重复时，覆盖原代码
        boolean overwrite = true;
        //读取我们的 MBG 配置文件
        InputStream is = Generator.class.getResourceAsStream("/generatorConfig.xml");
        ConfigurationParser cp = new ConfigurationParser(warnings);
        Configuration config = cp.parseConfiguration(is);
        is.close();

        DefaultShellCallback callback = new DefaultShellCallback(overwrite);
        //创建 MBG
        MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
        //执行生成代码
        System.out.println("执行了");
        myBatisGenerator.generate(null);
        //输出警告信息
        for (String warning : warnings) {
            System.out.println(warning);
        }
    }
}
```

# 常用的方法

| 方法                          | 描述                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| deleteByPrimaryKey()          | 根据主键删除对应的行                                         |
| insert()                      | 增加一行数据，没有给的 col 将会填入 null                     |
| insertSelective()             | 插入一行数据，没有给的 col 不会填入 null                     |
| insertMultiple()              | 插入多行数据，没有给的 col 将会填入 null                     |
| selectByPrimaryKey()          | 根据主键查询对应的行                                         |
| updateByPrimaryKey()          | 根据主键更新行,没有给的 col 将会填入 null                    |
| updateByPrimaryKeySelective() | 根据主键更新，没有给的 col 不会填入 null,即没有给的 col 不更新，只更新给的 col |
|                               |                                                              |

# Example 类的使用

Example 类用于动态的生成 where 子句

Example 类中含有一个内部类 Criteria, 这个类中含有一系列可添加到 where 子句中的条件.一个 Criterial 对象中添加的多个条件最终通过 and 拼接成 sql。

Example 类中可以持有多个 Criteria 对象(`List<Criteria>`)，这些 Criteria 对象最终会组合到一起组成一个 where 子句

创建 Criteria 对象可以通过 Example 实例对象的 `createCriteria` 或者 `or` 方法。调用这些方法时，会自动将生成的对象添加到 `List<Criteria>` 中。下面是这两个方法的实现

```java
public Criteria or() {
    Criteria criteria = createCriteriaInternal();
    oredCriteria.add(criteria);
    return criteria;
}

public Criteria createCriteria() {
    Criteria criteria = createCriteriaInternal();
    if (oredCriteria.size() == 0) {
        oredCriteria.add(criteria);
    }
    return criteria;
}
```

使用 `or` 方法每次都会添加到 `List<Criteria>` 中

使用 `createCriteria` 方法只有在 `List<Cirteria>` 为空的时候会添加进去

因此 `createCriteria` 方法只适合生成一个简单的 where 子句

**注意： 官网建议只使用 `or` 方法，因为该方法写出来的代码可读性更强**

## 使用示例

生成 `where field1 = 5`

```java
TestTableExample example = new TestTableExample();
example.createCriteria().andField1EqualTo(5);
```

或者

```java
TestTableExample example = new TestTableExample();
example.or().andField1EqualTo(5);
```

### 复杂的例子

```java
TestTableExample example = new TestTableExample();

  example.or()
    .andField1EqualTo(5)
    .andField2IsNull();

  example.or()
    .andField3NotEqualTo(9)
    .andField4IsNotNull();

  List<Integer> field5Values = new ArrayList<Integer>();
  field5Values.add(8);
  field5Values.add(11);
  field5Values.add(14);
  field5Values.add(22);

  example.or()
    .andField5In(field5Values);

  example.or()
    .andField6Between(3, 7);
```

最终生成的 sql：

```sql
 where (field1 = 5 and field2 is null)
     or (field3 <> 9 and field4 is not null)
     or (field5 in (8, 11, 14, 22))
     or (field6 between 3 and 7)
```


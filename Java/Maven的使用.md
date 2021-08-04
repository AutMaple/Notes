# Maven 的使用

## 什么是Maven

Maven 是一款帮助开发人员构建项目的一个工具。它会根据配置文件自动的下载和使用第三方的 jar 包以及第三方 jar 包的依赖，从而使开发人员将更多的精力放在项目的实现上，而不是项目的搭建上

## 目录结构

如果要使用 Maven， 那么项目的目录结构必须符合 Maven 的规范

## 常用命令

| 命令          | 说明                                         |
| ------------- | -------------------------------------------- |
| `mvn -v`      | 查看 Maven 版本                              |
| `mvn compile` | 编译项目下的 java 文件                       |
| `mvn test`    | 执行 test 目录下的测试文件                   |
| `mvn package` | 将项目进行打包                               |
| `mvn clean`   | 删除项目下的 target 文件夹                   |
| `mvn install` | 将项目打包并存放在本地仓库中以供其他项目使用 |

## Maven 仓库

Maven 仓库的作用是存放 jar 包。仓库分为本地仓库和中央仓库

- 本地仓库：Maven 会将下载的第三方 jar 包存放在本地仓库中, 本地仓库默认存储在`${user.home}/.m2/repository`， 其中`${user.home}`表示用户的家目录。 如果想要更改默认的存储路径可以修改 `Maven 安装目录/conf/settings.xml`文件，打开文件之后搜索 `localRepository`, 按照该文件的注释进行修改即可
- 中央仓库: Maven 官方提供的远程仓库，该仓库中存储着大量的第三方包。

当使用 Maven 编译项目时，首先会从本地寻找项目所需要的 jar 包，如果本地没有，则会根据配置文件中的配置从中央仓库中下载指定的 jar 包

## 如何指定第三方包

在 Maven 中如何设置才能够使下载到的第三方包？在每个 Maven 生成的项目中，根目录下都有一个`pom.xml`配置文件。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.autmaple</groupId>
    <artifactId>mysqlcode</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.25</version>
        </dependency>
    </dependencies>
    
</project>
```

所有的第三方包都放在`<dependencies></dependencies>`标签中

每个第三方包的配置都用`<dependency></dependency>`标签进行包裹

```xml
<dependencies>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.25</version>
    </dependency>
    <dependency>
    	......
    </dependency>
    <dependency>
    	......
    </dependency>
    ......
</dependencies>
```

每个包通过`goupId` 、 `artifactId`和`version`这三个参数进行确定。每个包的参数不需要自己填写，可以去[中央仓库](https://mvnrepository.com/)中搜索,找到需要的包，直接复制其中的配置即可，也可以去[阿里云镜像](https://maven.aliyun.com/mvn/search))中进行搜索。

## 下载第三方包

配置完项目的 `pom.xml`后，在项目根目录下使用`mvn compile`命令，Maven 会自动的从默认的下载源中下载本地没有的 jar 包，但是默认的下载源的位置位于国外，下载速度比较慢，因此可以使用如下方法配置阿里云镜像源

编辑`${user.home}/.m2/setting.xml`文件，然后将如下内容粘贴进去即可

```xml
<settings>
    <mirrors>
        <mirror>
            <id>aliyun</id>
            <name>aliyun</name>
            <mirrorOf>central</mirrorOf>
                <!-- 国内推荐阿里云的Maven镜像 -->
            <url>https://maven.aliyun.com/repository/central</url>
        </mirror>
    </mirrors>
</settings>
```

这样配置后使用的就是阿里云的镜像，速度会提升很多。
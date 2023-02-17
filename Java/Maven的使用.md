# Maven 的使用

## 什么是Maven

Maven 是一款帮助开发人员构建项目的一个工具。它会根据配置文件自动的下载和使用第三方的 jar 包以及第三方 jar 包的依赖，从而使开发人员将更多的精力放在项目的实现上，而不是项目的搭建上

## Maven的作用

1. 可以管理 jar 包
2. 自动下载 jar 包和它的文档, 源代码
3. 管理 jar 包的直接依赖，例如：a.jar 需要 b.jar 包的支持，maven 会自动去下载 b.jar
4. 管理需要的 jar 包版本
5. 编译程序,将 java 编译成 class 文件
6. 测试代码是否正确
7. 将项目打包成 jar 或者 war 格式
8. 可以帮助部署项目

**总结**

Maven 可以整合多个项目之间的引用关系，我们可以根据业务和分层需要任意拆分一个项目

Maven 提供规范的管理各个常用 jar 包及其各个版本，并且可以自动下载和引入项目中

Maven 可以根据指定版本自动解决 jar 包版本兼容问题

Maven 可以把 jar 包所依赖的其它 jar 包自动下载并引入项目

类似自动化构建工具还有：Ant, Maven, Gradle。

## 构建的过程

构建（build）是面向过程的(从开始到结尾的多个步骤)，涉及到多个环节的协同工作。

构建过程的几个主要环节:

1. 清理：删除以前的编译结果，为重新编译做好准备。
2. 编译：将 Java 源程序编译为字节码文件。maven 的编译和 javac 的编译不一样，javac 的编译一次只能够编译一个文件，而 maven 可以同时编译成百上千的 java 文件
3. 测试：针对项目中的关键点进行测试，确保项目在迭代开发过程中关键点的正确性。maven 可以同时执行多个测试代码，同时测试很多功能
4. 报告：在每一次测试后以标准的格式记录和展示测试结果。
5. 打包：将一个包含诸多文件的工程封装为一个压缩文件用于安装或部署。Java 工程对应 jar 包，Web 工程对应 war 包。这个包中含有项目所有的 class 文件、配置文件等所有的资源文件
6. 安装：在 Maven 环境下特指将打包的结果——jar 包或 war 包安装到本地仓库中。
7. 部署：将打包的结果部署到远程仓库或将war包部署到服务器上运行

## maven 的核心概念

maven 的核心概念有：POM(Project Object Model)、约定的目录结构、坐标、依赖管理、仓库管理、生命周期、插件和目标、继承、聚合

- POM: 是一个文件 => pom.xml, pom 翻译过来就是项目对象模型。maven 将一个项目当作一个模型使用。该文件用于控制 maven 构建项目的过程,管理 jar 依赖
- 约定的目录结构：maven 项目的目录和文件的位置是规定好的
- 坐标：是一个唯一的字符串，用来定位或者表示资源
- 依赖管理：管理项目中可以使用 jar 文件
- 仓库管理：远程 jar 包存放的位置
- 生命周期：maven 工具构建项目的过程，就是生命周期
- 插件和目标：执行 maven 构建的时候使用的工具就是插件
- 继承: 解决不同模块依赖同一个资源的不同版本的问题，用于对资源版本的统一,简化配置
- 聚合: 同时管理多个模块，控制它们的更新,在 pom.xml 中配置：`<packaging>pom</packaging>`, 就表明该模块专门用于管理。聚合主要用于快速构建项目

### pom.xml 文件

pom.xml 文件是 maven 的灵魂

#### pom.xml 常用标签

| 属性         | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| modelVersion | maven 模型的版本，对于 maven2 和 maven3 来说只能是 4.0.0     |
| groupId      | 组织的唯一 Id, 一般是公司域名的倒写, 格式：域名倒写或者 域名倒写 + 项目名 => com.baidu.hello |
| artifactId   | 项目名称，也是模块的名称，对应 groupId 中的子项目            |
| version      | 1）项目的版本号，如果项目还在开发中，是不稳定的版本，通常版本后面带：-SNAPSHOT，版本号使用三位标识：1.1.1<br />2）groupId、artifactId、version 三个合起来就是坐标，这三个和起来是全网唯一的，我们通过坐标来定位我们需要的 jar 包<br />3）需要哪些 jar 包可以在：https://mvnrepository.com/ 这个地址中去查找<br />4）一个项目中坐标**必须有** |
| scope        | 表示该依赖在构建项目的那个阶段会使用，可选的值有 compile, test, provided |
| packaging    | 指定项目打包后文件的扩展名，默认是 jar，可以不写，可以是 jar、war、rar、ear、pom。 |
| dependencies | 所有的依赖都放于这个标签下                                   |
| dependency   | 指定一个第三方 jar 包                                        |
| properties   | properties 是用来定义一些配置属性的，例如 `project.build.sourceEncoding`（项目构建源码编码方式），可以设置为 UTF-8，防止中文乱码，也可定义相关构建版本号，便于日后统一升级,可以自定义属性，在其他地方使用 |
| build        | 表示于构建相关的配置, 如设置编译插件的 jdk 版本              |
| parent       | 在 Maven 中，如果多个模块都需要声明相同的配置，例如：groupId、version、有相同的依赖、或者相同的组件配置等，也有类似 Java 的继承机制，用 parent 声明要继承的父工程的 pom 配置。 |
| modules      | 在 Maven 的多模块开发中，为了统一构建整个项目的所有模块，可以提供一个额外的模块，该模块打包方式为 pom，并且在其中使用 modules 聚合的其它模块，这样通过本模块就可以一键自动识别模块间的依赖关系来构建所有模块，叫 Maven 的聚合。 |

### 依赖的作用范围

表示在 maven 构建项目的过程中的那个阶段起作用

| 值       | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| complie  | 默认值,在构建的全过程都需要用到                              |
| test     | 只对测试程序起作用                                           |
| provided | 编译，测试着两个阶段需要用，在打包时不需要使用 => 通常是运行环境中有，我们提供的 jar 包不需要内置了，直接用运行环境中提供的即可 |

|                    | compile | test | provided | runtime |
| ------------------ | ------- | ---- | -------- | ------- |
| 是否对主程序有效   | 是      | 否   | 是       | 否      |
| 是否对测试程序有效 | 是      | 是   | 是       | 否      |
| 是否参与打包       | 是      | 否   | 否       | 是      |
| 是否参与部署       | 是      | 否   | 否       | 是      |

## 目录结构

如果要使用 Maven， 那么项目的目录结构必须符合 Maven 的规范

每一个 maven 项目在磁盘中都是一个文件夹

maven 中约定的目录结构

```text
Hello/
	src/
		main/
			java/ # 存放java 代码
			resources/ # 存放 java 中要使用的配置文件
		test/ # 放置测试程序和测试程序的配置文件
			java/
			resources/
	pom.xml # maven的核心文件
```

## 属性

### 属性的类别

自定义属性，内置属性，Setting属性，java系统属性，环境变量属性

系统属性查看方式

```shell
mvn help:system
```

## 全局变量

在 Maven 的 pom.xml 文件中，`<properties>` 可用于定义全局变量，POM 中通过`${property_name}`的形式引用变量的值

定义全局变量

```xml
<properties>
	<spring.version>4.3.10.RELEASE</spring.version>
</properties>
```

引用全局变量

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>${spring.version}</version>
</dependency>
```

## 系统变量

```xml
<properties>
    <-- 源码编译 jdk 版本 -->
    <maven.compiler.source>1.8</maven.compiler.source> 
    <-- 运行代码的 jdk 版本 -->
    <maven.compiler.target>1.8</maven.compiler.target>
	<-- 项目构建使用的编码，避免中文乱码 -->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding> 
    <-- 生成报告的编码 -->
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding> 
</properties>
```

## 内置变量

- ${basedir} 项目根目录(即 pom.xml 文件所在目录)
- ${project.build.directory} 构建目录，缺省为 target 目录
- ${project.build.outputDirectory} 构建过程输出目录，缺省为target/classes
- `${project.build.finalName}` 产出物名称，缺省为`${project.artifactId}-${project.version}`
- ${project.packaging} 打包类型，缺省为jar
- ${project.xxx} 当前 pom 文件的任意节点的内容
- `${env.xxx}` 获取系统环境变量。例如,"env.PATH"指代了$path环境变量（在 Windows上 是 %PATH% ）。
- ${settings.xxx} 指代了 settings.xml 中对应元素的值。例如：`<settings><offline>false</offline></settings>` 通过 ${settings.offline} 获得 offline 的值。
- Java System Properties: 所有可通过java.lang.System.getProperties() 访问的属性都能在POM 中使用，例如 ${JAVA_HOME}。

Maven总共有6类属性，内置属性、POM属性、自定义属性、Settings属性、java系统属性和环境变量属性；

### 内置属性

两个常用内置属性 ${basedir} 或者 ${project.basedir} 表示项目跟目录，即包含pom.xml文件的目录 ${version} 表示项目版本

### POM 属性

用户可以使用该类属性引用POM文件中对应元素的值。如${project.artifactId} 就对应了`<project> <artifactId>`元素的值，常用的POM属性包括：

- ${project.build.sourceDirectory}: 项目的主源码目录，默认为 `src/main/java/`
- ${project.build.testSourceDirectory}: 项目的测试源码目录，默认为src/test/java/
- ${project.build.directory}: 项目构建输出目录，默认为target/
- ${project.outputDirectory}: 项目主代码编译输出目录，默认为target/classes/
- ${project.testOutputDirectory}：项目测试主代码输出目录，默认为target/testclasses/
- ${project.groupId}：项目的groupId
- ${project.artifactId}：项目的artifactId
- ${project.version}：项目的 version,与 ${version} 等价
- `${project.build.finalName}`: 项目打包输出文件的名称，默认为 `${project.artifactId}-${project.version}`

### 自定义属性

如下 account-aggregator 的 pom.xml，那么继承了此pom.xml 的子模块也可以用此自定义属性

### Settings属性

与POM属性同理，用户使用以 settings. 开头的属性引用settings.xml 文件中的 XML 元素的值。 

### Java系统属性

所有 java 系统属性都可以用 Maven 属性引用，如 ${user.home} 指向了用户目录。

### 环境变量属性

所有环境变量属性都可以使用以 `env.` 开头的 Maven 属性引用，如 ${env.JAVA_HOME} 指代了 JAVA_HOME 环境变量的的值。


## 常用命令

| 命令               | 说明                                                         |
| ------------------ | ------------------------------------------------------------ |
| `mvn -v`           | 查看 Maven 版本                                              |
| `mvn compile`      | 该命令需要在 pom.xml 文件所在的目录下执行。它会编译 src/main 目录下的所有 java 文件, 编译成功之后会在项目根目录下创建一个 target 文件夹,该文件夹放置了所有编译好的 class 文件和资源文件。其中 class 文件存放在 target 文件夹下的 classes 目录下，资源文件也放在 classes 目录下 |
| `mvn test`         | 执行 test 目录下的测试文件,并会生成一个目录 `surefire-reports`，保存测试结果 |
| `mvn package`      | 将项目进行打包。会编译、编译测试、测试、并且按照 pom.xml 配置把主程序打包生成 jar 包或者 war 包 |
| `mvn clean`        | 删除项目下的 target 文件夹, 但是已经 install 到仓库里的包不会删除 |
| `mvn install`      | 会把本工程打包，并且按照本工程的坐标保存到本地仓库中,供其他项目使用 |
| `mvn deploy`       | 部署主程序。会把本工程打包，按照本工程的坐标保存到本地库中，并且还会保存到私服仓库中。还会自动把项目部署到 web 容器中 |
| `mvn test-compile` | 编译测试程序，会在当前目录下生成一个 target,里边存放编译测试程序之后生成的字节码文件 |

### 生命周期

1. mvn clean: 清理(会删除原来编译和测试的目录，即 target 目录，但是已经 install 到仓库里的包不会删除)
2. mvn compile: 编译主程序(会在当前目录下生成一个 target,里边存放编译主程序之后生成的字节码文件)
3. mvn test-compile: 编译测试程序(会在当前目录下生成一个 target,里边存放编译测试程序之后生成的字节码文件)
4. mvn test: 测试(会生成一个目录surefire-reports，保存测试结果)
5. mvn package: 打包主程序(会编译、编译测试、测试、并且按照 pom.xml 配置把主程序打包生成 jar 包或者 war 包), 里面的内容只包含 main/java 下的文件和资源文件，不包含测试文件
6. mvn install: 安装主程序(会把本工程打包，并且按照本工程的坐标保存到本地仓库中)
7. mvn deploy: 部署主程序(会把本工程打包，按照本工程的坐标保存到本地库中，并且还会保存到私服仓库中。还会自动把项目部署到 web 容器中)。

**执行某个命令时，之前的命令都会执行**，例如执行 `mvn test` 命令，则之前的三个命令都执行了

如果某个命令执行失败，将不会继续向下执行

### 配置插件

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
    			<-- 告诉 maven 代码使用的 jdk1.8-->
                <source>1.8</source>
    			<-- 代码运行在 jdk1.8 -->
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>	
```

### 资源插件

该插件默认只处理 src/mian/resources 目录下的资源文件，其他地方存放的资源文件默认不处理，如果需要该插件处理，需要在 pom.xml 中对该插件进行配置

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory><!--所在的目录-->
            <includes><!--包括目录下的.properties,.xml 文件都会扫描到-->
            	<include>**/*.properties</include>
            	<include>**/*.xml</include>
            </includes>
            <!—filtering 选项 false 不启用过滤器， *.property 已经起到过滤的作用了 -->
            <filtering>false</filtering>
        </resource>
    </resources>
</build>
```

## Maven 仓库

Maven 仓库的作用是存放 jar 包。仓库分为本地仓库和中央仓库

- 本地仓库：Maven 会将下载的第三方 jar 包存放在本地仓库中, 本地仓库默认存储在`${user.home}/.m2/repository`， 其中`${user.home}`表示用户的家目录。 如果想要更改默认的存储路径可以修改 `Maven 安装目录/conf/settings.xml`文件，打开文件之后搜索 `localRepository` 标签, 按照该文件的注释进行修改即可,但是注意，路径中不能含有中文
- 中央仓库: Maven 官方提供的远程仓库，该仓库中存储着大量的第三方包。

当使用 Maven 编译项目时，首先会从本地寻找项目所需要的 jar 包，如果本地没有，则会根据配置文件中的配置从中央仓库中下载指定的 jar 包

### 仓库的分类

- 本地仓库：即 maven 下载 jar 包时存放的位置
- 远程仓库：网络上的仓库，需要联网才能够使用
  - 中央仓库：最权威的，所有开发人员都共享的一个集中仓库, 中央仓库的地址：https://repo.maven.apache.org
  - 中央仓库的镜像：在各大洲中央仓库的备份，
  - 私服：在公司内部，在局域网中使用, 不对外使用

### 仓库的使用

当项目中使用一个第三方的 jar 时，maven 查找仓库的顺序：本地仓库 => 私服 => 镜像 => 中央仓库, 只要查找到对应的 jar 包，就不继续向上继续查找

### 仓库中存放的内容

1. Maven 的插件，插件也是一些 jar，这些 jar 可以完成一定的功能。
2. 我们自己开发项目的模块
3. 第三方框架或工具的 jar 包

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

## maven idea 中的配置

通常 idea 中会自带 maven，但是我们通常不适用内置的 maven，而是使用自己下载好的 maven

需要在 idea中：file -> settings -> Build, Execution,Deployment -> maven 进行配置

配置：Maven home path, User settings file, Local repository

还有 Maven -> Runner -> VM Options: -DarchetypeCatalog=internal, 如果报错

在使用 maven 创建项目时，maven 会联网下载模板文件，比较大，-DarchetypeCatalog=internal 这个选项的作用就是禁止 maven 联网去下载那些模板文件

## 依赖管理

直接依赖：直接依赖是指项目直接指定的 jar 包，而 jar 包依赖的 jar 则是该项目的间接依赖

### 依赖传递冲突问题的解决

- 路径优先：当依赖中出现相同的资源时，层级越深，优先级越底，层级越低，优先级越高。其中直接依赖位于最底层
- 声明优先：当相同资源在相同层级被依赖时,配置顺序靠前的覆盖配置顺序靠后的
- 特殊优先：当同级配置了相同资源的不同版本，后声明的覆盖先声明的

### 可选依赖

如果某个依赖不想让依赖这个项目的项目知道它依赖了哪些 jar 包，可以使用 `optional` 标签，并将值设置为 true，默认值是 false， 即可见

### 排除依赖

排除依赖是指主动的断开其依赖的某个资源的某些依赖,此时不需要指定版本

```xml
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity-engine-core</artifactId>
    <version>2.3</version>
    <exclusions>
        <exclusion>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

### 依赖范围的传递性

第一行是直接依赖中指定的范围，第一列是间接依赖

|          | compile | test | provided | runtime |
| -------- | ------- | ---- | -------- | ------- |
| compile  | compile | test | provided | runtime |
| test     |         |      |          |         |
| provided |         |      |          |         |
| runtime  | runtime | test | provided | runtime |

## 插件

插件与生命周期内的阶段绑定，在执行到对应生命周期时，执行对应的插件的功能

默认maven在各个生命周期上绑定有预设的功能

通过插件可以自定义更多的功能

可直接在官网上去查

## 版本

- SNAPSHOT(快照版本)
  - 项目开发过程中,为方便团队成员合作，解决模块间相互依赖和实是更新的问题，开发者对每个模块进行构建的时候,输出的临时版本叫做快照版本
- RELEASE(发布版本)
  - 项目开发进入到里程碑之后，向团队外部发布比较稳定的版本, 这种版本所对应的构建文件是稳定的，即便进行功能的后续开发,也不会改变当前发布版本的内容，这种版本叫做发布版本

## 跳过测试

```shell
mvn [command] -D skipT
```

## dependencyManagemant

dependencyManagemant 的作用是用来声明依赖，但是不会将对应的依赖导入到项目中。

### 特点

- 子项目不会继承 dependencyManagement 节点中声明的依赖。
- 但如果子项目想导入某个父 pom 中 dependencyManagement 节点中的依赖，只需要填写 **groupId 和 artifactId** ,不需要填写版本号，maven 会自动去父 pom 的 dependencyManagement 中找对应的 version，包括 scope、exclusions 等

使用 **dependencyManagement** 组件后，所有的子项目只需要在父 pom 的 “公共依赖声明池” 中挑选自己想要的依赖，而不用关心版本。这样即不会继承到不需要的依赖，又统一了依赖的版本

如果某个子项目不想使用公共的版本号，只需要在 dependency 中加上版本号，子项目就会使用自定义的版本号，不会继承父类版本号。

## 依赖版本的查找  

Maven 会沿着父子层次向上走，直到找到一个拥有 dependencyManagement 组件的项目，然后在其中查找，如果找到则返回申明的依赖，没有继续往下找。

Maven 会先在本地 Repository 中查找依赖，如果依赖存在，则使用该依赖，如果不存在，则通过 pom.xml 中的 Repository 配置从远程下载依赖到本地 Repository 中。默认情况下，Maven 将使用 *Maven Central Repository* 作为远端 Repository。但是在 pom.xml 中为什么没有看到这样的配置信息呢？原因在于，任何一个 Maven 工程都默认地继承自一个 [Super POM](http://books.sonatype.com/mvnref-book/reference/pom-relationships-sect-pom.html#pom-relationships-sect-super-pom)，Repository 的配置信息便包含在其中。

## dependencies 与 dependencyManagement 的区别

**dependencies**

- 引入依赖
- 即使子项目中不写 dependencies ，子项目仍然会从父项目中继承 **dependencies** 中的所有依赖项

**dependencyManagement**

- 声明依赖，并不引入依赖。
- 子项目默认不会继承父项目 **dependencyManagement** 中的依赖
- 只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承（version、exclusions、scope 等读取自父 pom）
- 子项目如果指定了依赖的具体版本号，会优先使用子项目中指定版本，不会继承父 pom 中申明的依赖

## 反应堆 reactor

在一个多模块的 Maven 项目中，反应堆是指所有模块组成的一个构建结构。模块间的依赖关系会将反应堆构成一个有向非循环图(Directed Acyclic Graph, DAG)，各个模块是该图的节点，依赖关系构成了有向边。这个图不允许出现循环，因此，当出现模块 A 依赖于 B，而 B 又依赖于 A 的情况时，Maven 就会报错。

Maven 会根据自己的 Reactor 机制决定哪个模块应该先执行，哪个模块应该后执行。比如，在父工程中执行 `mnv clean install` 命令并且 webapp 模块依赖于 core 模块，那么 Maven 会先在 core 模块上执行 “mvn clean install”，再在 webapp 模块上执行相同的命令。

当在 webapp 中执行 `mvn clean install`，Maven 发现 webapp 自己依赖于 core，此时 Maven 会在本地的 Repository 中去找 core，如果存在，那么你很幸运，如果不存在，那么对不起，运行失败，说找不到 core，因为 Maven 并不会先将 core 模块安装到本地 Repository。此时你需要做的是，切换到 core 目录，执行 `mvn clean install` 将 core 模块安装到本地 Repository，再切换回 webapp 目录，执行 `mvn clean install`，万事才大吉。

在父模块下执行 `mvn clean install`， Maven 会在每个模块上执行该命令，然后又发现 webapp 依赖于 core，此时他们之间有一个协调者（即父工程），它知道将 core 作为 webapp 的依赖，于是会先在 core 模块上执行 `mvn clean install`，当在 webapp 上执行命令时，无论先前的 core 模块是否存在于本地 Repository 中，父工程都能够获取到 core 模块（如果不存在于本地 Repository，它将现场编译 core 模块，再将其做为 webapp 的依赖，比如此时使用 `mvn clean package` 也是能够构建成功的），所以一切成功。

## paren 节点的 relativePath 节点

relativePath 用于指定父 pom.xml 文件的位置，它的默认值是 `../pom.xml`

### Maven 寻找父模块 pom.xml 文件的顺序

1. 首先会在当前项目中查找 parent 节点中的坐标对应的 pom.xml 文件
2. 根据相对路径查找 parent 节点中的坐标对应的 pom.xml 文件
3. 在本地仓库查找 parent 节点中的坐标对应的 pom.xml 文件
4. 在远程仓库查找 parent 节点中的坐标对应的 pom.xml 文件

## Scope 为 import 的作用

```xml
<dependencyManagement>
	<dependencies>
		<dependency>
			<!-- Import dependency management from Spring Boot -->
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-dependencies</artifactId>
			<version>2.1.12.RELEASE</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
</dependencyManagement>
```

`<scope>import</scope>`，它的意思是将 `spring-boot-dependencies` 中 `dependencyManagement` 的 `dependencies`，全部引入到当前工程的`dependencyManagement` 中，官方文档的解释

>import
>This scope is only supported on **a dependency of type pom** in the `<dependencyManagement>` section. It indicates the dependency to be replaced with the effective list of dependencies in the specified POM’s
>`<dependencyManagement>` section. Since they are replaced, dependencies with a scope of import do not actually participate in limiting the transitivity of a dependency.

`import` 只能用在 `dependencyManagement` 块中，它将 `spring-boot-dependencies` 中 `dependencyManagement` 下的 `dependencies` 插入到当前工程的 `dependencyManagement` 中，所以不存在依赖传递。

### 无 import

当没有 `<scope>import</scope>` 时，意思是将 `spring-boot-dependencies` 的 `dependencies` 全部插入到当前工程的 `dependencies` 中，并且会依赖传递。

## Maven 私服

### Nexus

Nexus 是一个强大的 Maven 仓库管理工具，使用 Nexus 可以方便的管理内部仓库同时简化外部仓库的访问。

### 仓库的类型

| 类型    | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| proxy   | 表示这个仓库是一个远程仓库的代理，最典型的就是代理 Maven 中央仓库 |
| hosted  | 宿主仓库，公司自己开发的一些 jar 存放在宿主仓库中，以及一些在 Maven 中央仓库上没有的 jar |
| group   | 仓库组，包含代理仓库和宿主仓库                               |
| virtual | 虚拟仓库                                                     |

nexus 中包括很多仓库，hosted 中存放的是企业自己发布的 jar 包及第三方公司的 jar 包，proxy 中存放的是中央仓库的 jar，为了方便从私服下载 jar 包可以将多个仓库组成一个仓库组，每个工程需要连接私服的仓库组下载 jar 包

### 上传 jar 包

上传 jar 包到私服，需要配置两个地方：

1. Maven 的 conf/settings.xml
2. 项目的 pom.xml 文件

settings.xml

```xml
<server>
  <id>releases</id>
  <username>admin</username>
  <password>admin123</password>
</server>
<server>
  <id>snapshots</id>
  <username>admin</username>
  <password>admin123</password>
</server>
```

pom.xml

```xml
<distributionManagement>
    <repository>
        <id>releases</id>
        <!-- 这里输入私服对应仓库的地址 -->
        <url>http://localhost:8081/repository/maven-releases/</url>
    </repository>
    <snapshotRepository>
        <id>snapshots</id>
        <!-- 当项目的版本号带有 SNAPSHOT 字样时，就会上传到该地址对应的仓库上 -->
        <url>http://localhost:8081/repository/maven-snapshots/</url>
    </snapshotRepository>
</distributionManagement>
```

# Spring 开发

## Spring 的介绍

Spring 框架是 Java 平台的一个开源的全栈( full-stack) 应用程序框架和控制反转( Inversion of Control，IoC )容器实现，一般被直接称为 Spring。该框架的一些核心功能理论上可用于任何 Java 应用，但 Spring 还为基于 Java 企业版平台构建的 Web 应用提供了大量的拓展支持。**Spring 没有直接实现任何的编程模型**，但它已经在 Java 社区中广为流行，基本上完全代替了企业级 JavaBeans（EJB）模型。

**核心技术**：IOC （Inversion of Control）， AOP（Aspect Oriented Programming）

## 控制反转 IOC

IOC ==> Iversion of Control 。控制反转在 java 中表示把创建对象的权利交给框架，也就是指将对象的创建和赋值、对象的存储、对象的管理交给了 Spring 容器。Spring 容器是 Spring 中的一个核心模块，用于管理对象，底层是一个 ConcurrentHashMap 集合。Spring 实现 IOC 通过 DI (Dependency Injection )依赖注入来实现。

### 依赖注入 DI

依赖注入 DI (Dependency Injection ) 是 IOC 的一种技术实现，Spring 通过 java 的反射技术实现。在程序中只需要提供类的名称, Spring 就会自动的创建和管理该类。至于对象的创建、存储、查找、获取都由容器( Spring )内部自己实现，程序员无需理会。

## 面向切面编程 AOP

面向切面编程（Aspect-Oriented Programming, AOP） 就是将那些与业务无关，却为业务模块所共同调用的逻辑或责任分开封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可操作性和可维护性。

## Spring 的配置文件

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="..." class="..." .../>
    ...
    <bean id="..." class="..." .../>
    
</beans>
```

> - Spring 中把类称为 Bean
> - id 类的表示符，可以不写，Spring 会提供默认值
> - class 类 的全名，需要指定类所在的位置( 全路径 )，必须填写，Spring 通过这个设置创建对象
> - 该配置文件告诉 Spring 需要创建那些对象，有多少个 就创建多少个对象

### bean 对象的 scope 属性

常用可选值: singleton 和 prototype 

区别：

- singleton表示单实例，prototype 表示多实例
- 当 scope 是 singleton 的时候，java 在加载配置文件时就会创建单实例对象
- 当 scope 是 prototype 的时候，java 不会在加载配置文件时创建对象，而是在调用 getBean 方法的时候在创建对象

### bean 的生命周期

1. 通过构造器创建 bean 实例（无参构造方法）
2. 为 bean 设置属性值（ setter 方法）
3. 调用 bean 的后置处理器方法 postProcessBeforeInitialization （需要 bean 实现 BeanPostProcessor 接口， 可选）
4. 调用 bean 的初始化方法 （需要配置初始化方法， 通过 init-method 属性配置）
5. 调用 bean 的后置处理器方法 postProcessAfterInitialization (需要 bean 实现 BeanPostProcessor 接口, 可选)
6. bean创建完毕，可以使用
7. 当容器关闭时，调用 bean 的销毁方法（需要配置销毁方法， 通过 destroy-method 属性配置）

注意：如果在配置文件中声明了后置处理器，那么配置文件中的所有 bean 都会被配置后置处理器

User.java

```java
package com.autmaple.entity;

public class User {
    private String name;

    public User(){
        System.out.println("调用无参构造方法");
    }
    public String getName() {return name;}

    public void setName(String name) {
        System.out.println("调用 setter 方法");
        this.name = name;
    }

    public void initMethod(){
        System.out.println("调用 initMethod");
    }

    public void destroyMethod(){
        System.out.println("调用 destroyMethod ");
    }
}

```

bean.xml

```xml
<bean class="com.autmaple.entity.User" id="user7" init-method="initMethod" destroy-method="destroyMethod">
    <property name="name" value="AutMaple"/>
</bean>
<bean class="com.autmaple.entity.BeanPost" id="beanPost"/>
```

BeanPost.java

```java
package com.autmaple.entity;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

public class BeanPost implements BeanPostProcessor {

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("before init");
        return BeanPostProcessor.super.postProcessBeforeInitialization(bean, beanName);
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("after init");
        return BeanPostProcessor.super.postProcessAfterInitialization(bean, beanName);
    }
}

```

test.java

```java
ClassPathXmlApplicationContext cpx = new ClassPathXmlApplicationContext("bean.xml");
User user7 = cpx.getBean("user7", User.class);
System.out.println(user7);
cpx.close();
```

输出：

```text
调用无参构造方法
调用 setter 方法
before init
调用 initMethod
after init
User{name='AutMaple'}
调用 destroyMethod 
```

## Spring 获取对象

配置文件 Bean.xml

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user" class="com.autmaple.User"/>

</beans>
```

代码中使用对象

```JAVA
ClassPathXmlApplicationContext cpx = new ClassPathXmlApplicationContext("Bean.xml");
User user = (User)cpx.getBean("user");
System.out.println(user.getName());
```

> 1. ClassPathXmlApplicationContext 就是容器对象，Spring 创建的对象可以通过该容器获得
> 2. Bean.xml 是 Spring 的配置文件，在该配置文件中声明需要创建的对象
> 3. Spring 默认使用无参构造方法构建对象
> 4. **Spring 默认在创建容器时就将所有的对象创建好，并放在容器中，并不是程序需要使用的时候才去创建**

### 获取容器相关信息

获取容器中创建的对象数量

```JAVA
int count = cpx.getBeanDefinitionCount();
```

获取容器中创建的对象的名称

```JAVA
String[] names = cpx.getBeanDefinitionNames();
```

## Spring 给对象赋值

java 中给对象赋值有两种方式：构造方法或者 setter 方法，Spring 两种方式都支持。Spring 可以在 xml 中进行相关的配置进行赋值也可以使用注解的方法进行赋值。

Spring 中建议使用 setter 方法进行注入

### xml 配置中进行赋值操作

#### setter 方法进行赋值

xml 中使用 setter 赋值需要配置 bean 的 property 属性。如果赋值的是基本数据类型，可以使用`value="..."`的方式进行赋值；如果是引用数据类型则需要使用`ref="..."`。

User.java

```JAVA
package com.autmaple;

public class User {
    private String name;
    private int age;
    private School school;

    public String getName() {return name;}
    public void setName(String name) {this.name = name; }

    public int getAge() { return age;}
    public void setAge(int age) {this.age = age;}

    public School getSchool() { return school;}
    public void setSchool(School school) { this.school = school;}

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", school=" + school +
                '}';
    }
}
```

School.java

```JAVA
public class School {
    private String name;
    private String address;

    public String getName() {return name; }
    public void setName(String name) {this.name = name;}

    public String getAddress() {return address;}
    public void setAddress(String address) {this.address = address; }

    @Override
    public String toString() {
        return "School{" +
                "name='" + name + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
}
```

xml 配置

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="user" class="com.autmaple.User">
        <property name="age" value="10"/>
        <property name="name" value="AutMaple"/>
        <property name="school" ref="school"/>
    </bean>

    <bean id="school" class="com.autmaple.School">
        <property name="name" value="市一中"/>
        <property name="address" value="郴州市北湖区"/>
    </bean>
</beans>
JAVA
ClassPathXmlApplicationContext cpx = new ClassPathXmlApplicationContext("Bean.xml");
User user = cpx.getBean(User.class);
System.out.println(user); 
//输出： User{name='AutMaple', age=10, school=School{name='市一中', address='郴州市北湖区'}}
```

除了使用 property 属性进行 setter 注入外，还可以使用 p 命名空间进行 setter 注入，该方法了解即可，不经常使用

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user" class="com.autmaple.User"
    p:age="10" p:name="AutMaple" p:school-ref="school"/>

    <bean id="school" class="com.autmaple.School">
        <property name="name" value="市一中"/>
        <property name="address" value="郴州市北湖区"/>
    </bean>

</beans>
```

注意：p 需要引入名称空间，在 IDEA 中使用时直接根据提示进行引入即可(Alt + Enter), 使用 IDEA 会在 xml 加入`xmlns:p="http://www.springframework.org/schema/p"`

#### 构造方法进行赋值

使用构造方法进行赋值，需要使用 bean 的 constructor-arg 属性。基本数据类型使用 value 赋值；引用数据类型使用 ref 进行赋值

```XML
<bean id="user" class="com.autmaple.User">
        <constructor-arg name="age" value="10"/>
        <constructor-arg name="name" value="AutMaple"/>
        <constructor-arg name="school" ref="school"/>
    </bean>

    <bean id="school" class="com.autmaple.School">
        <property name="name" value="市一中"/>
        <property name="address" value="郴州市北湖区"/>
    </bean>
```

也可以根据 bean 构造方法的参数位置进行赋值。下标从 0 开始

```XML
<bean id="user" class="com.autmaple.User">
    <constructor-arg index="0" value="AutMaple"/>
    <constructor-arg index="1" value="10"/>
    <constructor-arg index="2" ref="school"/>
</bean>
```

#### 自动注入

自动注入只能够在引用数据类型中使用。Spring 有两种自动注入方式：byName, byType。

byName: java 类中引用数据类型的属性名称与 Spring 容器中 bean 的 id 名称一样，且数据类型也一样的时候，该属性会被 Spring 自动赋值

byType: java 类中引用的数据类型和 bean 中的 class 是同源的时候，也会被 Spring 自动的进行赋值。但是需要注意的是，如果 xml 配置文件中出现了 多个同源的 bean 时，会报错。

通过设置 bean 中的 autowire 属性即可进行配置

byName 自动注入

```XML
<bean id="user" class="com.autmaple.User" autowire="byName">
    <property name="age" value="10"/>
    <property name="name" value="AutMaple"/>
</bean>

<bean id="school" class="com.autmaple.School">
    <property name="name" value="市一中"/>
    <property name="address" value="郴州市北湖区"/>
</bean>
```

```java
ClassPathXmlApplicationContext cpx = new ClassPathXmlApplicationContext("Bean.xml");
User user = cpx.getBean(User.class);
System.out.println(user); 
//输出： User{name='AutMaple', age=10, school=School{name='市一中', address='郴州市北湖区'}}
```

byType 自动注入

PrimarySchool.java

```JAVA
package com.autmaple;

public class PrimarySchool extends School{}
```

```xml
<bean id="user" class="com.autmaple.User" autowire="byType">
    <property name="age" value="10"/>
    <property name="name" value="AutMaple"/>
</bean>


<bean id="primarySchool" class="com.autmaple.PrimarySchool">
    <property name="name" value="金海小学"/>
    <property name="address" value="郴州市苏仙区"/>
</bean>
```

```java
ClassPathXmlApplicationContext cpx = new ClassPathXmlApplicationContext("Bean.xml");
User user = cpx.getBean(User.class);
System.out.println(user); 
//输出：User{name='AutMaple', age=10, school=School{name='金海小学', address='郴州市苏仙区'}}
```

### 外部 Bean 的注入

有些对象的创建不是通过构造方法创建出来的，而是使用其他的方法创建的。这种对象如何使用 Spring 容器来创建和管理呢？答案是使用工厂注入的方式。工厂分为静态工厂和实例工厂，它们将 bean 对象注入到 Spring 容器的方式不同。

#### 静态工厂

首先提供静态工厂类 DruidUtils

```JAVA
package com.autmaple;

public class DruidUtils {
    private static User user;
    public static User getInstance(){
        if(user == null){
            user = new User();
        }
        return user;
    }
}
```

然后再 xml 配置文件中配置该工厂

```XML
<bean id="userFactory" class="com.autmaple.DruidUtils" factory-method="getInstance"/>
```

在到 java 中使用

```JAVA
ClassPathXmlApplicationContext cpx = new ClassPathXmlApplicationContext("Bean.xml");
User user = cpx.getBean("userFactory", User.class);
System.out.println(user); // 输出: User{name='null', age=0, school=null}
```

#### 实例工厂

首先提供工厂类 DruidUtils

```JAVA
public class DruidUtils {
    private User user;
    public User getInstance(){
        if(user == null){
            user = new User();
        }
        return user;
    }
}
```

然后在 xml 配置文件中配置该工厂。配置实例工厂时，由于需要先获取到实例才能够使用其方法，因此需要先配置工厂的 bean。然后在该实例来获取对应的 bean

```XML
<bean id="User" class="com.autmaple.DruidUtils"/>
<bean id="userFactory" class="com.autmaple.User" factory-bean="User2" factory-method="getInstance1"/>
```

在 java 中使用

```JAVA
ClassPathXmlApplicationContext cpx = new ClassPathXmlApplicationContext("Bean.xml");
User user = cpx.getBean("userFactory", User.class);
System.out.println(user);
```

### 复杂属性的赋值

#### 数组注入

数组注入和集合注入在 xml 中的配置是一样的。如下：

```XML
<bean class="org.javaboy.User" id="user">
    <property name="cat" ref="cat"/>
    <property name="favorites">
        <array>
            <value>足球</value>
            <value>篮球</value>
            <value>乒乓球</value>
        </array>
    </property>
</bean>
<bean class="org.javaboy.Cat" id="cat">
    <property name="name" value="小白"/>
    <property name="color" value="白色"/>
</bean>
```

注意，array 节点，也可以被 list 节点代替。

当然，array 或者 list 节点中也可以是对象。

```XML
<bean class="org.javaboy.User" id="user">
    <property name="cat" ref="cat"/>
    <property name="favorites">
        <list>
            <value>足球</value>
            <value>篮球</value>
            <value>乒乓球</value>
        </list>
    </property>
    <property name="cats">
        <list>
            <ref bean="cat"/>
            <ref bean="cat2"/>
            <bean class="org.javaboy.Cat" id="cat3">
                <property name="name" value="小花"/>
                <property name="color" value="花色"/>
            </bean>
        </list>
    </property>
</bean>
<bean class="org.javaboy.Cat" id="cat">
    <property name="name" value="小白"/>
    <property name="color" value="白色"/>
</bean>
<bean class="org.javaboy.Cat" id="cat2">
    <property name="name" value="小黑"/>
    <property name="color" value="黑色"/>
</bean>
```

注意，即可以通过 ref 使用外部定义好的 Bean，也可以直接在 list 或者 array 节点中定义 bean。

#### Map 注入

```XML
<property name="map">
    <map>
        <entry key="age" value="99"/>
        <entry key="name" value="javaboy"/>
    </map>
</property>
```

#### Properties 注入

```XML
<property name="info">
    <props>
        <prop key="age">99</prop>
        <prop key="name">javaboy</prop>
    </props>
</property>
```

## Spring 配置文件中引入 properties 文件

引入 context 名称空间

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xmlns:context="http://www.springframework.org/schema/context" <==
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"> <==
```

引入 properties 文件

```xml
<context:property-placeholder location="classpath:jdbc.properties"/>
```

jdbc.properties

```properties
prop.driverClass=com.mysql.jdbc.Drive
prop.url=jdbc:mysql://localhost:3306/userdb
prop.userName=root
prop.password=root
```

在 spring 配置文件中使用 properties 中的属性: `${ keyName }`

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${prop.driverClass}"/>
    <property name="url" value="${prop.url}"/>
    <property name="username" value="${prop.userName}"/>
    <property name="password" value="${prop.password}"/>
</bean>
```

## java 中配置 Bean

在 Spring 中，想要将一个 Bean 注册到 Spring 容器中，整体上来说，有三种不同的方式。

- XML 注入，如前文所说
- Java 配置（通过 Java 代码将 Bean 注册到 Spring 容器中）
- 自动化扫描

Java 配置这种方式在 Spring Boot 出现之前，其实很少使用，自从有了 Spring Boot，Java 配置开发被广泛使用，因为在 Spring Boot 中，不使用一行 XML 配置。

例如我有如下一个 Bean：

```JAVA
public class SayHello {
    public String sayHello(String name) {
        return "hello " + name;
    }
}
```

在 Java 配置中，我们用一个 Java 配置类去代替之前的 applicationContext.xml 文件。

```JAVA
@Configuration
public class JavaConfig {
    @Bean
    SayHello sayHello() {
        return new SayHello();
    }
}
```

首先在配置类上有一个 @Configuration 注解，这个注解表示这个类不是一个普通类，而是一个配置类，它的作用相当于 applicationContext.xml。 然后，定义方法，方法返回对象，方法上添加 @Bean 注解，表示将这个方法的返回值注入的 Spring 容器中去。也就是说，@Bean 所对应的方法，就相当于 applicationContext.xml 中的 bean 节点。

既然是配置类，我们需要在项目启动时加载配置类。

```JAVA
public class Main {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(JavaConfig.class);
        SayHello hello = ctx.getBean(SayHello.class);
        System.out.println(hello.sayHello("javaboy"));
    }
}
```

注意，配置的加载，是使用 AnnotationConfigApplicationContext 来实现。

关于 Java 配置，这里有一个需要注意的问题:Bean 的名字是什么？

Bean 的默认名称是方法名。以上面的案例为例，Bean 的名字是 sayHello。 如果开发者想自定义方法名，也是可以的，直接在 @Bean 注解中进行过配置。如下配置表示修改 Bean 的名字为 javaboy：

```JAVA
@Configuration
public class JavaConfig {
    @Bean("javaboy")
    SayHello sayHello() {
        return new SayHello();
    }
}
```

## 注解开发

在 Spring4 之后，使用注解开发需要导入 aop 包

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>5.3.1</version>
</dependency>
```

同时需要在配置文件中配置注解支持

```XML
<contex:component-scan base-package="....."/>
<contex:annotation-config/>
```

扫描多个包可以是用 `，`隔开

### 配置如何扫描包

如果使用

```xml
<contex:component-scan base-package="....."/>
```

那么 spring 会使用默认 filter 去扫描指定包中的所有内容

如果想配置自己的 filter 可以禁用默认的 filter，此时，就必须指定 filter 来告诉 spring 如何进行扫描

```xml
<context:component-scan base-package="com.autmaple.entity" use-default-filters="false">
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

上述配置告诉 spring 只扫描 `com.autmaple.entity`下带`Controller`注解的内容

```xml
 <context:component-scan base-package="com.autmaple.entity">
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

上述配置告诉 spring 不扫描 `com.autmaple.entity`下带`Controller`注解的内容

### 常用注解

| 注解              | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| @Autowired        | 根据 Type 自动进行注入，目标类中可以没有 setter 方法         |
| @Qualifier        | 配合 Autowired 使用，指定注入的类型。当类型有多个时，就需要指定使用哪一个 |
| @Resource         | 默认根据 Type 自动进行注入，也可以配置根据 Name 进行自动注入。 同时 Resource 注解位于 `javax.annotation`包中 |
| @Value            | 配置基本数据类型的自动注入, 可以注入配置文件中的值           |
| @Scope            | 声明 Bean 的生存方式，singleton 还是 prototype               |
| @Component        | 声明对应类为一个 Bean 对象，交给 Spring 容器管理             |
| @Repository       | 声明 Dao 包中的类为一个 Bean 对象，交给 Spring 容器管理      |
| @Service          | 声明 Service 包中的类为一个 Bean 对象，交给 Spring 容器管理  |
| @Controller       | 声明 Controller 包中的类为一个 Bean 对象，交给 Spring 容器管理 |
| @RestController   | 该注解声明该类是一个直接返回数据的 Controller，该注解是 @ResponseBody 和 @Controller 的组合 |
| @Configuration    | 申明该类是一个配置类                                         |
| @ComponentScan    | 指定扫描那个包下的 Component                                 |
| @Import           | 导入配置类                                                   |
| @Aspect           | 声明标注类是一个切面                                         |
| @Before           | 目标方法执行之前执行                                         |
| @After            | 目标方法执行完毕之后执行，不论其是否报错都会执行             |
| @Around           | 可以在方法执行前后设置相关的信息，即在方法执行之前调用一次，方法执行完毕以后再调用一次 |
| @AfterReturning   | 方法返回之后执行                                             |
| @AfterThrowing    | 目标方法抛出异常时执行                                       |
| @ControllerAdvice | 该注解是 @Controller 注解的一个特殊化，该注解修饰的类中被指定注解修饰的方法可以被**所有的 @Controller 所共享**，并且可以使用 @Order 注解指定多个 @ControllerAdvice 的执行顺序。这些注解包括：@ExceptionHandler, @InitBinder, @ModelAttribute, |

#### @Autowired 注解

该注解既可以放在字段、方法和构造方法中

- 如果注解放在字段上，那么 Spring 在项目启动时自动注入对应类型的实例对象
- 如果注解放在方法上，那么 Spring 在注入时，会调用一次 @Autowired 注解所在的方法，同时如果方法声明了参数，会自动对参数进行装配。这种方式可以用于在自动注入的时候进行一些初始化操作

#### Idea 使用 @Autowired 报 warning 的问题

> Spring Team recommends "Always use constructor based dependency injection in your beans. Always use assertions for mandatory dependencies". 使用构造方法进行依赖注入，使用断言进行强制依赖

@Autowired 注入 bean，相当于在配置文件中配置 bean，并且使用 setter 进行注入

Java 变量初始化的步骤：

静态代码块 -> 构造方法 -> @Autowired

### 注解声明一个bean

使用注解声明一个 bean 可以使用如下注解

- `@Component` ===> 普通的bean
- `@Controller` ===> controller 层的 bean
- `@Service` ===> service 层的 bean
- `@Repository` ==> dao 层的 bean

上述四个注解都可以申明对应的类为一个 bean， 基本上没有差别，这几个注解的设置可以增强代码可可阅读性


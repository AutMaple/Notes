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
JAVA
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
XML
<bean id="user" class="com.autmaple.User" autowire="byType">
    <property name="age" value="10"/>
    <property name="name" value="AutMaple"/>
</bean>


<bean id="primarySchool" class="com.autmaple.PrimarySchool">
    <property name="name" value="金海小学"/>
    <property name="address" value="郴州市苏仙区"/>
</bean>
JAVA
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

同时需要在配置文件中配置注解支持

```XML
<contex:component-scan base-package="....."/>
<contex:annotation-config/>
```

### 常用注解

| 注解            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| @Autowired      | 根据 Type 自动进行注入，目标类中可以没有 setter 方法         |
| @Qualifier      | 配合 Autowired 使用，指定注入的类型                          |
| @Resource       | 默认根据 Name 自动进行注入。也可以配置根据 Type 进行自动注入 |
| @Value          | 配置基本数据类型的自动注入                                   |
| @Scope          | 声明 Bean 的生存方式，singleton 还是 prototype               |
| @Component      | 声明对应类为一个 Bean 对象，交给 Spring 容器管理             |
| @Repository     | 声明 Dao 包中的类为一个 Bean 对象，交给 Spring 容器管理      |
| @Service        | 声明 Service 包中的类为一个 Bean 对象，交给 Spring 容器管理  |
| @Controller     | 声明 Controller 包中的类为一个 Bean 对象，交给 Spring 容器管理 |
| @Configuration  | 申明该类是一个配置类                                         |
| @ComponentScan  | 指定扫描那个包下的 Component                                 |
| @Import         | 导入配置类                                                   |
| @Aspect         | 声明标注类是一个切面                                         |
| @Before         | 目标方法之前执行                                             |
| @After          | 目标方法执行完之后执行，不论其是否报错都会执行               |
| @Around         | 可以在方法执行前后设置相关的信息                             |
| @AfterReturning | 方法返回之后执行                                             |
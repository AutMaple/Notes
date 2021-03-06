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
| @ImportResource                | 该注解引入 XML 文件，用于创建 Bean。指定该注解的 value 的时候需要使用 `classpath:` 前缀 |
| @PostConstruct                 | 表示 Spring Bean 生命周期的一个方法，表示在 Spring 实例化注解所在的 Bean 之后立马执行该注解所修饰的方法 |
| @Transactional                 | 这个注解可以标注在类或者方法上，当它标注在类上时，代表这个类所有公共（public）非静态的方法都将启用事务功能 |

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

![image-20220715171425499](../../Attachment/image-20220715171425499.png)

Bean 的生命周期大致分为 Bean 定义、Bean 的初始化、Bean 的生存期和 Bean 的销毁 4 个部分。其中 Bean 定义过程大致如下：

1. Spring 通过我们的配置，如 @ComponentScan 定义的扫描路径去找到带有 @Component 的类，这个过程就是一个资源定位的过程。
2. 一旦找到了资源，那么它就开始解析，并且将定义的信息保存起来。注意，此时还没有初始化 Bean，也就没有 Bean 的实例，它有的仅仅是 Bean 的定义。
3. 然后就会把 Bean 定义发布到 Spring IoC 容器中。此时，IoC 容器也只有 Bean 的定义，还是没有 Bean 的实例生成。

完成了这 3 步只是一个资源定位并将 Bean 的定义发布到 IoC 容器的过程，还没有 Bean 实例的生成，更没有完成依赖注入。在默认的情况下，Spring 会继续去完成 Bean 的实例化和依赖注入，这样从 IoC 容器中就可以得到一个依赖注入完成的 Bean。但是，有些 Bean 会受到变化因素的影响，这时我们希望取出 Bean 的时候在完成初始化和依赖注入，换句话说就是让那些 Bean 只是将定义发布到 IoC 容器而不做实例化和依赖注入，当我们取出来的时候才做初始化和依赖注入等操作。

![image-20220715174642220](../../Attachment/image-20220715174642220.png)

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

## Spring EL(Expression Languate) Spring 表达式语言

通过 Spring EL 可以拥有更为强大的运算规则来更好地装配 Bean，最常用的就是：

```java
@Value("${database.driverName}")
String driver;
```

`${...}` 表示占位符，它会读取 spring 配置文件(如 application.yaml) 中的值装配到对应的字段中。

除了读取 Spring 配置文件之外，可以进行运算，要进行运算需要使用: `#{...}` 占位符

```java
@Value("#{T(System).currentTimeMillis()}") // `T(...)` 表示引入类
private Long initTime = null;

@Value("#{'使用 Spring EL 赋值字符串'}") 
private String str = null; 

// 科学计数法赋值
@Value("#{9.3E3}") 
private double d; 

// 赋值浮点数
@Value("#{3.14}") 
private float pi;

// 获取其他 Bean 中的属性
@Value("#{beanName.str}") 
private String otherBeanProp = null;

@Value("#{beanName.str?.toUpperCase()}") // ? 表示如果为空就不执行后面的方法
private String otherBeanProp = null;

// 数学运算
@Value("#{1+2}") 
private int run; 

// 浮点数比较运算
@Value("#{beanName.pi == 3.14f}") 
private boolean piFlag; 

// 字符串比较运算
@Value("#{beanName.str eq 'Spring Boot'}") 
private boolean strFlag; 

// 字符串连接
@Value("#{beanName.str + ' 连接字符串'}") 
private String strApp = null; 

// 三元运算
@Value("#{beanName.d > 1000 ? '大于' : '小于'}") 
private String resultDesc = null;
```

## AOP

### AOP 常用注解

| 注解            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| @Aspect         | 声明所修饰的类为一个切面，可在切面中定义各类通知             |
| @Before         |                                                              |
| @After          | 无论是否发生异常，都会执行                                   |
| @AfterReturning |                                                              |
| @AfterThrowing  |                                                              |
| @Pointcut       | 定义切点                                                     |
| @DeclareParents | 它的作用是引入新的类来增强服务，它有两个必须 value 和 defaultImpl |

### AOP 术语和流程

通过约定，可以将一些业务逻辑织入流程中，并且可以将一些通用的逻辑抽取出来，然后**给予默认实现**，这样你只需要完成部分的功能就可以了，这样做可以使得开发者的代码更加简短，同时可维护性也得到提高。

- 连接点（join point）：对应的是具体被拦截的对象，因为 Spring 只能支持方法，所以被拦截的对象往往就是指特定的方法，例如，我们前面提到的 HelloServiceImpl 的 sayHello 方法就是一个连接点，AOP 将通过动态代理技术把它织入对应的流程中。

- 切点（point cut）：有时候，我们的切面不单单应用于单个方法，也可能是多个类的不同方法，这时，可以通过正则式和指示器的规则去定义，从而适配连接点。切点就是提供这样一个功能的概念。

- 通知（advice）：就是按照约定的流程下的方法，分为前置通知（before advice）、后置通知（after advice）、环绕通知（around advice）、事后返回通知（afterReturning advice）和异常通知（afterThrowing advice），它会根据约定织入流程中，需要弄明白它们在流程中的顺序和运行的条件。

- 目标对象（target）：即被代理对象，例如，约定编程中的 HelloServiceImpl 实例就是一个目标对象，它被代理了。

- 引入（introduction）：是指引入新的类和其方法，增强现有 Bean 的功能。

- 织入（weaving）：它是一个通过动态代理技术，为原有服务对象生成代理对象，然后将与切点定义匹配的连接点拦截，并按约定将各类通知织入约定流程的过程。

- 切面（aspect）：是一个可以定义切点、各类通知和引入的内容，Spring AOP 将通过它的信息来增强 Bean 的功能或者将对应的方法织入流程。

AOP 编程的实质是通过动态代理织入切面，并将连接点(被增强的方法)放置在切面的合适位置

![image-20220708110439486](../../Attachment/image-20220708110439486.png)

### 开发切面

@Aspect 注解修饰的类表示一个切面，然后就可以使用各类注解来声明各种通知了。

```java
@Aspect
public class MyAspect { 
    @Before("execution(* com.springboot.chapter4.aspect.service.impl.UserServiceImpl.printUser(..))") 
    public void before() { 
    	System.out.println("before ......"); 
	} 
 
    @After("execution(* com.springboot.chapter4.aspect.service.impl.UserServiceImpl.printUser(..))") 
    public void after() { 
    	System.out.println("after ......"); 
    } 
 

    @AfterReturning("execution(* com.springboot.chapter4.aspect.service.impl.UserServiceImpl.printUser(..))") 
    public void afterReturning() { 
    	System.out.println("afterReturning ......"); 
    } 
    @AfterThrowing("execution(* com.springboot.chapter4.aspect.service.impl.UserServiceImpl.printUser(..))") 
    public void afterThrowing() { 
    	System.out.println("afterThrowing ......"); 
    }
}
```

execution 中的正则表达式就是切点，匹配正则表达式的方法则为连接点。上面代码的注解中，每个注解都使用了同一个正则表达式。为了克服这个问题，Spring 定义了切点（Pointcut）的概念，**切点的作用就是向 Spring 描述哪些类的哪些方法需要启用 AOP 编程**

```java
@Aspect 
public class MyAspect { 
 
 @Pointcut("execution(* com.springboot.chapter4.aspect.service.impl.UserServiceImpl.printUser(..))")
 public void pointCut() { 
 } 
 
 @Before("pointCut()") 
 public void before() { 
 	System.out.println("before ......"); 
 } 
 
 @After("pointCut()") 
 public void after() { 
 	System.out.println("after ......"); 
 } 
 @AfterReturning("pointCut()") 
 public void afterReturning() { 
 	System.out.println("afterReturning ......"); 
 } 
 
 @AfterThrowing("pointCut()") 
 public void afterThrowing() { 
 	System.out.println("afterThrowing ......"); 
 } 
}
```

其中：

- execution 表示在执行的时候，拦截里面的正则匹配的方法；

- `*` 表示任意返回值类型的方法；

- com.springboot.chapter4.aspect.service.impl.UserServiceImpl 指定目标对象的全限定名称；

- printUser 指定目标对象的方法；

- (..)表示任意参数进行匹配。

### 环绕通知

环绕通知（Around）是所有通知中最为强大的通知，强大也意味着难以控制。一般而言，**使用它的场景是在你需要大幅度修改原有目标对象的服务逻辑时**，否则都尽量使用其他的通知。环绕通知是一个取代原有目标对象方法的通知，当然它也提供了回调原有目标对象方法的能力。

```java
@Around("pointCut()") 
public void around(ProceedingJoinPoint jp) throws Throwable { 
    System.out.println("around before......"); 
    // 回调目标对象的原有方法
    jp.proceed();
    System.out.println("around after......"); 
}
```

### @DeclareParens 注解

```java
@Aspect 
public class MyAspect { 
    @DeclareParents(
    value= "com.springboot.chapter4.aspect.service.impl.UserServiceImpl+", 
    defaultImpl=UserValidatorImpl.class)
    public UserValidator userValidator;
    ...... 
}
```

@DeclareParents，它的作用是引入新的类来增强服务，它有两个必须配置的属性 value 和 defaultImpl。 

- value：指向你要增强功能的目标对象，这里是要增强 UserServiceImpl 对象，因此可以看到配置为 com.springboot.chapter4.aspect.service.impl.UserServiceImpl+。 

- defaultImpl：引入增强功能的类，这里配置为 UserValidatorImpl，用来提供校验用户是否为空的功能。

```java
// 定义请求
@RequestMapping("/vp") 
// 返回 JSON 
@ResponseBody 
public User validateAndPrint(Long id, String userName, String note) { 
    User user = new User(); 
    user.setId(id); 
    user.setUsername(userName); 
    user.setNote(note); 
    // 强制转换
    UserValidator userValidator = (UserValidator)userService;
    // 验证用户是否为空
    if (userValidator.validate(user)) { 
    	userService.printUser(user); 
    } 
	return user; 
}
```

### 通知获取参数

给通知传递参数，只需要在切点处加入对应的正则式就可以了。当然，对于非环绕通知还可以使用一个连接点（JoinPoint）类型的参数，通过它也可以获取参数

```java
@Before("pointCut() && args(user)") 
public void beforeParam(JoinPoint point, User user) { 
    Object[] args = point.getArgs(); 
    System.out.println("before ......"); 
}
```

正则式 pointCut() && args(user)中，pointCut()表示启用原来定义切点的规则，并且约定将连接点(目标对象方法)名称为 user 的参数传递进来

JoinPoint 类型的参数对于非环绕通知而言，Spring AOP 会自动地把它传递到通知中；对于环绕通知而言，可以使用 ProceedingJoinPoint 类型的参数

### 多个切面

当指定多个切面时，执行顺序是混乱的，如果要明确执行的顺序，可以使用 Spring 提供的注解 @Order 和接口 Ordered。我们可以使用它们的任意一个指定切面的顺序

#### @order

```java
@Aspect 
@Order(1)
public class MyAspect1 { 
	...... 
}
```

#### Ordered 接口

```java
@Aspect 
public class MyAspect1 implements Ordered { 
// 指定顺序
    @Override 
    public int getOrder() { 
    return 1; 
    }
    
}
```

## 事务

Spring Boot 中通过 @Transactional 注解开启事务

![image-20220708135901074](../../Attachment/image-20220708135901074.png)

### Spring 事务执行步骤

如果都没有发生异常，Spring 数据库拦截器就会帮助我们提交事务，这点也并不需要我们干预。

如果发生异常，就要判断一次事务定义器内的配置，如果事务定义器已经约定了该类型的异常不回滚事务就提交事务，如果没有任何配置或者不是配置不回滚事务的异常，则会回滚事务，并且将异常抛出，这步也是由事务拦截器完成的。

**无论发生异常与否，Spring 都会释放事务资源，这样就可以保证数据库连接池正常可用了，这也是由 Spring 事务拦截器完成的内容。**

### 事务的传播行为

事物的传播行为是方法之间调用事务采取的策略问题。在 Spring 中，当一个方法调用另外一个方法时，可以让事务采取不同的策略工作,如新建事务或者挂起当前事务等，这便是事务的传播行为,例如：

批量任务我们称之为当前方法，那么批量事务就称为当前事务，当它调用单个交易时，称单个交易为子方法，当前方法调用子方法的时候，让每一个子方法不在当前事务中执行，而是创建一个新的事务去执行子方法，我们就说当前方法调用子方法的传播行为为新建事务

#### 传播行为枚举类

```java
package org.springframework.transaction.annotation;
/**** imports ****/
public enum Propagation {
    /**
    * 需要事务，它是默认传播行为，如果当前存在事务，就沿用当前事务，
    * 否则新建一个事务运行子方法
    */
    REQUIRED(TransactionDefinition.PROPAGATION_REQUIRED),
    
    /**
    * 支持事务，如果当前存在事务，就沿用当前事务，
    * 如果不存在，则继续采用无事务的方式运行子方法
    */
    SUPPORTS(TransactionDefinition.PROPAGATION_SUPPORTS),
    
    /**
    * 必须使用事务，如果当前没有事务，则会抛出异常，
    * 如果存在当前事务，就沿用当前事务
    */
    MANDATORY(TransactionDefinition.PROPAGATION_MANDATORY),
    
    /**
    * 无论当前事务是否存在，都会创建新事务运行方法，
    * 这样新事务就可以拥有新的锁和隔离级别等特性，与当前事务相互独立
    */
    REQUIRES_NEW(TransactionDefinition.PROPAGATION_REQUIRES_NEW),
    
    /**
    * 不支持事务，当前存在事务时，将挂起事务，运行方法
    */
    NOT_SUPPORTED(TransactionDefinition.PROPAGATION_NOT_SUPPORTED),
    
    /**
    * 不支持事务，如果当前方法存在事务，则抛出异常，否则继续使用无事务机制运行
    */
    NEVER(TransactionDefinition.PROPAGATION_NEVER),
    
    /**
    * 在当前方法调用子方法时，如果子方法发生异常，
    * 只回滚子方法执行过的 SQL，而不回滚当前方法的事务
    */
    NESTED(TransactionDefinition.PROPAGATION_NESTED);
    private final int value;
    Propagation(int value) { this.value = value; }
    public int value() { return this.value; }
}
```

在大部分的数据库中，一段 SQL 语句中可以设置一个标志位，然后后面的代码执行时如果有异常，只是回滚到这个标志位的数据状态，而不会让这个标志位之前的代码也回滚。这个标志位，在数据库的概念中被称为保存点（save point）。从加粗日志部分可以看到，Spring 为我们生成了 nested 事务，而从其日志信息中可以看到保存点的释放，可见 Spring 也是使用保存点技术来完成让子事务回滚而不致使当前事务回滚的工作。

**注意**，并不是所有的数据库都支持保存点技术，因此 Spring 内部有这样的规则：当数据库支持保存点技术时，就启用保存点技术；如果不能支持，就新建一个事务去运行你的代码，即等价于 REQUIRES_NEW 传播行为。

NESTED 传播行为和 REQUIRES_NEW 还是有区别的。NESTED 传播行为会沿用当前事务的隔离级别和锁等特性，而 REQUIRES_NEW 则可以拥有自己独立的隔离级别和锁等特性，这是在应用中需要注意的地方。

### @Transactional 注解

```java
package org.springframework.transaction.annotation;

@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Transactional {
    // 通过 bean name 指定事务管理器
    @AliasFor("transactionManager") 
    String value() default "";
    
    // 同 value 属性
    @AliasFor("value")
    String transactionManager() default "";
    
    // 指定传播行为
    Propagation propagation() default Propagation.REQUIRED;
    
    // 指定隔离级别
    Isolation isolation() default Isolation.DEFAULT;
    
    // 指定超时时间（单位秒）
    int timeout() default TransactionDefinition.TIMEOUT_DEFAULT;
    
    // 是否只读事务
    boolean readOnly() default false;
    
    // 方法在发生指定异常时回滚，默认是所有异常都回滚
    Class<? extends Throwable>[] rollbackFor() default {};
    
    // 方法在发生指定异常名称时回滚，默认是所有异常都回滚
    String[] rollbackForClassName() default {};
    
    // 方法在发生指定异常时不回滚，默认是所有异常都回滚
    Class<? extends Throwable>[] noRollbackFor() default {};
    
    // 方法在发生指定异常名称时不回滚，默认是所有异常都回滚
    String[] noRollbackForClassName() default {};
}
```

关于注解 @Transactional 值得注意的是它可以放在接口上，也可以放在实现类上。但是 Spring 团队推荐放在实现类上，因为放在接口上将使得你的类基于接口的代理时它才生效。Spring 可以使用 JDK 动态代理，也可以使用 CGLIG 动态代理。如果使用接口，那么你将不能切换为 CGLIB 动态代理，而只能允许你使用 JDK 动态代理，并且使用对应的接口去代理你的类，这样才能驱动这个注解，这将大大地限制你的使用，因此在实现类上使用 @Transactional 注解才是最佳的方式

#### @Transational 自调用失效的问题

Spring 数据库事务的约定，其实现原理是 AOP，而 AOP 的原理是动态代理，在自调用的过程中，是类自身的调用，而不是代理对象去调用，那么就不会产生 AOP，这样 Spring 就不能把你的代码织入到约定的流程中，于是就产生了现在看到的失败场景。此时我们可以从 IOC 容器中取出代理对象来调用对应的方法，从而达到目的。

### 事务管理器

事务的打开、回滚和提交是由事务管理器来完成的。在 Spring 中，事务管理器的顶层接口为 PlatformTransactionManager。当我们引入其他框架时，还会有其他的事务管理器的类，比如引入 Hibernate, 那么那么 Spring orm 包还会提供 HibernateTransactionManager 与之对应并给我们使用; Mybatis 中最常用到的事物管理器就是 DataSourceTransactionManager。

在 Spring Boot 中，当你依赖于 mybatis-spring-boot-starter 之后，它会自动创建一个 DataSourceTransactionManager 对象，作为事务管理器，如果依赖于 spring-boot-starter-data-jpa，则它会自动创建 JpaTransactionManager 对象作为事务管理器，所以我们一般不需要自己创建事务管理器而直接使用它们即可

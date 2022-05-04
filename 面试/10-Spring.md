# Spring

spring 是一款开源的轻量级 Java 开发框架，旨在提高开发人员的开发效率以及系统的可维护性。

> 语言的流行通常需要一个杀手级的应用，Spring 就是 Java 生态的一个杀手级的应用框架。 

Spring 自带 IOC（Inverse Of Control:控制反转） 和 AOP(Aspect-Oriented Programming:面向切面编程)、可以很方便地对数据库进行访问、可以很方便地集成第三方组件（电子邮件，任务，调度，缓存等等）、对单元测试支持比较好、支持 RESTful Java 应用程序的开发。

Spring 最核心的思想就是不重新造轮子，开箱即用！

Spring 提供的核心功能主要是 IOC 和 AOP。学习 Spring ，一定要把 IOC 和 AOP 的核心思想搞懂！

- 从⼤⼩与开销两⽅⾯⽽⾔Spring都是轻量级的。

- IoC 和 AOP 技术能够实现服务之间的松耦合，简化项目的开发和维护
- 提供了⾯向切⾯编程的丰富⽀持，允许通过分离应⽤的业务逻辑与系统级服务进⾏内聚性的开发
- 包含并管理应⽤对象(Bean)的配置和⽣命周期，这个意义上是⼀个容器。
- 将简单的组件配置、组合成为复杂的应⽤，这个意义上是⼀个框架。

**Spring 是一款开源的轻量级 java 开发框架，旨在提高开发人员的开发效率以及系统的可维护性。它自带的 IOC 和 AOP 能够实现服务之间的松耦合，简化项目的开发和维护， Spring 最核心的思想就是不重新造轮子，开箱即用**

## 核心组件

![Spring主要模块](/home/autmaple/Documents/Notes/Attachment/e0c60b4606711fc4a0b6faf03230247a.png)

- Core Container: 核心模块，Spring 其他所有的功能基本都需要依赖于该类库，主要提供 IOC 依赖注入功能的支持。
- Aspects: 该模块为与 AspectJ 的集成提供支持。
- AOP: 提供了面向切面的编程实现。
- Data Access/Integration: 轻松实现 java 程序同数据库进行交互
  - spring-jdbc : 提供了对数据库访问的抽象 JDBC。不同的数据库都有自己独立的 API 用于操作数据库，而 Java 程序只需要和 JDBC API 交互，这样就屏蔽了数据库的影响。
  - spring-tx : 提供对事务的支持。
  - spring-orm : 提供对 Hibernate 等 ORM 框架的支持。
  - spring-oxm : 提供对 Castor 等 OXM 框架的支持。
  - spring-jms : Java 消息
- Web: 编写 Web 应用相关的模块
  - spring-web ：对 Web 功能的实现提供一些最基础的支持。
  - spring-webmvc ：提供对 Spring MVC 的实现。
  - spring-websocket ：提供了对 WebSocket 的支持，WebSocket 可以让客户端和服务端进行双向通信。
  - spring-webflux ：提供对 WebFlux 的支持。WebFlux 是 Spring Framework 5.0 中引入的新的响应式框架。与 Spring MVC 不同，它不需要 Servlet API，是**完全异步.**
- Test: 
  - Spring 团队提倡测试驱动开发（TDD）。有了控制反转 (IoC)的帮助，单元测试和集成测试变得更简单。
  - Spring 的测试模块对 JUnit（单元测试框架）、TestNG（类似 JUnit）、Mockito（主要用来 Mock 对象）、PowerMock（解决 Mockito 的问题比如无法模拟 final, static， private 方法）等等常用的测试框架支持的都比较好。

## IOC 和 AOP

### IOC 控制反转

**IoC（Inversion of Control:控制反转）**是一种设计思想，而不是一个具体的技术实现。IoC 的思想就是将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理。不过， IoC 并非 Spring 特有，在其他语言中也有应用。可以分为两个方面：

- 控制：指的是对象的创建和对象属性(依赖关系)的操作
- 反转：将对象的控制权交给第三方。

控制反转即将对象的控制权交给信赖的第三方去处理，而 Spring 就是一个值得信赖的第三方。我们将对象的创建，属性的赋值都交给它来处理。

- IoC(思想，设计模式)主要的实现方式有两种：依赖查找，**依赖注入**。
- 依赖注入是一种更可取的方式(实现的方式)

![img](/home/autmaple/Documents/Notes/Attachment/frc-365faceb5697f04f31399937c059c162.png)

IoC 容器就像是一个大工厂一样，当我们需要创建一个对象的时候，只需要配置好配置文件/注解即可，完全不用考虑对象是如何被创建出来的。

IOC 容器其实就是一个大工厂，它用来管理我们所有的对象以及依赖关系。

- 原理就是通过 Java 的**反射技术**来实现的！通过反射我们可以获取类的所有信息(成员变量、类名等等等)！
- 再通过配置文件(xml)或者注解来**描述**类与类之间的关系
- 我们就可以通过这些配置信息和反射技术来**构建**出对应的对象和依赖关系了！

在 Spring 中， IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个 Map（key，value），Map 中存放的是各种对象。

Spring 时代我们一般通过 XML 文件来配置 Bean，后来开发人员觉得 XML 文件来配置不太好，于是 SpringBoot 注解配置就慢慢开始流行起来。

#### 使用 IOC 的好处

1. 不用自己组装，拿来就用。
2. 享受单例的好处，效率高，不浪费空间。
3. 便于单元测试，方便切换 mock 组件。
4. 便于进行 AOP 操作，对于使用者是透明的。
5. 统一配置，便于修改。

### AOP 面向切面编程

AOP 编程可以简单理解成：**在执行某些代码前，执行另外的代码**

AOP 的理念：就是将**分散在各个业务逻辑代码中相同的代码通过横向切割的方式**抽取到一个独立的模块中！

AOP(Aspect-Oriented Programming:面向切面编程)能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。

Spring AOP 就是基于动态代理的，如果要代理的对象，实现了某个接口，那么 Spring AOP 会使用 **JDK Proxy**，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候 Spring AOP 会使用 **Cglib** 生成一个被代理对象的子类来作为代理，如下图所示：

![SpringAOPProcess](/home/autmaple/Documents/Notes/Attachment/926dfc549b06d280a37397f9fd49bf9d.jpg)

当然你也可以使用 **AspectJ** ！Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。

# Spring MVC

## 说说自己对于 Spring MVC 了解?

MVC 是模型(Model)、视图(View)、控制器(Controller)的简写，其核心思想是通过将业务逻辑、数据、显示分离来组织代码。

![img](/home/autmaple/Documents/Notes/Attachment/image-20210809181452421.png)

网上有很多人说 MVC 不是设计模式，只是软件设计规范，我个人更倾向于 MVC 同样是众多设计模式中的一种

随着 Spring 轻量级开发框架的流行，Spring 生态圈出现了 Spring MVC 框架， Spring MVC 是当前最优秀的 MVC 框架。相比于 Struts2，Spring MVC 使用更加简单和方便，开发效率更高，并且 Spring MVC 运行速度更快。

MVC 是一种设计模式,Spring MVC 是一款很优秀的 MVC 框架。Spring MVC 可以帮助我们进行更简洁的 Web 层的开发，并且它天生与 Spring 框架集成。Spring MVC 下我们一般把后端项目分为 Service 层（处理业务）、Dao 层（数据库操作）、Entity 层（实体类）、Controller 层(控制层，返回数据给前台页面)。

## SpringMVC 的原理

![img](/home/autmaple/Documents/Notes/Attachment/de6d2b213f112297298f3e223bf08f28.png)

**流程说明（重要）：**

1. 客户端（浏览器）发送请求，直接请求到 `DispatcherServlet`。
2. `DispatcherServlet` 根据请求信息调用 `HandlerMapping`，解析请求对应的 `Handler`。
3. 解析到对应的 `Handler`（也就是我们平常说的 `Controller` 控制器）后，开始由 `HandlerAdapter` 适配器处理。
4. `HandlerAdapter` 会根据 `Handler` 来调用真正的处理器开处理请求，并处理相应的业务逻辑。
5. 处理器处理完业务后，会返回一个 `ModelAndView` 对象，`Model` 是返回的数据对象，`View` 是个逻辑上的 `View`。
6. `ViewResolver` 会根据逻辑 `View` 查找实际的 `View`。
7. `DispaterServlet` 把返回的 `Model` 传给 `View`（视图渲染）。
8. 把 `View` 返回给请求者（浏览器）

# Spring 事务

## Spring 管理事务的方式有几种？

- **编程式事务** ： 在代码中硬编码(不推荐使用) : 通过 `TransactionTemplate` 或者 `TransactionManager` 手动管理事务，实际应用中很少使用，但是对于你理解 Spring 事务管理原理有帮助。
- **声明式事务** ： 在 XML 配置文件中配置或者直接基于注解（推荐使用） : 实际是通过 AOP 实现（基于`@Transactional` 的全注解方式使用最多

## Spring 事务中哪几种事务传播行为?

**事务传播行为是为了解决业务层方法之间互相调用的事务问题**。

当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行。

正确的事务传播行为可能的值如下:

**1.`TransactionDefinition.PROPAGATION_REQUIRED`**

使用的最多的一个事务传播行为，我们平时经常使用的 `@Transactional` 注解默认使用就是这个事务传播行为。如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。

**`2.TransactionDefinition.PROPAGATION_REQUIRES_NEW`**

创建一个新的事务，如果当前存在事务，则把当前事务挂起。也就是说不管外部方法是否开启事务，`Propagation.REQUIRES_NEW` 修饰的内部方法会新开启自己的事务，且开启的事务相互独立，互不干扰。

**3.`TransactionDefinition.PROPAGATION_NESTED`**

如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于 `TransactionDefinition.PROPAGATION_REQUIRED`。

**4.`TransactionDefinition.PROPAGATION_MANDATORY`**

如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。（mandatory：强制性）

这个使用的很少。

若是错误的配置以下 3 种事务传播行为，事务将不会发生回滚：

- **`TransactionDefinition.PROPAGATION_SUPPORTS`**: 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
- **`TransactionDefinition.PROPAGATION_NOT_SUPPORTED`**: 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
- **`TransactionDefinition.PROPAGATION_NEVER`**: 以非事务方式运行，如果当前存在事务，则抛出异常。

推荐阅读文章：[Spring事务传播行为](https://juejin.cn/post/6844903600943022088) --> 该文章讲的非常详细

## Spring 事务中的隔离级别有哪几种?

和事务传播行为这块一样，为了方便使用，Spring 也相应地定义了一个枚举类：`Isolation`

```java
public enum Isolation {

    DEFAULT(TransactionDefinition.ISOLATION_DEFAULT),

    READ_UNCOMMITTED(TransactionDefinition.ISOLATION_READ_UNCOMMITTED),

    READ_COMMITTED(TransactionDefinition.ISOLATION_READ_COMMITTED),

    REPEATABLE_READ(TransactionDefinition.ISOLATION_REPEATABLE_READ),

    SERIALIZABLE(TransactionDefinition.ISOLATION_SERIALIZABLE);

    private final int value;

    Isolation(int value) {
        this.value = value;
    }
    
    public int value() {
        return this.value;
    }
}
```

下面我依次对每一种事务隔离级别进行介绍：

- **`TransactionDefinition.ISOLATION_DEFAULT`** :使用后端数据库默认的隔离级别，MySQL 默认采用的 `REPEATABLE_READ` 隔离级别 Oracle 默认采用的 `READ_COMMITTED` 隔离级别.
- **`TransactionDefinition.ISOLATION_READ_UNCOMMITTED`** :最低的隔离级别，使用这个隔离级别很少，因为它允许读取尚未提交的数据变更，**可能会导致脏读、幻读或不可重复读**
- **`TransactionDefinition.ISOLATION_READ_COMMITTED`** : 允许读取并发事务已经提交的数据，**可以阻止脏读，但是幻读或不可重复读仍有可能发生**
- **`TransactionDefinition.ISOLATION_REPEATABLE_READ`** : 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，**可以阻止脏读和不可重复读，但幻读仍有可能发生。**
- **`TransactionDefinition.ISOLATION_SERIALIZABLE`** : 最高的隔离级别，完全服从 ACID 的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，**该级别可以防止脏读、不可重复读以及幻读**。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

### @Transactional(rollbackFor = Exception.class)注解了解吗？

`Exception` 分为运行时异常 `RuntimeException` 和非运行时异常。事务管理对于企业应用来说是至关重要的，即使出现异常情况，它也可以保证数据的一致性。

当 `@Transactional` 注解作用于类上时，该类的所有 public 方法将都具有该类型的事务属性，同时，我们也可以在方法级别使用该标注来覆盖类级别的定义。如果类或者方法加了这个注解，那么这个类里面的方法抛出异常，就会回滚，数据库里面的数据也会回滚。

在 `@Transactional` 注解中如果不配置 `rollbackFor` 属性,那么事务只会在遇到 `RuntimeException` 的时候才会回滚，加上 `rollbackFor=Exception.class`,可以让事务在遇到非运行时异常时也回滚。

# Spring 面试题

## 什么是 Spring？

Spring 是 java 企业级应用的开源开发框架,旨在提高开发人员的开发效率和系统的维护性

## 什么是 Spring Bean？

### 什么是 Bean

简单来说，Bean 指的是被 IOC 容器管理的对象

我们需要告诉 IoC 容器帮助我们管理哪些对象，这个是通过配置元数据来定义的。配置元数据可以是 XML 文件、注解或者 Java 配置类。

`org.springframework.beans`和 `org.springframework.context` 这两个包是 IoC 实现的基础，如果想要研究 IoC 相关的源码的话，可以去看看

### bean 的作用域有哪些?

Spring 中 Bean 的作用域通常有下面几种：

- **singleton** : 唯一 bean 实例，Spring 中的 bean 默认都是单例的，对单例设计模式的应用。
- **prototype** : 每次请求都会创建一个新的 bean 实例。
- **request** : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP request 内有效。
- **session** : 每一次来自新 session 的 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP session 内有效。
- **global-session** ： 全局 session 作用域，仅仅在基于 portlet 的 web 应用中才有意义，Spring5 已经没有了。Portlet 是能够生成语义代码(例如：HTML)片段的小型 Java Web 插件。它们基于 portlet 容器，可以像 servlet 一样处理 HTTP 请求。但是，与 servlet 不同，每个 portlet 都有不同的会话。

### 单例 bean 的线程安全问题了解吗？

大部分时候我们并没有在项目中使用多线程，所以很少有人会关注这个问题。单例 bean 存在线程问题，主要是因为当多个线程操作同一个对象的时候是存在资源竞争的。

常见的有两种解决办法：

1. 在 bean 中尽量避免定义可变的成员变量。
2. 在类中定义一个 `ThreadLocal` 成员变量，将需要的可变成员变量保存在 `ThreadLocal` 中（推荐的一种方式）。

不过，大部分 bean 实际都是无状态（没有实例变量）的（比如 Dao、Service），这种情况下， bean 是线程安全的。

### @Component 和 @Bean 的区别是什么？

1. `@Component` 注解作用于类，而 `@Bean` 注解作用于方法。
2. `@Component` 通常是通过类路径扫描来自动侦测以及自动装配到 Spring 容器中（我们可以使用 `@ComponentScan` 注解定义要扫描的路径，从中找出标识了要自动装配到 Spring 的 bean 容器中的类）;`@Bean` 注解通常是我们在标有该注解的方法中产生需要的 bean,`@Bean` 告诉了 Spring 该方法的返回值是某个类的实例，这个实例交给 Spring IOC 容器进行管理。
3. `@Bean` 注解比 `@Component` 注解的自定义性更强，而且很多地方我们只能通过 `@Bean` 注解来注册 bean。比如当我们引用第三方库中的类需要装配到 `Spring` 容器时，则只能通过 `@Bean` 来实现。

### @Autowire 和 @Resource 的区别

1. `@Autowire` 和 `@Resource` 都可以用来装配 bean，都可以用于字段或 setter 方法。
2. `@Autowire` 默认按类型装配，默认情况下必须要求依赖对象必须存在，如果要允许 null 值，可以设置它的 required 属性为 false。
3. `@Resource` 默认按名称装配，当找不到与名称匹配的 bean 时才按照类型进行装配。名称可以通过 name 属性指定，如果没有指定 name 属性，当注解写在字段上时，默认取字段名，当注解写在 setter 方法上时，默认取属性名进行装配。

> 注意：如果 name 属性一旦指定，就只会按照名称进行装配。

`@Autowire` 和 `@Qualifier` 配合使用效果和 `@Resource `一样：

```java
@Autowired(required = false)
@Qualifier("example")
private Example example;

@Resource(name = "example")
private Example example;
```

`@Resource` 装配顺序

1. 如果同时指定 name 和 type，则从容器中查找唯一匹配的 bean 装配，找不到则抛出异常；
2. 如果指定 name 属性，则从容器中查找名称匹配的 bean 装配，找不到则抛出异常；
3. 如果指定 type 属性，则从容器中查找类型唯一匹配的 bean 装配，找不到或者找到多个抛出异常；
4. 如果不指定，则自动按照 byName 方式装配，如果没有匹配，则回退一个原始类型进行匹配，如果匹配则自动装配。

### 将一个类声明为 Spring 的 bean 的注解有哪些？

- `@Component` ：通用的注解，可标注任意类为 Spring 的组件。如果一个 Bean 不知道属于哪个层，可以使用 `@Component` 注解标注。
- `@Repository` ：对应持久层即 Dao 层，主要用于数据库相关操作。
- `@Service` ：对应服务层，主要设计一些复杂的逻辑，需要用到 Dao 层。
- `@Controller` ：对应 Spring MVC 控制层，主要用来接受用户请求并调用 Service 层返回数据给前端页面。
- `@Configuration` ：声明该类为一个配置类，可以在此类中声明一个或多个 `@Bean` 方法。

### @Configuration：配置类注解

`@Configuration` 表明在一个类里可以声明一个或多个 `@Bean` 方法，并且可以由 Spring 容器处理，以便在运行时为这些 bean 生成 bean 定义和服务请求

### Bean 的生命周期

- Bean 容器找到配置文件中 Spring Bean 的定义。
- Bean 容器利用 Java Reflection API 创建一个 Bean 的实例。
- 如果涉及到一些属性值 利用 `set()`方法设置一些属性值。
- 如果 Bean 实现了 `BeanNameAware` 接口，调用 `setBeanName()`方法，传入 Bean 的名字。
- 如果 Bean 实现了 `BeanClassLoaderAware` 接口，调用 `setBeanClassLoader()` 方法，传入 `ClassLoader` 对象的实例。
- 如果 Bean 实现了 `BeanFactoryAware` 接口，调用 `setBeanFactory()`方法，传入 `BeanFactory` 对象的实例。
- 与上面的类似，如果实现了其他 `*.Aware` 接口，就调用相应的方法。
- 如果有和加载这个 Bean 的 Spring 容器相关的 `BeanPostProcessor` 对象，执行`postProcessBeforeInitialization()` 方法
- 如果 Bean 实现了 `InitializingBean` 接口，执行 `afterPropertiesSet()` 方法。
- 如果 Bean 在配置文件中的定义包含 init-method 属性，执行指定的方法。
- 如果有和加载这个 Bean 的 Spring 容器相关的 `BeanPostProcessor` 对象，执行`postProcessAfterInitialization()` 方法
- 当要销毁 Bean 的时候，如果 Bean 实现了 `DisposableBean` 接口，执行 `destroy()` 方法。
- 当要销毁 Bean 的时候，如果 Bean 在配置文件中的定义包含 destroy-method 属性，执行指定的方法。

## 解释不同方式的自动装配

- no：默认的方式是不进行自动装配，通过显式设置 ref 属性来进行装配。
- byName：通过参数名自动装配，Spring 容器在配置文件中发现 bean 的 autowire 属性被设置成 byname，之后容器试图匹配、装配和该 bean 的属性具有相同名字的 bean。
- byType:：通过参数类型自动装配，Spring 容器在配置文件中发现 bean 的 autowire 属性被设置成byType，之后容器试图匹配、装配和该 bean 的属性具有相同类型的 bean。如果有多个 bean 符合条件，则抛出错误。
- constructor：这个方式类似于 byType， 但是要提供给构造器参数，如果没有确定的带参数的构造器参数类型，将会抛出异常。
- autodetect：首先尝试使用 constructor 来自动装配，如果无法工作，则使用 byType 方式。

只用注解的方式时，**注解默认是使用byType的**！

## Spring AOP 和 AspectJ 有什么区别

**Spring AOP 属于运行时增强，而 AspectJ 是编译时增强。** Spring AOP 基于代理(Proxying)，而 AspectJ 基于字节码操作(Bytecode Manipulation)。

Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。AspectJ 相比于 Spring AOP 功能更加强大，但是 Spring AOP 相对来说更简单，

如果我们的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择 AspectJ ，它比 Spring AOP 快很多。

## Spring 框架中用到了哪些设计模式？

- **工厂设计模式** : Spring 使用工厂模式通过 `BeanFactory`、`ApplicationContext` 创建 bean 对象。
- **代理设计模式** : Spring AOP 功能的实现。
- **单例设计模式** : Spring 中的 Bean 默认都是单例的。
- **模板方法模式** : Spring 中 `jdbcTemplate`、`hibernateTemplate` 等以 Template 结尾的对数据库操作的类，它们就使用到了模板模式。
- **包装器设计模式** : 我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。
- **观察者模式:** Spring 事件驱动模型就是观察者模式很经典的一个应用。
- **适配器模式** : Spring AOP 的增强或通知(Advice)使用到了适配器模式、spring MVC 中也是用到了适配器模式适配 `Controller`。

## 如何使用 JPA 在数据库中非持久化一个字段？

```java
static String transient1; // not persistent because of static
final String transient2 = "Satish"; // not persistent because of final
transient String transient3; // not persistent because of transient
@Transient
String transient4; // not persistent because of @Transient
```

# SpringBoot

## SpringBoot 自动装配原理

> SpringBoot 定义了一套接口规范，这套规范规定：SpringBoot 在启动时会扫描外部引用 jar 包中的`META-INF/spring.factories`文件，将文件中配置的类型信息加载到 Spring 容器（此处涉及到 JVM 类加载机制与 Spring 的容器知识），并执行类中定义的各种操作。对于外部 jar 来说，只需要按照 SpringBoot 定义的标准，就能将自己的功能装置进 SpringBoot。

自动装配可以简单理解为：**通过注解或者一些简单的配置就能在 Spring Boot 的帮助下实现某块功能。**

### SpringBoot 的核心注解 @SpringBootApplication

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited

@SpringBootConfiguration
@ComponentScan
@EnableAutoConfiguration
public @interface SpringBootApplication {

}

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration //实际上它也是一个配置类
public @interface SpringBootConfiguration {
}
```

大概可以把 `@SpringBootApplication` 看作是 `@Configuration`、`@EnableAutoConfiguration`、`@ComponentScan` 注解的集合。根据 SpringBoot 官网，这三个注解的作用分别是：

- `@EnableAutoConfiguration`：启用 SpringBoot 的自动配置机制
- `@Configuration`：允许在上下文中注册额外的 bean 或导入其他配置类
- `@ComponentScan`： 扫描被 `@Component` (`@Service`,`@Controller`)注解的 bean，**注解默认会扫描启动类所在的包下所有的类 ，可以自定义不扫描某些 bean。**

`@EnableAutoConfiguration` 是实现自动装配的重要注解

### @EnableAutoConfiguration 自动装配的核心注解

`EnableAutoConfiguration` 只是一个简单地注解，自动装配核心功能的实现实际是通过 `AutoConfigurationImportSelector`类。

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage //作用：将 main 包下的所有组件注册到容器中
@Import({AutoConfigurationImportSelector.class}) //加载自动装配类 xxxAutoconfiguration
public @interface EnableAutoConfiguration {
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

    Class<?>[] exclude() default {};

    String[] excludeName() default {};
}
```

### @Import 注解

该注解的作用是将指定的类加载到容器，比如上述注解的意思就是将 AutoConfigurationImportSelector 注入到容器中

### @AutoConfigurationImportSelector: 自动配置中的导入选择器

顾名思义就是选择性的导入配置

`AutoConfigurationImportSelector`类的继承体系如下：

```java
public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware, ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {

}

public interface DeferredImportSelector extends ImportSelector {

}

public interface ImportSelector {
    String[] selectImports(AnnotationMetadata var1);
}
```

可以看出，`AutoConfigurationImportSelector` 类实现了 `ImportSelector `接口，也就实现了这个接口中的 `selectImports`方法，**该方法主要用于获取所有符合条件的类的全限定类名，这些类需要被加载到 IoC 容器中**。

```java
private static final String[] NO_IMPORTS = new String[0];

public String[] selectImports(AnnotationMetadata annotationMetadata) {
        // <1>.判断自动装配开关是否打开
        if (!this.isEnabled(annotationMetadata)) {
            return NO_IMPORTS;
        } else {
          //<2>.获取所有需要装配的bean
            AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader.loadMetadata(this.beanClassLoader);
            AutoConfigurationImportSelector.AutoConfigurationEntry autoConfigurationEntry = this.getAutoConfigurationEntry(autoConfigurationMetadata, annotationMetadata);
            return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
        }
    }
```

这里我们需要重点关注一下 `getAutoConfigurationEntry()` 方法，这个方法主要负责加载自动配置类的。

```java
private static final AutoConfigurationEntry EMPTY_ENTRY = new AutoConfigurationEntry();

AutoConfigurationEntry getAutoConfigurationEntry(AutoConfigurationMetadata autoConfigurationMetadata, AnnotationMetadata annotationMetadata) {
        //<1>.判断自动装配是否打开
        if (!this.isEnabled(annotationMetadata)) {
            return EMPTY_ENTRY;
        } else {
            //<2>.
            AnnotationAttributes attributes = this.getAttributes(annotationMetadata);
            //<3>.
            List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
            //<4>.
            configurations = this.removeDuplicates(configurations);
            Set<String> exclusions = this.getExclusions(annotationMetadata, attributes);
            this.checkExcludedClasses(configurations, exclusions);
            configurations.removeAll(exclusions);
            configurations = this.filter(configurations, autoConfigurationMetadata);
            this.fireAutoConfigurationImportEvents(configurations, exclusions);
            return new AutoConfigurationImportSelector.AutoConfigurationEntry(configurations, exclusions);
        }
    }
```

1. 判断自动装配开关是否打开。默认 `spring.boot.enableautoconfiguration=true`，可在 `application.properties` 或 `application.yml` 中设置

2. 用于获取 `EnableAutoConfiguration` 注解中的 `exclude` 和 `excludeName`。

3. 获取需要自动装配的所有配置类，读取 `META-INF/spring.factories`

```java
spring-boot/spring-boot-project/spring-boot-autoconfigure/src/main/resources/META-INF/spring.factories
```

配置文件中的 `XXXAutoConfiguration` 的作用就是按需加载组件。

不光是这个依赖下的 `META-INF/spring.factories` 被读取到，所有 Spring Boot Starter 下的 `META-INF/spring.factories` 都会被读取到。

所以，你可以清楚滴看到， druid 数据库连接池的 Spring Boot Starter 就创建了 `META-INF/spring.factories`文件。

4. 这一步又经历了一遍筛选，`@ConditionalOnXXX` 中的所有条件都满足，该类才会生效。

   ```java
   @Configuration
   // 检查相关的类：RabbitTemplate 和 Channel是否存在
   // 存在才会加载
   @ConditionalOnClass({ RabbitTemplate.class, Channel.class })
   @EnableConfigurationProperties(RabbitProperties.class)
   @Import(RabbitAnnotationDrivenConfiguration.class)
   public class RabbitAutoConfiguration {
   }
   ```

   有兴趣的童鞋可以详细了解下 Spring Boot 提供的条件注解

   - `@ConditionalOnBean`：当容器里有指定 Bean 的条件下
   - `@ConditionalOnMissingBean`：当容器里没有指定 Bean 的情况下
   - `@ConditionalOnSingleCandidate`：当指定 Bean 在容器中只有一个，或者虽然有多个但是指定首选 Bean
   - `@ConditionalOnClass`：当类路径下有指定类的条件下
   - `@ConditionalOnMissingClass`：当类路径下没有指定类的条件下
   - `@ConditionalOnProperty`：指定的属性是否有指定的值
   - `@ConditionalOnResource`：类路径是否有指定的值
   - `@ConditionalOnExpression`：基于 SpEL 表达式作为判断条件
   - `@ConditionalOnJava`：基于 Java 版本作为判断条件
   - `@ConditionalOnJndi`：在 JNDI 存在的条件下差在指定的位置
   - `@ConditionalOnNotWebApplication`：当前项目不是 Web 项目的条件下
   - `@ConditionalOnWebApplication`：当前项目是 Web 项 目的条件下

## starter 组件命名规则

`SpringBoot` 官方的建议是，如果是我们开发者自己开发的 `starter` 组件（即属于第三方组件），那么命名规范是`{name}-spring-boot-starter`，而如果是 `SpringBoot` 官方自己开发的组件，则命名为 `spring-boot-starter-{name}`。

## SpringBoot 面试题

### SpringBoot支持什么前端模板，

- thymeleaf，freemarker，jsp，官方不推荐 JSP 会有限制

### SpringBoot 实现热部署有哪几种方式？

- 热部署就是可以不用重新运行 SpringBoot 项目可以实现操作后台代码自动更新到以运行的项目中
- 主要有两种方式：
  - Spring Loaded
  - Spring-boot-devtools

### SpringBoot 事务的使用

- SpringBoot 的事务很简单，首先使用注解 EnableTransactionManagement 开启事物之后，然后在Service 方法上添加注解 Transactional 便可。

### Async异步调用方法

- 在 SpringBoot 中使用异步调用是很简单的，只需要在方法上使用 @Async 注解即可实现方法的异步调用。 注意：需要在启动类加入 @EnableAsync 使异步调用 @Async 注解生效。

### 如何在 Spring Boot 启动的时候运行一些特定的代码？

- 可以实现接口 ApplicationRunner 或者 CommandLineRunner，这两个接口实现方式一样，它们都只提供了一个 run 方法

### Spring Boot 有哪几种读取配置的方式？

- Spring Boot 可以通过 @PropertySource, @Value, @Environment, @ConfigurationPropertie 注解来绑定变量

### Java Config

使用纯 java 代码的方式来配置 Spring IOC 容器

常用的 Java config：

- @Configuration：在类上打上写下此注解，表示这个类是配置类
- @ComponentScan：在配置类上添加 @ComponentScan 注解。该注解默认会扫描该类所在的包下所有的配置类，相当于之前的 <context:component-scan >。
- @Bean：bean 的注入：相当于以前的 `<bean id="objectMapper" class="org.codehaus.jackson.map.ObjectMapper"/>`
- @EnableWebMvc：相当于 xml 的 `<mvc:annotation-driven>`
- @ImportResource： 相当于 xml 的 `<import resource="applicationContext-cache.xml">`

### Spring Boot 是否可以使用 XML 配置 ?

- Spring Boot 推荐使用 Java 配置而非 XML 配置，但是 Spring Boot 中也可以使用 XML 配置，通过 @ImportResource 注解可以引入一个 XML 配置。

### spring boot 核心配置文件是什么？bootstrap.properties 和 application.properties 有何区别 ?

- 单纯做 Spring Boot 开发，可能不太容易遇到 bootstrap.properties 配置文件，但是在结合 Spring Cloud 时，这个配置就会经常遇到了，特别是在需要加载一些远程配置文件的时侯。
- spring boot 核心的两个配置文件：
  - bootstrap (. yml 或者 . properties)：boostrap 由父 ApplicationContext 加载的，比 applicaton 优先加载，配置在应用程序上下文的引导阶段生效。一般来说我们在 Spring Cloud 配置就会使用这个文件。且 boostrap 里面的属性不能被覆盖；
  - application (. yml 或者 . properties)： 由ApplicatonContext 加载，用于 spring boot 项目的自动化配置。

### 什么是 Spring Profiles？

Spring Profiles 允许用户根据不同的开发环境加载不同的配置文件

### 比较一下 Spring Security 和 Shiro 各自的优缺点 ?

由于 Spring Boot 官方提供了大量的非常方便的开箱即用的 Starter ，包括 Spring Security 的 Starter ，使得在 Spring Boot 中使用 Spring Security 变得更加容易，甚至只需要添加一个依赖就可以保护所有的接口，所以，如果是 Spring Boot 项目，一般选择 Spring Security 。当然这只是一个建议的组合，单纯从技术上来说，无论怎么组合，都是没有问题的。Shiro 和 Spring Security 相比，主要有如下一些特点：

- Spring Security 是一个重量级的安全管理框架；Shiro 则是一个轻量级的安全管理框架
- Spring Security 概念复杂，配置繁琐；Shiro 概念简单、配置简单
- Spring Security 功能强大；Shiro 功能简单

### Spring Boot 中如何解决跨域问题 ?

跨域可以在前端通过 JSONP 来解决，但是 JSONP 只可以发送 GET 请求，无法发送其他类型的请求，在 RESTful 风格的应用中，就显得非常鸡肋，因此我们推荐在后端通过 （CORS，Cross-origin resource sharing） 来解决跨域问题。这种解决方案并非 Spring Boot 特有的，在传统的 SSM 框架中，就可以通过 CORS 来解决跨域问题，只不过之前我们是在 XML 文件中配置 CORS ，现在可以通过实现 WebMvcConfigurer 接口然后重写 addCorsMappings 方法解决跨域问题。

```java
  @Configuration
  public class CorsConfig implements WebMvcConfigurer {

      @Override
      public void addCorsMappings(CorsRegistry registry) {
          registry.addMapping("/**")
                  .allowedOrigins("*")
                  .allowCredentials(true)
                  .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
                  .maxAge(3600);
      }

  }
```

### 如何使用 Spring Boot 实现全局异常处理？

- Spring 提供了一种使用 ControllerAdvice 处理异常的非常有用的方法。我们通过实现一个 ControlerAdvice 类，来处理控制器类抛出的所有异常。

### SpringBoot性能如何优化

- 如果项目比较大，类比较多，不使用 @SpringBootApplication，采用 @Compoment 指定扫包范围
- 在项目启动时设置 JVM 初始内存和最大内存相同
- 将 springboot 内置服务器由 tomcat 设置为 undertow

### 如何重新加载 Spring Boot 上的更改，而无需重新启动服务器？Spring Boot项目如何热部署？

这可以使用 DEV 工具来实现。通过这种依赖关系，您可以节省任何更改，嵌入式 tomcat 将重新启动。Spring Boot 有一个开发工具（DevTools）模块，它有助于提高开发人员的生产力。Java 开发人员面临的一个主要挑战是将文件更改自动部署到服务器并自动重启服务器。开发人员可以重新加载 Spring Boot 上的更改，而无需重新启动服务器。这将消除每次手动部署更改的需要。Spring Boot 在发布它的第一个版本时没有这个功能。这是开发人员最需要的功能。DevTools 模块完全满足开发人员的需求。该模块将在生产环境中被禁用。它还提供 H2 数据库控制台以更好地测试应用程序。

```xml
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

### SpringBoot 微服务中如何实现 session 共享 ?

- 在微服务中，一个完整的项目被拆分成多个不相同的独立的服务，各个服务独立部署在不同的服务器上，各自的 session 被从物理空间上隔离开了，但是经常，我们需要在不同微服务之间共享 session ，常见的方案就是 Spring Session + Redis 来实现 session 共享。将所有微服务的 session 统一保存在 Redis 上，当各个微服务对 session 有相关的读写操作时，都去操作 Redis 上的 session 。这样就实现了 session 共享，Spring Session 基于 Spring 中的代理过滤器实现，使得 session 的同步操作对开发人员而言是透明的，非常简便。
- cookie 或者 jwt 也能够实现 Session 的共享，但是有 CSRF 的风险

### Spring Boot 中如何实现定时任务 ?

- 在 Spring Boot 中使用定时任务主要有两种不同的方式，一个就是使用 Spring 中的 @Scheduled 注解，另一个则是使用第三方框架 Quartz。

### spring-boot-starter-parent 有什么用 ?

- 我们都知道，新创建一个 Spring Boot 项目，默认都是有 parent 的，这个 parent 就是 spring-boot-starter-parent ，spring-boot-starter-parent 主要有如下作用：
  1. 定义了 Java 编译版本为 1.8 。
  2. 使用 UTF-8 格式编码。
  3. 继承自 spring-boot-dependencies，这个里边定义了依赖的版本，也正是因为继承了这个依赖，所以我们在写依赖时才不需要写版本号。
  4. 执行打包操作的配置。
  5. 自动化的资源过滤。
  6. 自动化的插件配置。
  7. 针对 application.properties 和 application.yml 的资源过滤，包括通过 profile 定义的不同环境的配置文件，例如 application-dev.properties 和 application-dev.yml。
- 总结就是打包用的

### Spring Boot 打成的 jar 和普通的 jar 有什么区别 ?

- Spring Boot 项目最终打包成的 jar 是可执行 jar ，这种 jar 可以直接通过 `java -jar xxx.jar` 命令来运行，这种 jar 不可以作为普通的 jar 被其他项目依赖，即使依赖了也无法使用其中的类。
- Spring Boot 的 jar 无法被其他项目依赖，主要还是他和普通 jar 的结构不同。普通的 jar 包，解压后直接就是包名，包里就是我们的代码，而 Spring Boot 打包成的可执行 jar 解压后，在 `\BOOT-INF\classes` 目录下才是我们的代码，因此无法被直接引用。如果非要引用，可以在 pom.xml 文件中增加配置，将 Spring Boot 项目打包成两个 jar ，一个可执行，一个可引用。


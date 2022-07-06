# @Configuration

@Configuration 注解会告诉 Spring 它所修饰的类是一个配置类，会为 Spring 的应用上下文提供 Bean

## @Bean 注解

@Configuration 注解所修饰的类中，可以使用 @Bean 注解来修饰方法，表明该方法的返回值将作为一个 Bean 注入到 Spring 的应用上下文中。**默认情况下，这些 Bean 的名字与 @Bean 所修饰方法的方法名相同**

```java
@Configuration
public class ServiceConfiguration {
    @Bean
    public InventoryService inventoryService() {
    	return new InventoryService();
    }
    
    @Bean
    public ProductService productService() {
    	return new ProductService(inventoryService());
    }
}
```

它等价的 XML 配置如下

```xml
<bean id="inventoryService" class="com.example.InventoryService" />
<bean id="productService" class="com.example.ProductService">
	<constructor-arg ref="inventoryService" />
</bean>
```

相对于基于 XML 的配置⽅式，基于 Java 的配置会带来多项额外的收益，包括更强的类型安全性以及更好的重构能⼒

**在代码中调用 @Bean 注解修饰的方法，都会被拦截，并返回 Spring 容器中该方法返回对象**

# 自动配置

在 Spring 技术中，⾃动配置起源于所谓的⾃动装配(autowiring) 和组件扫描(component scanning)。借助组件扫描技术，Spring 能够⾃动发现应⽤类路径下的组件，并将它们创建成 Spring 应⽤上下⽂中的 bean。借助⾃动装配技术，Spring 能够⾃动为组件注⼊它们所依赖的其他 bean。

SpringBoot 能够基于类路径中的条⽬、环境变量和其他因素合理猜测需要配置的组件并将它们装配在⼀起。

### @SpringBootApplication

该注解是由三个注解组合而成的一个注解：

- @SpringBootConfiguration: 将该类声明成一个配置类。这个注解实际上是 @Configuration 注解的特殊形式
- @EnableAutoConfiguration: 启用 SpringBoot 的自动配置。这个注解的作用就是告诉 SpringBoot 自动装配项目中可能会用到的 Bean
- @ComponentScan: 启用组件扫描。Spring 会自动将被 @Component, @Service, @Controller, @Repository 等注解修饰的类注入到 Spring 的应用上下文中

## SpringBoot 测试

```java
package tacos;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
public class TacoCloudApplicationTests {
 @Test 
 public void contextLoads() {
 }
}
```

- @RunWith：该注解是 JUnit 的一个注解，该注解会提供一个 Runner, 从而指导 JUnit 使用指定的 Runner 进行测试。这个注解提供了用户自定义测试的功能
- SpringRunner: 这个类是 Spring 提供的测试运行器, 它会创建测试运行所需的 Spring Applicatoin Context. 你可能还见过名为 SpringJUnit4ClassRunner 的测试运行器。SpringRunner 是 SpringJUnit4ClassRunner 测试运行期的别名，实在 Spring 4.3 引入的，引入这个类的目的就是移除对特定 JUnit 版本的关联，提高程序的可阅读性和输入
- @SprintBootTest: 该注解告诉 JUnit 在启动测试的时候要添加上 SpringBoot 的功能

## 组件注入

@Controller, @Service, @Reposiroty, @Component 注解的作用都是让组件扫描将这些注解修饰的类识别为一个 Bean，然后注入到 Spring 的应用上下文中

## Controller 测试

```java
import org.hamcrest.Matchers;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.result.MockMvcResultMatchers;

@RunWith(SpringRunner.class)
@WebMvcTest(HomeController.class)
public class HomeControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @Test
    public void testHomePage() throws Exception {
        mockMvc.perform(MockMvcRequestBuilders.get("/"))
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andExpect(MockMvcResultMatchers.view().name("home"))
                .andExpect(MockMvcResultMatchers.content().string(Matchers.containsString("Welcome to ....")));
    }
}
```

- @WebMvcTest: 该注解是 SpringBoot 所提供的的一个特殊测试注解，该注解的作用是让测试在 SpringMVC 的应用上下文中进行。在这个测试中的意思就是：它会将 HomeController 注册到 SpringMVC 中，这样我们就能够向 HomeController 发送请求了。WebMvcTest 注解会为测试 Spring MVC 应用提供 Spring 环境支持

# Spring Boot DevTools

DevTools 是 Spring 开发人员提供的一个便利的开发工具集，其中有：

- 代码变更后应用自动重启
- 当面向浏览器的资源如:模板，js, html, css 等发生变化时，会自动刷新浏览器
- 自动禁用模板缓存
- 如果使用 H2 数据库的话，内置的 H2 的控制台

## DevTools 工作原理

DevTool 作为项目的一部分，当应用程序运行的时候，应用程序会被两个类加载器加载到 JVM 中，其中一个类加载器会加载我们的 java 代码、属性文件以及资源文件，这些文件都是经常变化。另一个类加载器会加载依赖库，而这些库基本上是不会发生变化的。当 DevTools 检测到变更时，只会重新加载经常变化的那部分文件，并重新启动 Spring 的应用上下文，而那些依赖库则不会重新加载。这种方式可以减少应用的启动时间，同时，如果应用的依赖发生变化则需要我们重新启动应用。

# SpringMVC 请求注解映射

| 注解            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| @RequestMapping | 通用的请求处理                                               |
| @GetMapping     | 处理 HTTP Get 请求                                           |
| @PostMapping    | 处理 HTTP Post 请求                                          |
| @PutMapping     | 处理 HTTP Put 请求                                           |
| @DeleteMapping  | 处理 HTTP Delete 请求                                        |
| @PatchMapping   | 处理 HTTP Patch 请求                                         |
| @ResponseBody   | 表示请求的返回值直接写入响应体中，而不是将数据放入 Model 中，然后再交给 View 进行视图的渲染。 |
| @RestController | 该注解是 @Controller 和 @ResponseBody 注解的组合体           |
| @CrossOrigin    | 配置跨域请求                                                 |
| @PathVariable   | 提取请求路径中的指定的参数赋值给其修饰的方法参数             |
| @RequestBody    | 用于将请求体中的数据封装到该注解所修饰的实体列中             |
| @ResponseStatus | 设置请求成功之后返回的状态码                                 |

处理特定类型请求的注解是在 Spring 4.3 中引入的

**注意：通常在类上使用 @RequestMapping 这个通用的注解来指定请求的基本路径；在方法上使用 @GetMapping 等这些处理具体请求的注解。**

### 处理 HTTP 请求注解的参数

@RequestMapping 等注解有一些参数，用于对请求进行过滤。

#### produces 参数

produces 属性指明所修饰的方法或者类只会处理请求头中 Accept 参数与注解中设置的参数相匹配的请求。Accept 参数用于表明本次请求希望服务端返回的数据格式



```java
@PostMapping(produces="application/json")
@ResponseStatus(HttpStatus.CREATED)
public Taco postTaco(@RequestBody Taco taco) {
	return tacoRepo.save(taco);
}
```

如请求头中传递了 Accept:application/json 参数, 且 Produces 中设置 application/json, 那么 Controller 才会处理对应的请求

该参数可以设置多个

#### consumes 参数

consumes 参数指明所修饰的方法或者类只处理请求头中 ContentType 参数与注解中设置的参数相匹配的请求。ContentType 参数用于告诉服务端本次请求携带的数据的格式。

```java
@PostMapping(consumes="application/json")
@ResponseStatus(HttpStatus.CREATED)
public Taco postTaco(@RequestBody Taco taco) {
	return tacoRepo.save(taco);
}
```

# 参数校验

Spring 支持 Bean 的校验，并且通过 SpringBoot，只需要一个 Spring Boot Web Starter 依赖就可以将实现了 Validation API 的 Hibernate 引入到项目中来，使用示例：

```java
package tacos;
import java.util.List;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;
import lombok.Data;
@Data
public class Taco {
    @NotNull
    @Size(min=5, message="Name must be at least 5 characters long")
    private String name;
    
    @Size(min=1, message="You must choose at least 1 ingredient")
    private List<String> ingredients;
}
```

当将请求中的参数封装成 Taco 对象的时候，如果对应的字段不符合要求，会报错。

参数校验常用的注解：

| 注解              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| @NotNull          | 被修饰的字段不能为 null                                      |
| @Size             | 设置字符串的长度                                             |
| @NotBlank         | 字符串不能为空字符串和 null 值                               |
| @Min              | 设置数字的最小值                                             |
| @Max              | 设置数字的最大值                                             |
| @Email            | 验证字符串的值是一个邮箱                                     |
| @AssertTrue       | 限制被修饰的字段的值为 true                                  |
| @NotEmpty         | 被修饰的字段不能为 null和不能为空，该注解可以修饰 String, Collection, Map, 或者数组类型 |
| @Positive         | 表示数字只能够为正数                                         |
| @PositiveOrZero   | 表示数字只能够为正数和 0                                     |
| @Negative         | 表示数字只能够为负数                                         |
| @Negative         | 表示数字只能够为负数或者为 0                                 |
| @Past             | 验证日期是否过期, 不包括现在                                 |
| @PastOrPresent    | 验证日期是否过期, 包括现在                                   |
| @Future           | 验证日期是否为未来的某个时间，不包括现在                     |
| @FuturePresent    | 验证日期是否为未来的某个时间，包括现在                       |
| @Pattern          | 该注解支持自定义正则表达式进行验证                           |
| @Length           | 验证字符串的长度在 [min, max] 之间                           |
| @Range            | 验证被修饰的元素必须在 [min, max] 之间                       |
| @CreditCardNumber | 验证合法的信用卡号                                           |
| @Digits           | 可以设置最大整数位和最大的小数位                             |
| @Validated        | 表示要对该注解的所修饰的字段进行校验, 但是该注解可以进行分组校验 |
| @Valid            | 表示要对该注解的所修饰的字段进行校验                         |

**所有的注解都包含一个 message 属性, 该属性用于定义校验失败时返回的提示信息**

上面的注解可以用在集合存储的元素上

```java
List<@NotBlank String> preferences; // 添加进 List 的所有元素都会被校验
```

同时这些注解也支持 Java 8 中的 *Optional* 类型：

```java
private LocalDate dateOfBirth;

public Optional<@Past LocalDate> getDateOfBirth() {
    return Optional.of(dateOfBirth);
}
```

这段代码中，验证框架会自动解包 LocalDate 的值并验证其合法性

```java
@PostMapping
public void addAll(
  @RequestBody 
  @NotEmpty(message = "Input movie list cannot be empty.")
  List<@Valid Movie> movies) {
    movieService.addAll(movies);
}
```

如果传入一个空的 Movie List，那么验证将不会通过，同时 @Valid 保证传入的每个 Movie 都会验证其是否为空

```java
@PutMapping("/add")
public String addUserInfo(@Validated UserInfoReq req) {
    log.info("添加用户成功: {}", req.toString());
    return "success";
}
```

@Validated 注解修饰的 UserInfoReq，当请求到达时，会自动验证 UserInfoReq 类中需要验证的字段

## @Validateed VS @Valid

- @Valid：标准 JSR-303 规范的标记型注解，用来标记验证属性和方法返回值，进行级联和递归校验
- @Validated：Spring 的注解，是标准 JSR-303 的一个变种（补充），提供了一个分组功能，可以在入参验证时，根据不同的分组采用不同的验证机制

- @Validated 注解可以用于类级别，用于支持 Spring 进行方法级别的参数校验。@Valid 可以用在属性级别约束，用来表示级联校验。

- @Validated 只能用在类、方法和参数上，而 @Valid 可用于方法、字段、构造器和参数上

在 Controller 中校验方法参数时，使用 @Valid 和 @Validated 并无特殊差异（若不需要分组校验的话）

# Spring Data JPA 注解

| 注解            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| @Entity         | 用在类上，用于表示该类是一个实体类                           |
| @Table          | 用在类上，用于将该类持久化到数据库中对应的表中               |
| @Id             | 声明某个字段为数据库中的 Id 字段                             |
| @GeneratedValue | 用于 Id 的自动生成                                           |
| @Column         | 将类中的字段与表中的字段进行关联                             |
| @PrePersist     | 该注解修饰的方法会在持久化之前调用                           |
| @ManyToMany     | 用于表示多对多的关系，一般用于修饰集合。意思是集合中的元素和 Entity 之间是一个多对多的关系 |
| @ManyToOne      | 设置多对一的关系，所修饰的字段和实体类是一对多的关系, 字段为一，实体类为多 |
| @Transactional  | 开启事务，如果用在类上，那么该类的所有方法都会开启事务，用在方法上则所修饰的方法被执行时开启事务。类和方法都有该注解，方法上的配置优先级更高 |

## Repository 接口

若只是简单的对单表进行 CRUD 操作只需要继承 JpaRepository 接口,传递了两个参数:1.实体类,2.实体类中主键类型

```java
public interface PersonRepository extends JpaRepository<Person, Long> {}
```

继承 `JpaSpecificationExecutor<T>` 接口, 泛型内传入实体类,只要简单实现 `toPredicate` 方法就可以实现复杂的查询

该接口提供了几个方法

```java
public interface JpaSpecificationExecutor<T> {
    T findOne(Specification<T> spec);

    List<T> findAll(Specification<T> spec);

    Page<T> findAll(Specification<T> spec, Pageable pageable);

    List<T> findAll(Specification<T> spec, Sort sort);

    long count(Specification<T> spec);
}
```

方法中的 `Specification` 就是需要我们传进去的参数，它是一个接口, 也是我们实现复杂查询的关键,其中只有一个方法 `toPredicate`

```java
public interface Specification<T> {
    Predicate toPredicate(Root<T> root, CriteriaQuery<?> query, CriteriaBuilder cb);
}
```

### 常用的 Repository 接口

| 接口           | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| CrudRepository | 该接口定义了很多 CRUD 的方法，该类需要两个参数：实体类，实体类的主键 |
|                |                                                              |
|                |                                                              |

## 自定义 Repositroy 接口中的方法

如果官方提供的方法不足以满足我们的要求，我们可以自定义接口中的方法，但是方法的名字不是随便定义的，需要使用官方指定的 DSL 领域特定语言来声明方法名，遵循 spring 以及 JPQL 定义的方法命名，这样 **Spring Data 就能够自动根据方法名来推测用户的行为**，从而在生成 Repository 的实现类时，自动生成对应的 SQL 语句用于执行。

```java
public interface PersonRepository extends JpaRepository<Person, Long> {
    Person findByName(String name);
}
```

命名的机制：find…By, read…By, query…By, count…By 和 get…By。By 后面的字句跟着的就是查询的条件，用于构建条件字句的关键字如下：

1. And----findByLastnameAndFirstname
2. Or----findByLastnameOrFirstname
3. Is,Equals----findByFirstnameIs,findByFirstnameEquals
4. Between----findByStartDateBetween
5. LessThan----findByAgeLessThan
6. LessThanEqual----findByAgeLessThanEqual
7. GreaterThan----findByAgeGreaterThan
8. GreaterThanEqual----findByAgeGreaterThanEqual
9. After----findByStartDateAfter
10. Before----findByStartDateBefore
11. IsNull----findByAgeIsNull
12. IsNotNull,NotNull----findByAge(Is)NotNull
13. Like----findByFirstnameLike
14. NotLike----findByFirstnameNotLike
15. StartingWith----findByFirstnameStartingWith
16. EndingWith----findByFirstnameEndingWith
17. Containing----findByFirstnameContaining
18. OrderBy----findByAgeOrderByLastnameDesc
19. Not----findByLastnameNot
20. In----findByAgeIn(Collection ages)
21. NotIn----findByAgeNotIn(Collection age)
22. TRUE----findByActiveTrue()
23. FALSE----findByActiveFalse()
24. IgnoreCase----findByFirstnameIgnoreCase

# Spring 环境抽象

Spring 的环境抽象是各种配置属性的一站式服务。它抽取了原始的属性，这样需要这些属性的bean就可以从Spring本⾝中获取了。Spring环境会拉取多个属性源，包括：

- JVM 系统属性
- 操作系统环境变量
- 命令行参数
- 应用属性配置文件

Spring 会将这些属性聚合到⼀个源中，通过这个源可以注⼊到 Spring 的 bean 中。下图解释了属性源如何流入 Spring 抽象环境并注入到 Spring Bean 中的

![image-20220705203550422](/home/autmaple/Documents/Notes/Attachment/image-20220705203550422.png)

Spring Boot 自动配置的 bean 都可以通过 Spring 环境提取的属性进行配置

## 服务器端口

```yaml
server:
	port: 0
```

当我们把服务器的端口设置为 0 时，Spring 会任意选择一个端口来启动服务。在我们运行自动化集成测试的时候，这会非常有用，因为这样能够保证并发运行的测试不会与硬编码的端口号冲突。如果不关心应用在哪个端口启动，那么这种配置方式也非常有用，因为此时应用将会变成通过服务注册中心来进行查找的微服务

### 开发 HTTPS 服务器

在开发 HTTPS 服务器的时候，通常将服务器的端口设置为 8443。

```yaml
server:
    port: 8443
    ssl:
        key-store: file:///path/to/mykeys.jks
        key-store-password: letmein
        key-password: letmein
```

key-store 可以通过如下命令生成

```shell
$ keytool -keystore mykeys.jks -genkey -alias tomcat -keyalg RSA
```

## 创建自己的配置属性

SpringBoot 提供 @ConfigurationProperties 注解来支持用户自定义配置属性。只需要将该注解放在类上，并声明对应的前缀，我们就能够在 applicaiton.yaml 文件中进行配置了，例如

```java 
@Controller
@RequestMapping("/orders")
@SessionAttributes("order")
@ConfigurationProperties(prefix="taco.orders")
public class OrderController {
    private int pageSize = 20;
    public void setPageSize(int pageSize) {
    	this.pageSize = pageSize;
    }
    ...
    @GetMapping
    public String ordersForUser(@AuthenticationPrincipal User user, Model model) {
        Pageable pageable = PageRequest.of(0, pageSize);
        model.addAttribute("orders", orderRepo.findByUserOrderByPlacedAtDesc(user, pageable));
        return "orderList";
    }
}
```

这样我们就可以在 application.yaml 文件中通过 taco.orders.pageSize 属性动态的配置页面的大小了, pageSize 的默认属性为 20。

```yamL
taco:
	orders:
		pageSize: 10
```

实际上 @ConfigurationProperties 通常会放到一种特定类型的 bean 中，这种 bean 的作用就是持有配置数据。这样的话，特定的配置细节就能从 Controller 和其他应用程序类中抽离出来，多个bean 也能更容易地共享一些通用的配置

```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;
import lombok.Data;

@Component
@ConfigurationProperties(prefix="taco.orders")
@Data
public class OrderProps {
	private int pageSize = 20;
}
```

这个类还用到了 @Component 注解，这样 Spring 的组件扫描功能会自动发现它并将其创建为 Spring 应用上下文中的 bean 。这是非常重要的，因为我们下一步要将 OrderProps 作为 bean 注入到OrderController 中。

```java
@Controller
@RequestMapping("/orders")
@SessionAttributes("order")
public class OrderController {
    private OrderRepository orderRepo;
    private OrderProps props;
    public OrderController(OrderRepository orderRepo,OrderProps props) {
        this.orderRepo = orderRepo;
        this.props = props;
    }
    ...
    @GetMapping
    public String ordersForUser(@AuthenticationPrincipal User user, Model model) {
        Pageable pageable = PageRequest.of(0, props.getPageSize());
        model.addAttribute("orders",
        orderRepo.findByUserOrderByPlacedAtDesc(user, pageable));
        return "orderList";
    }
    ...
}
```

现在，OrderController 不需要负责处理自己的配置属性了。这样能够让 OrderController 中的代码更加整洁一些，并且能够让其他的 bean 重用 OrderProps 中的属性。除此之外，我们可以将订单相关的属性全部放到一个地方，也就是 OrderProps 类中。如果我们需要添加、删除、重命名或者以其他方式更改其中的属性，我们只需要在 OrderProps 中进行变更就可以了。

# @Profile 注解

该注解可以指定某些类只在特定 profile 配置文件激活的时候才会作为 Bean 注入到 Spring 容器中

```java
@Bean
@Profile("dev")
public CommandLineRunner dataLoader(IngredientRepository repo,
    UserRepository userRepo, PasswordEncoder encoder) {
    ...
}
```

上面的 Bean 只有在 application.yaml 文件中配置了如下属性才会被注入到 Spring 容器中

```yaml
spring:
	profiles:
		active:
			- dev
```

如果一个 Bean 在多个环境下生效，可以进行如下配置

```java
@Bean
@Profile({"dev", "qa"})
public CommandLineRunner dataLoader(IngredientRepository repo,
    UserRepository userRepo, PasswordEncoder encoder) {
    ...
}
```

如果某个类在某个环境下不注入 Spring 容器

```java
@Bean
@Profile({"!dev", "!qa"})
public CommandLineRunner dataLoader(IngredientRepository repo,
    UserRepository userRepo, PasswordEncoder encoder) {
    ...
}
```

在这里，CommandLineRunner（包括 DevelopmentConfig 中定义的其他 bean）只有在 prod 和 qa 均没有激活的情况下才会创建。

# 启用超媒体(Hypermedia)

客户端可能会以硬编码的形式对 `/design/recent` 发送 GET 请求，以便于获取最近创建的 taco。类似的，客户端会以硬编码的形式将 taco 列表中的 ID 拼接到 `/design` 上形成获取特定 taco 资源的 URL

但是这种硬编码的方式非常不灵活，如果后端 API 接口的 URL 发生变化，那么前端这边也需要修改对应的代码，不符合设计模式的规范。

超媒体作为应用状态引擎(Hypermedia as the Engine of Applcation State, HATEOAS) 是一种创建自描述 API 的方式。**API 所返回的资源中会包含相关资源的链接**，客户端只需要了解最少的 API URL 信息就能导航整个 API。这种⽅式能够掌握 API 所提供资源之间的关系，客户端能够基于 API 中的 URL 之间的关系对它们进⾏遍历

举例来说，假设某个客户端想要请求最近设计的taco的列表，按照原始的形式，在没有超链接的情况下，客户端以JSON格式接收到的taco列表会如下所⽰（为了简洁，这⾥只保留了第⼀个taco，剩余的省略了）：

```json
[
 {
     "id": 4,
     "name": "Veg-Out",
     "createdAt": "2018-01-31T20:15:53.219+0000",
     "ingredients": [
         {"id": "FLTO", "name": "Flour Tortilla", "type": "WRAP"},
         {"id": "COTO", "name": "Corn Tortilla", "type": "WRAP"},
         {"id": "TMTO", "name": "Diced Tomatoes", "type": "VEGGIES"},
         {"id": "LETC", "name": "Lettuce", "type": "VEGGIES"},
         {"id": "SLSA", "name": "Salsa", "type": "SAUCE"}
     ]
 },
 ...
]
```

如果客户端想要获取某个 taco 或者对其进⾏其他 HTTP 操作，就需要将它的 id 属性以硬编码的⽅式拼接到⼀个路径为 `/design` 的 URL 上.与之类似，如果客户端想要对某个配料执⾏ HTTP 请求，就需要将该配料 id 属性的值拼接到路径为 `/ingredients` 的 URL 上。在这两种情况下，都需要在路径上添加 `http://` 或 `https://` 前缀以及 API 的主机名。

**如果 API 启⽤了超媒体功能，那么 API 将会描述⾃⼰的 URL，从⽽减轻客户端对其进⾏硬编码的痛苦**

```json
{
  "_embedded": {
    "tacoResourceList": [
      {
        "name": "Veg-Out",
        "createdAt": "2018-01-31T20:15:53.219+0000",
        "ingredients": [
          {
            "name": "Flour Tortilla",
            "type": "WRAP",
            "_links": {
              "self": { "href": "http://localhost:8080/ingredients/FLTO" }
            }
          },
          {
            "name": "Corn Tortilla",
            "type": "WRAP",
            "_links": {
              "self": { "href": "http://localhost:8080/ingredients/COTO" }
            }
          },
          {
            "name": "Diced Tomatoes",
            "type": "VEGGIES",
            "_links": {
              "self": { "href": "http://localhost:8080/ingredients/TMTO" }
            }
          },
          {
            "name": "Lettuce",
            "type": "VEGGIES",
            "_links": {
              "self": { "href": "http://localhost:8080/ingredients/LETC" }
            }
          },
          {
            "name": "Salsa",
            "type": "SAUCE",
            "_links": {
              "self": { "href": "http://localhost:8080/ingredients/SLSA" }
            }
          }
        ],
        "_links": {
          "self": { "href": "http://localhost:8080/design/4" }
        }
      }
    ]
  },
  "_links": {
    "recents": {
      "href": "http://localhost:8080/design/recent"
    }
  }
}
```

这种特殊⻛格的 HATEOAS 被称为 HAL（超⽂本应⽤语⾔，Hypertext Application Language）。这是⼀种在 JSON 响应中嵌⼊超链接的简单通⽤格式。

这个新 taco 列表中的每个元素都包含了⼀个名为 `_links` 的属性，为客户端提供导航 API 的超链接

如果客户端应⽤需要对列表中的 taco 执⾏ HTTP 请求，那么在开发的时候不需要关⼼ taco 资源的 URL 是什么样⼦。相反，它只需要请求“self”链接就可以了，该属性将会映射⾄ `http://localhost:8080/design/4`。如果客户端想要处理特定的配料，只需要查找该配料的 `self` 链接即可。

Spring HATEOAS 项⽬为 Spring 提供了超链接的⽀持。它提供了⼀些类和资源装配器(assembler)，在 Spring MVC 控制器返回资源之前能够为其添加链接。

```xml
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-hateoas</artifactId>
</dependency>
```

Spring HATEOAS 提供了两个主要的类型来表⽰超链接资源：Resource 和 Resources。Resource 代表⼀个资源，⽽ Resources 代表资源的集合。这两种类型都能携带其他资源的链接。当从 Spring MVC REST 控制器返回时，它们所携带的链接将会包含到客户端所接收到的 JSON（或XML）中。使用示例如下：

```java
@GetMapping("/recent")
public Resources<Resource<Taco>> recentTacos() {
    PageRequest page = PageRequest.of(0, 12, Sort.by("createdAt").descending());
    List<Taco> tacos = tacoRepo.findAll(page).getContent();
    Resources<Resource<Taco>> recentResources = Resources.wrap(tacos);
    recentResources.add(new Link("http://localhost:8080/design/recent", "recents"));
    return recentResources;
}
```

## ControllerLinkBuilder

ControllerLinkBuilder 能⾃动探知主机名是什么，这样就能避免对主机名进⾏硬编码。同时，它还提供了流畅的 API，允许我们相对于控制器的基础 URL 构建链接。借助ControllerLinkBuilder，我们可以将recentTacos()中硬编码的Link创建改造成如下的形式：

```java
Resources<Resource<Taco>> recentResources = Resources.wrap(tacos);
recentResources.add(
 ControllerLinkBuilder.linkTo(DesignTacoController.class)
     .slash("recent")
     .withRel("recents"));
```

我们不仅不需要硬编码主机名，⽽且不再需要指定“/design”。在这⾥，我们向 DesignTacoController 请求获取⼀个链接，它的基础路为 `/design` 。ControllerLinkBuilder 使⽤控制器的基础路径作为我们创建的 Link 对象的基础。

是 ControllerLinkBuilder 还有另外⼀个⽅法，**能够消除链接 URL 上的所有硬编码**。此时，我们不再需要调⽤ slash()，⽽是调⽤ linkTo()，并将控制器中的⼀个⽅法传递给它，这样 ControllerLinkBuilder 就能推断出控制器的基础路径和该⽅法的映射路径。如下的代码就以这种⽅式使⽤了linkTo()⽅法：

```java
Resources<Resource<Taco>> recentResources = Resources.wrap(tacos);
recentResources.add(
 linkTo(methodOn(DesignTacoController.class).recentTacos())
 .withRel("recents"));
```

## 资源适配器

参考：Spring 实战5.PDF P276

如果要向返回结果中的列表中的每一个元素添加链接可以将实体类对象转换成一个 ResourceSupport

```java
@GetMapping("/recent")
public Resources<TacoResource> recentTacos() {
    PageRequest page = PageRequest.of(0, 12, Sort.by("createdAt").descending());
    List<Taco> tacos = tacoRepo.findAll(page).getContent();
    List<TacoResource> tacoResources = new TacoResourceAssembler().toResources(tacos);
    Resources<TacoResource> recentResources = new Resources<TacoResource>(tacoResources);
    recentResources.add( linkTo(methodOn(DesignTacoController.class).recentTacos())
   .withRel("recents"));
    return recentResources;
}
```


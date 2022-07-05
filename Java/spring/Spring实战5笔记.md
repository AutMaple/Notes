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

| 注解            | 描述                  |
| --------------- | --------------------- |
| @RequestMapping | 通用的请求处理        |
| @GetMapping     | 处理 HTTP Get 请求    |
| @PostMapping    | 处理 HTTP Post 请求   |
| @PutMapping     | 处理 HTTP Put 请求    |
| @DeleteMapping  | 处理 HTTP Delete 请求 |
| @PatchMapping   | 处理 HTTP Patch 请求  |

处理特定类型请求的注解是在 Spring 4.3 中引入的

**注意：通常在类上使用 @RequestMapping 这个通用的注解来指定请求的基本路径；在方法上使用 @GetMapping 等这些处理具体请求的注解。**

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

所有的注解都包含一个 message 属性, 该属性用于定义校验失败时返回的提示信息

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


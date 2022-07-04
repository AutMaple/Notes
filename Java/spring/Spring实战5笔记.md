# @Configuration

@Configuration 注解会告诉 Spring 它所修饰的类是一个配置类，会为 Spring 的应用上下文提供 Bean

## @Bean 注解

@Configuration 注解所修饰的类中，可以使用 @Bean 注解来修饰方法，表明该方法的返回值将作为一个 Bean 注入到 Spring 的应用上下文中。**默认情况下，这些 Bean 的名字与 @Bean 所修饰方法的方法名相同**

// TODO 弄清楚接下来的代码 Spring 是如何进行注入的

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
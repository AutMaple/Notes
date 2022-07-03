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
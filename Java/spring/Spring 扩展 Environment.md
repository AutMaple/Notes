# 什么是 Environment

Environment 是 Spring 对 profile 和 property 的抽象。在一个项目中，会有不同的开发环境，如开发环境，测试环境，生产环境等，在 Spring 中，这些环境就是 profile。而不同环境，会有不同的配置项，在 Java 中叫做 property, 其实就是 key - value 键值对。

在 Spring 中 Environment  = profile + property

Enviroment 是一个接口，有很多的实现，在 SpringBoot 项目中，使用的实现类是 `ApplicationServletEnvironment`

Spring 使用 `PropertySource` 表示一个配置源(如 app.properties), PropertySource 有很多的子类, 如 PropertiesPropertySource ，SystemEnvironmentPropertySource 等。Environment 中维护了一个 PropertySource 列表. 当用户需要获取一个配置项时，会遍历这些 PropertySource 列表。PropertySource 在列表中的位置代表了优先级，位置越靠前，优先级越高。

如下是 Spring 从 propertySources 中获得 Property 的方式。

```java
protected <T> T getProperty(String key, Class<T> targetValueType, boolean resolveNestedPlaceholders) {
  if (this.propertySources != null) {
    for (PropertySource<?> propertySource : this.propertySources) {

      Object value = propertySource.getProperty(key);
      if (value != null) {
        if (resolveNestedPlaceholders && value instanceof String) {
          value = resolveNestedPlaceholders((String) value);
        }
        logKeyFound(key, propertySource, value);
        return convertValueIfNecessary(value, targetValueType);
      }
    }
  }
  return null;
}
```

# 扩展 Environment

Spring 在启动的时候，会进行上下文环境的准备, Spring 除了加载默认的配置之外，还提供了 `EnvironmentPostProcessor` 接口，方便开发者加载自定义的配置文件到 Spring 的 Environment 中。`EnvironmentPostProcessor` 接口定义如下：

```java
@FunctionalInterface
public interface EnvironmentPostProcessor {

	/**
	 * Post-process the given {@code environment}.
	 * @param environment the environment to post-process
	 * @param application the application to which the environment belongs
	 */
	void postProcessEnvironment(ConfigurableEnvironment environment, SpringApplication application);

}
```

SpringBoot 启动的时候会注册一个名为 `EnvironmentPostProcessorApplicationListener` 监听器，该监听器监听的事件中有 `ApplicationEnvironmentPreparedEvent`, 在这个事件的回调中，会通过 SPI(Service Provider Interface) 方式获取 spring.factories 文件中获取 key 为 `org.springframework.boot.env.EnvironmentPostProcessor` 的值，然后调用对应实现类的 `postProcessEnvironment()` 方法，从而实现自定义配置文件的注入

对于配置文件的解析则是通过 `org.springframework.boot.env.PropertySourceLoader` 接口来实现的。

如果想要将自定义的配置放到 Spring 的 Environment 中，只需要调用 `Environmen#getPropertySources()#addXXX(PropertySource)` 方法即可，例如

```java
public class RabbitEnvironmentPostProcessor implements EnvironmentPostProcessor {
    @Override
    public void postProcessEnvironment(ConfigurableEnvironment environment, SpringApplication application) {
        Properties properties = new Properties();
        PropertiesPropertySource propertySource = new PropertiesPropertySource("rabbitmq", properties);
        environment.getPropertySources().addLast(propertySource);
    }
}
```

在 `META-INF/spring.facotries` 文件中添加如下内容

```text
org.springframework.boot.env.EnvironmentPostProcessor=com.autmaple.user.config.RabbitEnvironmentPostProcessor
```
# 参考文章

- [Spring Security Architecture](https://spring.io/guides/topicals/spring-security-architecture/)

# Authentication and Access Control

authentication (who are you?) and authorization (what are you allowed to do?).

Sometimes people say “access control” instead of "authorization", which can get confusing, but it can be helpful to think of it that way because “authorization” is overloaded in other places.

# Authentication

The main strategy interface for authentication is `AuthenticationManager`, which has only one method:

```java
public interface AuthenticationManager {
  Authentication authenticate(Authentication authentication)
    throws AuthenticationException;
}
```

An `AuthenticationManager` can do one of 3 things in its `authenticate()` method:

- Return an `Authentication` (normally with `authenticated=true`) if it can verify that the input represents a valid principal.如果 authenticate 方法可以证明输入是一个有效的主体，就返回一个 Authentication 对象

- Throw an `AuthenticationException` if it believes that the input represents an invalid principal.
- Return `null` if it cannot decide.

**The most commonly used implementation of `AuthenticationManager` is `ProviderManager`, which delegates to a chain of `AuthenticationProvider` instances**

An `AuthenticationProvider` is a bit like an `AuthenticationManager`, but **it has an extra method to allow the caller to query whether it supports a given `Authentication` type:**

`AuthenticationProvider` 允许调用者去查询它是否支持一个给定的 Authentication 类型

```java
public interface AuthenticationProvider {

	Authentication authenticate(Authentication authentication)
			throws AuthenticationException;

	boolean supports(Class<?> authentication);
}
```

A `ProviderManager` can support multiple different authentication mechanisms in the same application by delegating to a chain of `AuthenticationProviders`. If a `ProviderManager` does not recognize a particular `Authentication` instance type, it is skipped

Sometimes, an application has logical groups of protected resources (for example, all web resources that match a path pattern, such as `/api/**`), and **each group can have its own dedicated `AuthenticationManager`.** Often, each of those is a `ProviderManager`, and **they share a parent. The parent is then a kind of “global” resource, acting as a fallback for all providers.**

ProviderManager 实例共享一个 parent, 这个父类用作全局资源的一种，并作为所有 Provider 的后备资源

![一文带你了解强大的Spring Security 架构原理！ - 知乎](../../../Attachment/v2-19992a59bb9ffbc0b14ed45613139986_1440w-165967043857619.jpg)

# Authentication Managers

SpringSecurity 提供了一些帮助我们快速搭建应用程序认证管理功能的助手。最常用的一个就是：`AuthenticationManagerBuilder`。用它来搭建 in-memory user details，JDBC user details 或者 LDAP user detail 或者是添加一个自定义的 `UserDetailsService` 是非常好的

```Java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    .... // other Configuration
    @Autowired
    public void initialize(AuthenticationManagerBuilder builder, DataSource dataSource) throws Exception {
        builder.jdbcAuthentication()
                .dataSource(dataSource)
                .withUser("dava")
                .password("secret")
                .roles("USER");

    }
}
```

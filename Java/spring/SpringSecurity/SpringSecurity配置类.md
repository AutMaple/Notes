```java
package com.autmaple.sercuringweb;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;

@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception{
        http.authorizeRequests()
                .antMatchers("/", "/home").permitAll() // 表示 / 和 /home 不需要任何的认证
                .anyRequest().authenticated()
                .and()
                .formLogin()
                .loginPage("/login") // /login 表示登录页面路径，该路径不需要任何认证 
                .permitAll()
                .and()
                .logout()
                .permitAll();
    }

    @Bean
    @Override
    public UserDetailsService userDetailsService(){
        UserDetails user =
                User.withDefaultPasswordEncoder()
                        .username("user")
                        .password("password")
                        .roles("USER")
                        .build();

        return new InMemoryUserDetailsManager(user);
    }
}
```

- @EnableWebSecurity: 开启 SpringSecurity 的 Web 安全支持并提供与 SpringMVC 的集成
- configure(HttpSecurity): 用于定义哪些 URL 需要被保护，哪些 URL 不需要被保护
- loginPage()：用于指定登录页面，该页面不需要任何的认证
- userDetailsService(): 提供了一个存放在内存中的单一用户，用户名是 user,密码是 password，角色是：USER

# 配置在内存中的用户

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
         auth
         .inMemoryAuthentication()
         	.withUser("buzz")
         		.password("infinity")
         		.authorities("ROLE_USER")
         .and()
         	.withUser("woody")
         		.password("bullseye")
         		.authorities("ROLE_USER");
    }
}
```

# 配置 JDBC 中存储的用户

```java 
@Autowired
DataSource dataSource;

@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
 auth
 .jdbcAuthentication()
 .dataSource(dataSource);
}
```

该配置就能够进行用户的认证和授权了，但是对于我们的数据库有一定的要求，下面是 SpringSecurity 默认执行的 SQL 查询语句

```java 
public static final String DEF_USERS_BY_USERNAME_QUERY =
 "select username,password,enabled " +
 "from users " +
 "where username = ?";
public static final String DEF_AUTHORITIES_BY_USERNAME_QUERY =
 "select username,authority " +
 "from authorities " +
 "where username = ?";
public static final String DEF_GROUP_AUTHORITIES_BY_USERNAME_QUERY =
 "select g.id, g.group_name, ga.authority " +
 "from groups g, group_members gm, group_authorities ga " +
 "where gm.username = ? " +
 "and g.id = ga.group_id " +
 "and g.id = gm.group_id";
```

从上面的代码中可以看出，SpringSecurity 默认会去查找：users, authorities, groups, group_members, group_authorities 这些表，也就是说如果我们按照 SpringSecurity 去创建表，那么我们不需要其他的配置，就能够获得基本的认证和授权功能

## 配置自定义的认证和授权

当 SpringSecurity 所提供的默认的认证和授权功能不能够满足要求时，就需要我们自定义认证和授权功能了

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
 auth
 .jdbcAuthentication()
 .dataSource(dataSource)
     .usersByUsernameQuery(
         "select username, password, enabled from Users " +
         "where username=?")
     .authoritiesByUsernameQuery(
         "select username, authority from UserAuthorities " +
         "where username=?")
     .passwordEncoder(new StandardPasswordEncoder("53cr3t");
}
```

将默认的SQL查询替换为⾃定义的设计时，很重要的⼀点就是要遵循查询的基本协议。**所有查询都将⽤户名作为唯⼀的参数**。

- 认证查询会选取⽤户名、密码以及启⽤状态信息。

- 权限查询会选取零⾏或多⾏包含该⽤户名及其权限信息的数据。
- 群组权限查询会选取零⾏或多⾏数据，每⾏数据中都会包含群组ID、群组名称以及权限。

passwordEncoder()⽅法可以接受 Spring Security 中 PasswordEncoder 接⼝的任意实现。Spring Security 的加密模块包括了多个这样的实现

- BCryptPasswordEncoder：使⽤ bcrypt 强哈希加密。

- NoOpPasswordEncoder：不进⾏任何转码。

- Pbkdf2PasswordEncoder：使⽤ PBKDF2 加密。

- SCryptPasswordEncoder：使⽤ scrypt 哈希加密。

- StandardPasswordEncoder：使⽤ SHA-256 哈希加密。

## UserDetails 接口

UserDetails 接口是 SpringSecurity 提供的，通过实现 UserDetails 接⼝，我们能够提供更多信息给 SpringSecurity 框架，⽐如⽤户都被授予了哪些权限以及⽤户的账号是否可⽤等。

## UserDetailsService 接口

UserDetailsService 接口是 SpringSecurity 提供的一个 Service 接口。

```java
public interface UserDetailsService {
	UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
}
```

这个接口要么返回一个 UserDetails 对象，要么抛出一个 UsernameNotFoundException 异常

将 UserDetailsService 配置到 SpringSecurity 中

```java
@Autowired
private UserDetailsService userDetailsService;
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
auth
.userDetailsService(userDetailsService);
}
```

# 配置 Web 级别的安全请求

配置 Web 级别的安全需要重写 configure 方法

```java 
@Override
protected void configure(HttpSecurity http) throws Exception {
 ...
}
```

confifigure()⽅法接受⼀个 HttpSecurity 对象，能够⽤来配置在 Web 级别该如何处理安全性。我们可以使⽤ HttpSecurity 配置的功能包括：

- 在为某个请求提供服务之前，需要预先满⾜特定的条件； 

- 配置⾃定义的登录⻚； 

- ⽀持⽤户退出应⽤； 

- 预防跨站请求伪造。

使用示例

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
     http
     .authorizeRequests()
     	.antMatchers("/design", "/orders")
    		.hasRole("ROLE_USER")
     	.antMatchers(“/”, "/**")
         	.permitAll();
}
```

声明的顺序很重要，先声明的优先级更高

### ⽤来定义如何保护路径的配置⽅法

| 方法                       | 描述                                                         |
| -------------------------- | ------------------------------------------------------------ |
| access(String)             | 如果给定的SpEL表达式计算结果为true，就允许访                 |
| anonymous()                | 允许匿名⽤户访问                                             |
| authenticated()            | 允许认证过的⽤户访问                                         |
| denyAll()                  | ⽆条件拒绝所有访问                                           |
| fullyAuthenticated()       | 如果⽤户是完整认证的（不是通过 Remember-me 功能认证的）就允许访问 |
| hasAnyAuthority(String...) | 如果⽤户具备给定权限中的某⼀个，就允许访问                   |
| hasAnyRole(String...)      | 如果⽤户具备给定⾓⾊中的某⼀个，就允许访问                   |
| hasAuthority(String)       | 如果⽤户具备给定权限，就允许访问                             |
| hasIpAddress(String)       | 如果请求来⾃给定IP地址，就允许访问                           |
| hasRole(String)            | 如果⽤户具备给定⾓⾊，就允许访问                             |
| not()                      | 对其他访问⽅法的结果求反                                     |
| permitAll()                | ⽆条件允许访问                                               |
| rememberMe()               | 如果⽤户是通过 Remember-me 功能认证的，就允许访问            |

可以使⽤ access() ⽅法，通过为其提供 SPEL表达式来声明更丰富的安全规则

## CSRF 跨域请求伪造

为了防⽌这种类型的攻击，应⽤可以在展现表单的时候⽣成⼀个 CSRF token，**并放到隐藏域中，然后将其临时存储起来**，以便后续在服务器上使⽤。在提交表单的时候，token 将和其他的表单数据⼀起发送⾄服务器端。请求会被服务器拦截，并与最初⽣成的 token 进⾏对⽐。如果 token 匹配，那么请求将会允许处理；否则，表单肯定是由恶意⽹站渲染的，因为它不知道服务器所⽣成的 token。 




















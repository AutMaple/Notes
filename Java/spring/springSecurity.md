# SpringSecurity 的使用

## SpringSecurity 的起源

Spring Security 是 Spring 家族中的一个安全管理框架，实际上，在 Spring Boot 出现之前，Spring Security 就已经发展了多年了，但是使用的并不多，安全管理这个领域，一直是 Shiro 的天下。

相对于 Shiro，在 SSM/SSH 中整合 Spring Security 都是比较麻烦的操作，所以，Spring Security 虽然功能比 Shiro 强大，但是使用反而没有 Shiro 多（Shiro 虽然功能没有 Spring Security 多，但是对于大部分项目而言，Shiro 也够用了）。

自从有了 Spring Boot 之后，Spring Boot 对于 Spring Security 提供了 自动化配置方案，可以零配置使用 Spring Security。

因此，一般来说，常见的安全管理技术栈的组合是这样的：

- SSM + Shiro
- Spring Boot/Spring Cloud + Spring Security

**注意，这只是一个推荐的组合而已，如果单纯从技术上来说，无论怎么组合，都是可以运行的。**

## SpringSecurity 是什么

SpringSecurity 是一个强大的可高度定制的**认证和授权**框架，对于 Spring 应用来说它是一套 Web 安全标准

SpringSecurity 注重于为 Java 应用提供认证和授权功能，像所有的 Spring 项目一样，它对自定义需求具有强大的扩展性。

## SpringBoot 中整合 SpringSecurity

添加依赖

```xml
<!--SpringSecurity依赖配置-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

只要项目中引入了上述的依赖，项目中所有的接口都会自动的被保护起来

重新启动项目，然后再去访问某一个路径，会自动跳转到 `/login` 这个路径，并且默认的用户名是 user， 密码则是在控制台中输出，是一个 uuid。即使项目的 Controller 中没有设置这个路径，依然会进行跳转并访问

### 自定义用户名和密码

自定义用户名和密码有三种方式

- 在配置文件中配置
- 在内存中配置
- 通过数据库中数据进行创建

1. 在配置文件中进行配置

自定义用户名和密码可以在 application.yaml 文件中配置

```yaml
spring:
  security:
    user:
      name: autmaple
      password: 111111

```

2. 使用内存中的用户信息

使用内存中的用户信息需要使用 WebSecurityConfigurerAdapter 这个适配器，SpringSecurity 配置 Web 相关的配置可以通过继承WebSecurityConfigurerAdapter 这个适配器来进行配置

自定义的认证信息需要重写其中的 `protected void configure(AuthenticationManagerBuilder auth)` 这个方法

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication().withUser("autmaple").password("111111").roles();
        auth.inMemoryAuthentication().withUser("irvin").password("111111").roles();
        auth.inMemoryAuthentication().withUser("kobe").password("111111").roles();
        auth.inMemoryAuthentication().withUser("admin").password("admin").roles();
    }
}

```

可以一次性设置多个用户名和密码, 此时项目可以正常的启动，但是不能够正常的登录

#### 密码加密

在 SpringSecurity5 版本中要求密码必须加密，否则当你输入用户名和密码时，登录不了，并在控制台报错： `There is no PasswordEncoder mapped for the id "null"`, 要解决这个问题需要创建一个实现了 PasswordEncode 接口的加密类，SpringSecurity 默认实现了一些类，可以直接使用, 不同的实现类使用不同的加密方法，可以自由的进行选择,通常使用 BCryptPasswordEncoder 这个类来对密码进行加密

![image-20220101132910469](/home/autmaple/.config/Typora/typora-user-images/image-20220101132910469.png)



```java
@Configuration
@EnableWebSecurity // 表示启用 SpringSecurity 安全框架的功能
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private PasswordEncoder pe;
    
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // PasswordEncoder pe = passwordEncoder();
        
        auth.inMemoryAuthentication()
                .withUser("autmaple")
                .password(pe.encode("111111"))
                .roles();
        auth.inMemoryAuthentication()
                .withUser("irvin")
                .password(pe.encode("111111"))
                .roles();
        auth.inMemoryAuthentication()
                .withUser("kobe")
                .password(pe.encode("111111"))
                .roles();
        auth.inMemoryAuthentication()
                .withUser("admin")
                .password(pe.encode("111111"))
                .roles();
    }

    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
}

```

#### 设置角色信息

基于角色 role 的身份认证，同一个用户可以担任不同的角色

```java
package com.autmaple.security.configure;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;


@Configuration
@EnableWebSecurity
// 表示启用方法级别的角色认证，prePostEnabled = true 表示可以使用 @PreAuthorize 和 @PostAuthorize
// @PreAuthorize ==> 表示在访问方法之前进行角色认证
// @PostAuthorize ==> 表示在访问方法之后进行角色认证
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private PasswordEncoder pe;
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // PasswordEncoder pe = passwordEncoder();

        auth.inMemoryAuthentication()
                .withUser("autmaple")
                .password(pe.encode("111111"))
                .roles("admin","normal");
        auth.inMemoryAuthentication()
                .withUser("irvin")
                .password(pe.encode("111111"))
                .roles("normal");
        auth.inMemoryAuthentication()
                .withUser("kobe")
                .password(pe.encode("111111"))
                .roles("normal");
        auth.inMemoryAuthentication()
                .withUser("admin")
                .password(pe.encode("111111"))
                .roles("normal", "admin");
    }

    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
}

```

```java
package com.autmaple.security.controller;

import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @RequestMapping("/hello")
    @PreAuthorize(value = "hasAnyRole('admin', 'normal')") // 在访问该方法之前进行角色的认证
    public String hello(){
        return "admin and normal";
    }

    @RequestMapping("/admin")
    @PreAuthorize(value = "hasAnyRole('admin')")
    public String admin(){
        return "admin";
    }
}
```

3. 根据数据库中信息创建用户

在 SpringSecurity 框架中，用户信息的表示定义在 UserDetails 接口中，这个接口高度抽象了用户的信息

创建用户的步骤如下：

1. 实现 UserDetails 接口，SpringSecurity 中提供了一个实现类：User, 因此可以直接拿来用
2. 实现 UserDetailsService 接口，这个接口中定义了一个方法：`UserDetails loadUserByUsername(String username)`, 通过实现这个方法从数据库中获取数据并将数据封装到 User 对象中然会返回 User 对象, 其中的 username 为前端传递过来的数据

### 关闭验证功能

在开发阶段如果觉得每次启动都需要验证比较麻烦，可以选择将其关闭, 但是不建议这么做

```java
@SpringBootApplication(exclude = {SecurityAutoConfiguration.class})
public class SecurityApplication {
    public static void main(String[] args) {
        ConfigurableApplicationContext run = SpringApplication.run(SecurityApplication.class, args);
        System.out.println("1111");
    }
}
```

## 登录认证的流程

1. 将传递过来的用户名和密码和数据库的用户名和密码进行比对
2. 如果匹配成功，则生成一个 Jwt（Json Web token）字符串返回给用户,同时将 jwt token 加入到 redis 中，提升访问速度
3. 登录之后的所有请求都需要携带 jwt 才能够对网站进行正常的访问
4. 服务器根据 jwt 获取相关的信息从而进行相关的授权操作 => 即授予用户哪些权限，用户可以访问哪些资源
5. 将用户请求的可访问的资源返回给用户

### 具体实现

#### 登录

1. 自定义登录接口

   调用 ProviderManager 的方法进行认证，如果认证通过，则生成 jwt, 并将用户信息存入 redis 中

2. 自定义 UserDetailsService

   在这个接口中实现查询数据库

### 校验

1. 定义 jwt 认证过滤器

   获取并解析 token，根据 token 从 redis 中获取对应用户的信息，并将用户信息存入 SecurityContextHolder

## SpringSecurity 的完整流程

SpringSecurity 的原理就是一个过滤器链，其内部提供各种功能的过滤器,常用的过滤器：

- UsernamePasswordAuthenticationFilter: 负责处理在登录界面发送过来的登录请求,登录中的认证操作主要是由这个过滤器完成
- ExceptionTranslationFilter: 处理过滤链中抛出的任何 AccessDeniedException 和 AuthenticationException
- FilterSecurityInterceptor: 负责权限校验的过滤器

### 查看 SpringSecurity 提供的过滤器

1. 打断点

```java
@SpringBootApplication
public class SecurityApplication {
    public static void main(String[] args) {
        ConfigurableApplicationContext run = SpringApplication.run(SecurityApplication.class, args);
        System.out.println("1111");
    }
}
```

<img src="/home/autmaple/.config/Typora/typora-user-images/image-20211231214704207.png" alt="image-20211231214704207" style="zoom: 67%;" />

2. 点击箭头指向的按钮，执行相应的指令：run.getBean(DefaultSecurityFilterChain.class)

<img src="/home/autmaple/.config/Typora/typora-user-images/image-20211231221413590.png" alt="image-20211231221413590" style="zoom:67%;" />

3. 得到的结果如下

<img src="/home/autmaple/.config/Typora/typora-user-images/image-20211231221659439.png" alt="image-20211231221659439" style="zoom:67%;" />

## 常用的一些接口

1. Authentication 接口：它的实现类表示当前访问系统的用户，封装了用户的相关信息
2. AuthenticationManager 接口： 定义了认证 Authentication 的方法
3. UserDetailsService 接口：加载用户特定数据的核心接口，里面定义了根据用户名查询用户信息的方法

4. UserDetails 接口： 提供核心用户信息。通过 UserDetailService 接口并根据用户名获取到处理过的用户信息之后要将其封装成 UserDetails 对象,然后将这些信息封裝到 Authentication 对象中

## 授权

SpringSecuriy 中使用 RBAC(Role-Based Access Control) 基于角色的访问控制， 在 RBAC 中，权限与角色相关联，用户通过成为适当的角色而得到对应角色的权限，这就极大的简化了权限的管理。这样管理都是层级相互依赖的，权限赋予给角色，而将角色赋予给用户，这样的权限设计很清楚，管理起来很方便

其基本思想就是，对系统操作的各种权限不是直接赋予给用户，而是在用户集合与权限集合之间建立一个角色集合，每一种角色对应一组相应的权限，一旦用户被分配了适当的角色后，该用户就拥有此角色的所有操作权限。这样做的好处是，不必在每次创建用户时都进行分配权限的操作，只要分配用户相应的角色即可，而且角色的权限的变更比用户权限的变更要少的多，这样就简化用户的权限管理，减少系统的开销

### 基于 RBAC 设计表

- 用户表：用于用户认证， 即用户登录
- 角色表：定义角色信息，如角色的名称，角色的描述
- 用户和角色的关系：用户和角色的关系可以是多对多的关系 => 一个用户可以有多个角色，一个角色对应多个用户
- 权限表：定义角色和权限的关系，角色可以有哪些权限

## SpringSecurity 中认证的类和接口

UserDetails 接口：表示用户的信息，可以自定义来实现着接口，从而作为这个系统的用户，这个类可以交给 SpringSecurity 来使用

UserDetailsService 接口：实现这个接口可以根据前端传递过来的 username 从数据库中查询信息并创建相应的用户对象，一般都需要实现, 最后在配置 SpringSecurity 时，需要告诉 SpringSecurity 使用哪一个 UserDetailService 来做用户的认证工作

## 自定义登录页面,表单参数，失败页面

```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .and()
                .formLogin()
                .usernameParameter("...")
                .passwordParameter("...")
                .loginPage("...")
                .loginProcessingUrl("...")
                .failureUrl("...")
                
    }
```

## SpringSecurity 处理登录流程

1. SpringSecurity 提取前端表单中的 username 和 password 字段，封装到 Authentication 接口的实现类：UsernamePasswordAuthenticationToken 对象中
2. 将 UsernamePasswordAuthenticationToken 对象传给 AuthenticationMananger 的实现类 ProviderManager 进行验证；
3. ProviderManager 在一条链上依次调用 AuthenticationProvider 进行验证；它是如何确定使用哪一个具体的 Provider 来进行验证的呢？答案是通过判断 AuthenticationToken 的实现类的类型来判断和对应方法的返回的结果来判断,且只要有一个 Provider 验证成功就不再继续验证
4. 验证成功则返回一个封装了权限信息的 Authentication对 象（即对象的Collection<? extends GrantedAuthority>属性被赋值）；
5. 将此对象放入安全上下文 SecurityContext中；
6. 需要时，可以将 Authentication 对象从 SecurityContextHolder 上下文中取出。

## UserDetailsService 接口

UserDetailService 接口作用是允许我们自定义用户的认证(查询数据库来判读用户是否是合法用户，登录是否有效)，如果合法则返回用户的详细信息，即返回一个 UserDetail 接口的实现类，这个类可以自定义实现，也可以使用 SpringSecurity 实现好的一个类：User

该接口只有一个方法需要实现

```java
public interface UserDetailsService {
    UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
}
```

实现类可以在这个方法中查询数据库并将用户信息封装成 UserDetails 类型的对象

## UserDetails 接口

这个接口规定了 SpringSecurity 的用户对象必须拥有的一些方法, 从而规定了用户对象必须要有的属性，属性名可以自定义，但是对应的意思必须与 SpringSecurity 方法规定的意思相同

## 认证过程中使用到的一些接口

- Authentication 接口：认证的接口，接口实现类中封装前端传递过来的用户名和密码,如果验证成功，则 Authentication 就会被放入 SecurityContextHolder 中, 在验证未过期的时间段内一直有效

- AuthenticatoinManager 接口：验证管理类的总接口，常用的实现类是 ProviderMananger，这个实现类中写好了验证的链条（需要经过哪些 AuthenticationProvider 接口的实现类) ， AuthenticationPorvider 提供具体的验证工作(对用户名和密码进行校验)
- AuthenticationProvider 接口： 进行具体验证的接口，其有一个实现类 DaoAuthenticationProvider 提供与数据库中的信息比对的服务

## 使用数据库中的数据进行认证

要使用数据库中的数据对用户进行认证，需要使用 DaoAuthenticationProvider。在 SpringSecurity 中可以直接通过 SpringSecurity 的配置类直接进行配置

```java
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        ...省略
        //加入数据库验证类，下面的语句实际上在验证链中加入了一个DaoAuthenticationProvider
        auth.userDetailsService(myUserDetailsService).passwordEncoder(new BCryptPasswordEncoder());
    }
```

## SpringSecurity 的配置类

```java
package com.autmaple.mall.config;

import com.autmaple.mall.component.JwtAuthenticationTokenFilter;
import com.autmaple.mall.component.RestAuthenticationEntryPoint;
import com.autmaple.mall.component.RestfulAccessDeniedHandler;
import com.autmaple.mall.dto.AdminUserDetails;
import com.autmaple.mall.mbg.model.UmsAdmin;
import com.autmaple.mall.mbg.model.UmsPermission;
import com.autmaple.mall.service.UmsAdminService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

import java.util.List;

@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private UmsAdminService umsAdminService;

    @Autowired
    private RestfulAccessDeniedHandler restfulAccessDeniedHandler;

    @Autowired
    private RestAuthenticationEntryPoint restAuthenticationEntryPoint;


    @Override
    protected void configure(HttpSecurity httpSecurity) throws Exception {
        httpSecurity.csrf().disable()// 由于使用的 JWT， 这里不需要 csrf
                .sessionManagement()// 基于 token， 所有不用 session
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                .antMatchers(HttpMethod.GET, // 允许对静态资源的无授权访问
                        "/",
                        "/*.html",
                        "/favicon.ico",
                        "/**/*.html",
                        "/**/*.css",
                        "/**/*.js",
                        "/swagger-resources/**",
                        "/v2/api-docs/**"
                )
                .permitAll()
                .antMatchers("/admin/login","/admin/register")// 对登录注册允许匿名访问
                .permitAll()
                .antMatchers(HttpMethod.OPTIONS)// 跨域请求先进行一个 options 请求
                .permitAll()
                // .antMatchers("/**")// 测试时全部都可以访问
                // .permitAll()
                .anyRequest()// 除了上面的请求外全部都需要鉴权认证
                .authenticated();

        // 禁用缓存
        httpSecurity.headers().cacheControl();

        // 添加 JWT filter
        httpSecurity.addFilterBefore(jwtAuthenticationTokenFilter(), UsernamePasswordAuthenticationFilter.class);

        // 添加自定义未授权和未登录用户的返回
        httpSecurity.exceptionHandling()
                .accessDeniedHandler(restfulAccessDeniedHandler) // 当用户访问没有权限的资源时，SpringSecurity 会使用该处理器来处里
                .authenticationEntryPoint(restAuthenticationEntryPoint); // 当用户没有认证或者是认证失败时，交给该处理器处理
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 配置 DaoAuthenticationProvider,用于使用数据库中的数据进行认证和授权工作
        auth.userDetailsService(userDetailsService())
                .passwordEncoder(passwordEncoder());
    }

    @Bean
    public JwtAuthenticationTokenFilter jwtAuthenticationTokenFilter() {
        return new JwtAuthenticationTokenFilter();
    }


    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

    @Bean
    public UserDetailsService userDetailsService() {
        return username -> {
            UmsAdmin admin = umsAdminService.getAdminByUsername(username);
            if(admin != null){
                List<UmsPermission> permissionList = umsAdminService.getPermissionList(admin.getId());
                return new AdminUserDetails(admin, permissionList);
            }
            throw new UsernameNotFoundException("用户名或密码错误");
        };
    }
}
```

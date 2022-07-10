# SpringSecurity 的使用

在 Java Web 工程中，一般使用 Servlet 过滤器（Filter）对请求进行拦截，然后在 Filter 中通过自己的 验证逻辑来 决定是否放 行请求。同 样地， Spring Security 也 是基于这个 原理，在进入到 DispatcherServlet 前就可以对 Spring MVC 的请求进行拦截，然后通过一定的验证，从而决定是否放行请求访问系统。

为了对请求进行拦截，Spring Security 提供了过滤器 DelegatingFilterProxy 类给予开发者配置。

在 Web 工程中可以使用 @EnableWebSecurity 来驱动 Spring Security 的启动，如果属于非 Web 工程，可以使用 @EnableGlobalAuthentication ，而事实上 @EnableWebSecurity 上已经标注了 @EnableGlobalAuthentication 并且依据自己的需要加入了许多 Web 的特性。

## SpringSecurity 的原理

一旦启用了 Spring Security，Spring IoC 容器就会为你创建一个名称为 springSecurityFilterChain 的 Spring Bean。它的类型为 FilterChainProxy，事实上它也实现了 Filter 接口，只是它是一个特殊的拦截器。在 Spring Security 操作的过程中它会提供 Servlet 过滤器 DelegatingFilterProxy，这个过滤器会通过 Spring Web IoC 容器去获取 Spring Security
所自动创建的 FilterChainProxy 对象，这个对象上存在一个拦截器列表（List），列表上存在用户验证的拦截器、跨站点请求伪造等拦截器，这样它就可以提供多种拦截功能。于是焦点又落到了
FilterChainProxy 对象上，通过它还可以注册 Filter，也就是允许注册自定义的 Filter 来实现对应的拦截逻辑，以满足不同的需要。当然，Spring Security 也实现了大部分常用的安全功能，并提供了相应的机制来简化开发者的工作，所以大部分情况下并不需要自定义开发，使用它提供的机制即可。

## 自定义配置

为了给 FilterChainProxy 对象加入自定义的初始化，SpringSecurity 提供了 SecurityConfigurer 接口，通过它就能够实现对 Spring Security 的配置。只是有了这个接口还不太方便，因为它只是能够提供接口定义的功能，为了更方便，Spring 对 Web 工程还提供了专门的接口 WebSecurityConfigurer，并且在这个接口的定义上提供了一个抽象类 WebSecurityConfigurerAdapter。开发者通过继承它就能得到 Spring Security 默认的安全功能。也可以通过覆盖它提供的方法，来自定义自己的安全拦截方案。这里需要研究 WebSecurityConfigurerAdapter 中默认存在的 3 个方法:

```java
/**
* 用来配置用户签名服务，主要是 user-details 机制，你还可以给予用户赋予角色
* @param auth 签名管理器构造器,用于构建用户具体权限控制
*/
protected void configure(AuthenticationManagerBuilder auth);
/**
* 用来配置 Filter 链
*@param web Spring Web Security 对象
*/
public void configure(WebSecurity web);
/**
* 用来配置拦截保护的请求，比如什么请求放行，什么请求需要验证
* @param http http 安全请求对象
*/
protected void configure(HttpSecurity http) throws Exception;
```

- WebSecurity 参数的方法主要是配置 Filter 链的内容，可以配置 Filter 链忽略哪些内容。WebSecurityConfigurerAdapter 提供的是空实现，也就是没有任何的配置。

- AuthenticationManagerBuilder 参数的方法，则是定义用户（user）、密码（password）和角色（role），在默认的情况下 Spring 不会为你创建任何的用户和密码，也就是有登录页面而没有可登录的用户。
- HttpSecurity 参数的方法，则是指定用户和角色与对应 URL 的访问权限，也就是开发者可以通过覆盖这个方法来指定用户或者角色的访问权限。

在 WebSecurityConfigurerAdapter 提供的验证方式下满足通过用户验证或者 HTTP 基本验证的任何请求，Spring Security 都会放行。

## 自定义用户认证服务

Spring Security 提供了一个 UserDetailsService 接口，通过它可以获取用户信息，而这个接口只有一个 loadUserByUsername 方法需要实现，这个方法定义返回 UserDetails 接口对象

```java
@Service
public class UserDetailsServiceImpl implements UserDetailsService {
    // 注入服务接口
    @Autowired
    private UserRoleService userRoleService = null;

    @Override
    @Transactional
    public UserDetails loadUserByUsername(String userName) throws UsernameNotFoundException {
        // 获取数据库用户信息
        DatabaseUser dbUser = userRoleService.getUserByName(userName);
        // 获取数据库角色信息
        List<DatabaseRole> roleList = userRoleService.findRolesByUserName(userName);
        // 将信息转换为 UserDetails 对象
        return changeToUser(dbUser, roleList);
    }
    private UserDetails changeToUser(DatabaseUser dbUser, List<DatabaseRole> roleList) {
        // 权限列表
        List<GrantedAuthority> authorityList = new ArrayList<>();
        // 赋予查询到的角色
        for (DatabaseRole role : roleList) {
        	GrantedAuthority authority = new SimpleGrantedAuthority(role.getRoleName());
        	authorityList.add(authority);
        }
        // 创建 UserDetails 对象，设置用户名、密码和权限
        UserDetails userDetails = new User(dbUser.getUserName(),dbUser.getPwd(), authorityList); // org.springframework.security.core.userdetails.User
        return userDetails;
    }
}
```

## CSRF 跨域请求伪造

CSRF 攻击的原理是通过在第三方网站中放置对应请求的链接(如交易的请求)，当点击对应的请求时，会携带该请求对应的 Cookie，而服务器无法识别改次请求是来自第三方网站，认为改次请求是用户主动的行为，从而导致用户账户中的钱莫名减少或者增多的情况。

Spring Security 提供了方案来处理 CSRF 过滤器。在默认的情况下，它会启用这个过滤器来防止 CSRF 攻击。对于不关闭 CSRF 的 Spring Security，每次 HTTP 请求的表单（Form）就要求存在 CSRF 参数。当访问表单的时候，Spring Security 就生成 CSRF 参数，放入表单中，这样当提交表单到服务器时，就要求连同 CSRF 参数一并提交到服务器。Spring Security 就会对 CSRF 参数进行判断，判断是否与其生成的保持一致。如果一致，它就不会认为该请求来自 CSRF 攻击；如果 CSRF 参数为空或者与服务器的不一致，它就认为这是一个来自 CSRF 的攻击而拒绝请求。因为这个参数不在 Cookie 中，所以第三方网站是无法伪造的，这样就可避免 CSRF 攻击。

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

## 在配置文件中配置用户名和密码

```properties
#自定义用户名和密码
spring.security.user.name=myuser
spring.security.user.password=123456
```

有了安全配置的属性，即使没有加入 @EnableWebSecurity，Spring Boot 也会根据配置的项自动启动安全机制


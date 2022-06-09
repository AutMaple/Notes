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
# 基本概念

- Authentication Client 用于 keycloak 进行认证和授权，用于获取 Access Token
- Resource Server 用于在 keycloak 中校验网关传递过来的 Access Token
- TokenDelay Filter: 该过滤器的作用是将 Gateway 获取到的 Access Token 传递给受保护的服务，如果想全局配置，则配置 `spring.cloud.gateway.default-filters`，如果只是想传递给指定的服务，则只需要到对应服务的 `filters` 字段中配置该 Filter 即可。

# 1. 网关统一认证和授权

```xml
<dependency>  
    <groupId>org.springframework.cloud</groupId>  
    <artifactId>spring-cloud-starter-gateway</artifactId>  
</dependency>
<dependency>  
    <groupId>org.springframework.cloud</groupId>  
    <artifactId>spring-cloud-starter-bootstrap</artifactId>  
</dependency>
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-oauth2-client</artifactId>  
</dependency>
```

```yaml
spring:
   security:
    oauth2:
      client:
        provider:
          keycloak:
            issuer-uri: http://localhost:10000/realms/ostock
            user-name-attribute: preferred_username

        registration:
          keycloak:
            provider: keycloak
            client-id: ostock
            client-secret: 1ahXcWZqcHtW4wRY6ENCfgEKlpOQCCeZ # 不同机器上的 keycloak，client-secret 也不同
            scope: openid
            authorization-grant-type: authorization_code
	cloud:
		gateway:
			default-filters:
				- TokenRelay # 令牌分发 Filter
```

# 2. 受保护服务作为资源服务器

```xml
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>  
</dependency>
```

```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: http://localhost:10000/realms/ostock
```

## 2.1 从 Token 中提取权限信息

Keycloak Access Token 采用 JWT 标准，要提取 JWT 中的权限信息，需要自定义转换器：

```java
@Configuration  
public class SecurityConfig {  
    @Bean  
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {  
        http.oauth2ResourceServer()  
                .jwt()  
                .jwtAuthenticationConverter(new KeycloakJwtAuthenticationConverter());  
        return http.build();  
    }  
}
```

```java
package com.autmaple.config;  
  
import org.springframework.core.convert.converter.Converter;  
import org.springframework.security.authentication.AbstractAuthenticationToken;  
import org.springframework.security.core.authority.SimpleGrantedAuthority;  
import org.springframework.security.oauth2.jwt.Jwt;  
import org.springframework.security.oauth2.server.resource.authentication.JwtAuthenticationToken;  
  
import java.util.Collection;  
import java.util.List;  
import java.util.Map;  
import java.util.stream.Collectors;  
import java.util.stream.Stream;  
  
public class KeycloakJwtAuthenticationConverter implements Converter<Jwt, AbstractAuthenticationToken> {  
  
    @Override  
    public AbstractAuthenticationToken convert(Jwt jwt) {  
        Map<String, Collection<String>> realmAccess = jwt.getClaim("realm_access");  
        Map<String, Map<String, Collection<String>>> resourceAccess = jwt.getClaim("resource_access");  
        List<String> resourceRoles = resourceAccess.values().stream()  
                .map(Map::values)  
                .flatMap(Collection::stream)  
                .flatMap(Collection::stream)  
                .collect(Collectors.toList());  
        List<String> realmRoles = realmAccess.values().stream()  
                .flatMap(Collection::stream)  
                .collect(Collectors.toList());  
        List<SimpleGrantedAuthority> authorities = Stream.of(resourceRoles, realmRoles)  
                .flatMap(Collection::stream)  
                .distinct()  
                .map(role -> new SimpleGrantedAuthority("ROLE_" + role))  
                .collect(Collectors.toList());  
        return new JwtAuthenticationToken(jwt, authorities);  
    }  
}
```

```ad-warning
title: 注意

重写 convert() 方法时，需要根据 token 中的信息进行提取，不同的 token， 提取方式不一样。
```
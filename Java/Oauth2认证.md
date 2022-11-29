# Oauth2 授权流程

```txt
 +--------+                               +---------------+
 |        |--(A)- Authorization Request ->|   Resource    |
 |        |                               |     Owner     |
 |        |<-(B)-- Authorization Grant ---|               |
 |        |                               +---------------+
 |        |
 |        |                               +---------------+
 |        |--(C)-- Authorization Grant -->| Authorization |
 | Client |                               |     Server    |
 |        |<-(D)----- Access Token -------|               |
 |        |                               +---------------+
 |        |
 |        |                               +---------------+
 |        |--(E)----- Access Token ------>|    Resource   |
 |        |                               |     Server    |
 |        |<-(F)--- Protected Resource ---|               |
 +--------+                               +---------------+
```

1. 用户发起授权请求，在该请求中会携带请求授权成功后需要重定向的地址(如果用户还未在客户端登录，则需要先登录在进行授权)
2. 授权服务器返回一个授权界面，用户需要在授权界面确认授权
3. 当用户点击确认授权按钮时，会向授权服务器发送确认授权的请求，同时请求头中的 Refferer 字段会携带第 1 步中的重定向地址
4. 授权服务器接收到确认授权的请求时，将会响应一个重定向的请求，同时会在重定向的地址中加入一个 Code 字段
5. 应用服务器在接收到重定向的请求后，可以通过请求中携带的 Code 向授权服务器发送请求，获取 AccessToken
5. 获取到 AccessToken 之后，携带 AccessToken 获取资源服务器中的资源。AccessToken 放在请求头的 Authorization 字段中，类型为 Bearer 类型的 Token
5. 资源服务器接收到请求之后会向认证服务器验证 AccessToken 的有效性，即向 `/oauth/check_token` endpoint 发送请求。如果 AccessToken 合法的话，认证服务器会返回用户相关的信息，包括基本信息和权限信息。
5. 资源服务器进行授权操作，判断用户(非第三方应用)是否有权限操作对应的资源
5. 如果有则返回相关的资源给第三方应用

在授权服务器和资源服务器中都需要配置第三方客户端的 client_id 和 secret:

- 授权服务器中的 client_id 和 secret 用于授权，即生成 access_token
- 资源服务器中的 client_id 和 secret 则用于检查 access_token 的有效性

## 授权码和令牌有什么区别？

授权码是用来获取令牌的，使用一次就失效，令牌是用来获取资源的

## Refresh Token

当 Token 快要过期的时候，我们需要获取一个新的 Token，而获取新的 Token 需要有个凭证，这个凭证就是 Refresh Token, 同时 Refresh Token 同样有过期时间。

## Oauth 项目提供的端点

在添加 @EnableAuthorizationServer 注解且应用启动之后，会自动添加以下几个 Endpoint(端点)：

| 端点                  | 说明                                                     |
| --------------------- | -------------------------------------------------------- |
| /oauth/authorize      | 获取授权码的端点                                         |
| /oauth/token          | 获取令牌的端点。该端点既可以获取 Token，也可以刷新 Token |
| /oauth/confirm_access | 用户确认授权提交端点                                     |
| /oauth/check_token    | 资源服务器校验 Access Token 的端点                       |
| /oauth/error          | 授权服务错误信息端点                                     |
| /oauth/token_key      | 使用 JWT Token时，该端点用于提交公钥                     |

### /oauth/token

请求方法：POST

请求参数:

| code          | 认证服务器返回的校验码                 |
| ------------- | -------------------------------------- |
| client_id     | 分配给第三方应用的唯一标识             |
| client_secret | 第三方应用的秘钥                       |
| redirect_uri  | 重定向地址                             |
| grant_type    | 授权类型, 常用的是: authorization_code |

### /oauth/check_token

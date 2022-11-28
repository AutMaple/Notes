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

在授权服务器和资源服务器中都需要配置第三方客户端的 client_id 和 secret:

- 授权服务器中的 client_id 和 secret 用于授权，即生成 access_token
- 资源服务器中的 client_id 和 secret 则用于判断 access_token 的有效性

## 授权码和令牌有什么区别？

授权码是用来获取令牌的，使用一次就失效，令牌是用来获取资源的

## Refresh Token

当 Token 快要过期的时候，我们需要获取一个新的 Token，而获取新的 Token 需要有个凭证，这个凭证就是 Refresh Token, 同时 Refresh Token 同样有过期时间。

## Oauth 项目提供的端点

| 端点                  | 说明                                                     |
| --------------------- | -------------------------------------------------------- |
| /oauth/authorize      | 授权端点                                                 |
| /oauth/token          | 获取令牌的端点。改端点既可以获取 Token，也可以刷新 Token |
| /oauth/confirm_access | 用户确认授权提交的端点                                   |
| /oauth/check_access   | 校验 Access Token 的端点                                 |
| /oauth/error          | 授权出错的端点                                           |
| /oauth/token_key      | 提交公钥的端点                                           |


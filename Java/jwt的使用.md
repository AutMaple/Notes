# jwt 的使用

## jwt 是什么

JWT 是 JSON WEB TOKEN 的缩写，它是基于 RFC 7519 标准定义的一种可以**安全传输**的的 JSON 对象，由于使用了数字签名，所以是可信任和安全的。

JWT 是 JSON 格式的加密字符串，即将 json 数据加密后的字符串。它可包含敏感的数据并对请求的客户端进行验证

## 什么时候使用 jwt?

- **授权**：这是使用 JWT 最常见的场景。JWT 是用于授权的而非用于身份验证
  - 通过**身份验证**，验证用户名和密码的有效性，并根据验证结果决定是否将用户登录到系统中。
  - 通过**授权**，可以验证发送到服务器的请求是否是已登录的用户，从而决定是否授予该用户访问系统的权限，继而批准该用户通过获得的 token 访问路由、服务和资源，其中 token 中含有权限等级。
- **信息交换**：JWT(JSON Web Token) 是在双方之间安全地传输信息的一种好方法。因为 JWT 可以被签名（例如，使用公钥/私钥对），所以能确保请求就是来自客户，而非来在恶意客户（黑客）。此外，由于签名是使用 Header 和 Payload 加密计算得到的，因此还能够验证发送的内容是否被篡改。

## When should you use JSON Web Tokens?

Here are some scenarios where JSON Web Tokens are useful:

- **Authorization**: This is the most common scenario for using JWT. Once the user is logged in, each subsequent request will include the JWT, allowing the user to access routes, services, and resources that are permitted with that token. **SSO(Single Sign On)** is a feature that widely uses JWT nowadays, because of its small overhead and its ability to be easily used across different domains.
- **Information Exchange**: JSON Web Tokens are a good way of securely transmitting information between parties. Because JWTs can be signed—for example, using public/private key pairs—you can be sure the senders are who they say they are. Additionally, as the signature is calculated using the header and the payload, you can also verify that the content hasn't been tampered with.

## JWT 与 Session Id 比较

### 小型 Web 应用程序

#### Session Id 实现

在传统的 Web 应用程序中，我们使用 Session 来授权用户，当用户登录到应用程序后，我们会为该用户分配一个唯一的 Session Id。我们将此 Session Id 保存在用户浏览器的安全 cookie 中和服务器的内存中。我们对每个请求都使用相同的会话，以便服务器知道该用户已通过身份验证。对于每个请求，cookie 中的 Session Id 都会与服务器内存中的 Session Id 作匹配，以验证用户是否被授权。

![Session Id Implementation](https://img2020.cnblogs.com/blog/2074831/202103/2074831-20210330020252363-247446431.png)

#### JWT 实现

在 JWT 实现中，我们使用 JWT 授权用户，当用户登录到应用程序后，就会为每个通过身份验证的用户生成一个唯一的 JWT。我们将该 token 保存在浏览器的 **local storage 或者 cookie 中，而不会在服务器端保存任何内容**。对于每个请求，该 token 都会被发送到服务器进行解密和验证，以核实该用户是否已授权，不管以何种方式篡改了 token 都会被拒绝。

![Implementation with JWT](https://img2020.cnblogs.com/blog/2074831/202103/2074831-20210330020149159-1322327347.png)

这种实现对于小型站点来说很好，因为服务器不再需要存储 Session Id，从而减少了服务器的负载

### 高级 Web 应用程序（多个服务器）

如果我们的应用程序越来越受欢迎，需要我们对其进行扩展，会发生什么呢？

#### Session Id 实现

![Session Id Implementation Multiple Servers](https://img2020.cnblogs.com/blog/2074831/202103/2074831-20210330020325962-677994127.png)

我们需要有一台连接到负载均衡器的新服务器，以便基于流量和可用性在 Web 服务器之间导航流量。这种实现给我们带来了一个新的问题，如下所示：

如果*用户 1* 登录到了*服务器 1*，那么*服务器 1* 已经将 session 保存在其内存中，当*用户 1* 发出另一个请求并且负载均衡器将该请求重定向到了*服务器 2*，而*服务器 2* 没有保存该 session 信息，这时会发生什么情况？

*用户将被认为已退出应用程序并被要求再次登录*，这不是一个好的用户体验。通常，我们解决这个问题的方法是引入缓存：

![Session Id Implementation introducing cache](https://img2020.cnblogs.com/blog/2074831/202103/2074831-20210330020357842-6567073.png)

现在，所有的 Session 也将同时保存在缓存中，因此任何一台服务器都可以检查该 Session 是否存在，并可以利用它来验证用户并授予他们对应用程序的访问权限。

尽管缓存解决了我们的问题，但是在生产环境中，这种解决方案有着昂贵的成本：

- 需要大量的 RAM、CPU、存储来跟踪所有这些会话和平稳地处理请求
- 需要维护缓存，以确保没有幽灵会话或无效会话
- 万一某台服务器崩溃，所有未与缓存同步的会话都会丢失
- 使用户无效更复杂
- 托管成本高

#### JWT 实现

让我们来看看如何通过 JWT 实现来处理相同的情况。

不同于在 Cookie 中使用 Session Id 与服务器内存中的 Session 作匹配；我们可以使用 JWT 来代替它。此时，当用户登录到我们的应用程序时，服务器将不会生成 Session Id 并将其保存在内存中，而是会创建一个 JWT token，并对其进行编码和序列化，然后使用自己的加密机制对其进行签名。通过这种方式，服务将知道一旦对它做了变更或篡改，便将其变为无效。由于通过服务器的加密机制对其进行了签名，所以这是可以被检验的。

![Implementation with JWT Multiple Servers](https://img2020.cnblogs.com/blog/2074831/202103/2074831-20210330020438448-1268213585.png)

使用 JWT 可以更容易地管理可伸缩性，因为我们不需要服务器来处理任何会话检查或缓存检查。请求可以转发到负载均衡器为其分配的任一服务器，而无需担心会话的可用性。万一某台服务器宕机，所有的 token 将仍然有效，因为所有服务器上的加密机制是一样的。

### JWT 和 Session Id 的区别总结

让我们来快速总结一下 JWT 和 Session Id 的区别

#### JWT

- 服务器上不保存任何东西，JWT 存储于客户端中
- 由服务器加密和签名
- token 包含用户的所有信息
- 所有信息都存储于 token 本身中
- 易于缩放

![quick JWT summary](https://img2020.cnblogs.com/blog/2074831/202103/2074831-20210330020517987-1336836798.png)

#### Session Id

- Session Id 保存于服务器和客户端中
- 加密并签名
- Session Id 是对用户的引用
- 服务器需要查找用户并进行必要的检查
- 难以缩放

![quick Session Id summary](https://img2020.cnblogs.com/blog/2074831/202103/2074831-20210330020550544-1890643182.png)

## JWT 结构

JSON Web Token 由三部分组成，以点（.）分隔，分别是：

- Header（标头）
- Payload（有效负载）
- Signature（签名）

因此，JWT 通常如下所示：

```plain
xxxxxx.yyyyyyy.zzzzzzzz
```

这种分隔使从视觉上更容易看出 token 的不同部分。让我们来分解一下它的不同的部分。

### Header

Header 通常由两部分组成：

- token 的类型，这里是 JWT
- 使用的签名算法，比如 HMAC、SHA256 或 RSA。

例如：

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

然后，将此 JSON 以 **Base64Url** 编码，形成 JWT 的第一部分。Base64Url 算法不是加密算法

### Payload (Data)

token 的第二部分是有效负载，其中包含 Claims（声明）。**Claims 是有关实体（通常是用户）和其他数据的声明**。有三种类型的 Claims：registered、public 和 private claims。

- **Registered Claims**：这是一组非强制性的但建议使用的预定义 Claims，以提供一组有用的、可互操作的 Claims。其中包含：
  - iss (Issuer)：签发人
  - sub (Subject)：主题
  - aud (Audience)：受众
  - exp (Expiration Time)：过期时间
  - nbf (Not Before)：生效时间
  - iat (Issued At)：签发时间
  - jti (JWT ID)：编号
- **Public Claims**：这些可以由使用 JWT 的人员随意定义。但是为了避免冲突，应该在 [IANA JSON Web Token Registry](https://www.iana.org/assignments/jwt/jwt.xhtml) 中定义它们，或者将其定义为包含抗冲突命名空间的 URI。
- **Private Claims**：这些是自定义声明，是为了在同意使用它们的双方共享信息而创建的，它们既不是注册的声明，也不是公共的声明。

举一个有效负载的例子：

```json
{
  "sub": "221122112",
  "name": "Mohamd Lawand",
  "admin": true,
  "exp": 15323232,
  "iat": 14567766 // 该 token 的签发时间
}
```

然后，对有效负载进行 **Base64Url** 编码，形成 JSON Web Token 的第二部分。

> 除非将其加密，否则请不要将机密信息放入 JWT 的 Payload 或 Header 元素中。

### Payload

The second part of the token is the payload, which contains the claims. Claims are statements about an entity (typically, the user) and additional data. There are three types of claims: *registered*, *public*, and *private* claims.

- [**Registered claims**](https://tools.ietf.org/html/rfc7519#section-4.1): These are a set of predefined claims which are not mandatory but recommended, to provide a set of useful, interoperable claims. Some of them are: **iss** (issuer), **exp** (expiration time), **sub** (subject), **aud** (audience), and [others](https://tools.ietf.org/html/rfc7519#section-4.1).

  > Notice that the claim names are only three characters long as JWT is meant to be compact.

- [**Public claims**](https://tools.ietf.org/html/rfc7519#section-4.2): These can be defined at will by those using JWTs. But to avoid collisions they should be defined in the [IANA JSON Web Token Registry](https://www.iana.org/assignments/jwt/jwt.xhtml) or be defined as a URI that contains a collision resistant namespace.

- [**Private claims**](https://tools.ietf.org/html/rfc7519#section-4.3): These are the custom claims created to share information between parties that agree on using them and are neither *registered* or *public* claims.

An example payload could be:

```
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

The payload is then **Base64Url** encoded to form the second part of the JSON Web Token.

### Signature签名

签名使我们能够验证 token 是否有效和没被篡改。它的工作方式是获取 token 的前两部分，将 Header 和 Payload 分别编码为 Base64，然后将它们用 “.” 连接起来。这样我们就拥有了与用户共享的所有数据。

然后，获取在第一部分（Header）中提供的算法并应用于上面的连接结果。如果前两部分的哈希结果与 token 的第三部分匹配，则表示此 JWT 是有效的；如果不匹配，则表示此 token 被修改过，是无效的。

```java
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret)
```

这种方案的唯一威胁是密钥在服务器以外的任何地方都可用。但是，如果我们保护好密钥的安全，就没有什么能损害这一过程。

签名被用于验证消息在传输过程中没有被篡改，而且，当 token 是使用私钥签名时，它还可以验证 JWT 发送方的真实身份。

它的工作原理与密码哈希非常相似——我们将两部分组合在一起，并且使用特定的算法进行单向哈希，然后我们比较哈希的结果看它们是否有效。

最后发送给用户的数据是：header.payload.signature

**其中 signature 使用 header 中的算法进行不可逆加密，header 和 payload 则使用 Base64Url 编码进行编码便于在网络中传输，由于使用的是 Base64Url 编码，因此不能够在 header 和 payload 中存放像密码这样的敏感数据** 

### Signature

**To create the signature part you have to take the encoded header, the encoded payload, a secret, the algorithm specified in the header, and sign that.**

For example if you want to use the HMAC SHA256 algorithm, the signature will be created in the following way:

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

The signature is used to verify the message wasn't changed along the way, and, in the case of tokens signed with a private key, it can also verify that the sender of the JWT is who it says it is.

#### 签名方式

现在，让我们来看一下对 JWT 进行签名的方式：

- 一个 secret（使用 HMAC 算法）
- 一个 公钥/私钥对（使用 RSA 或 ECDSA）

签名的 token 可以验证其中包含的 Claims 的完整性，而加密的 token 则可以向其他方隐藏这些 Claims。

## jwt常用的加密算法

| 算法                | 描述 |
| ------------------- | ---- |
| HS256(HMAC-SHA256)  |      |
| RS256(RSA-SHA256)   |      |
| ES256(ECDSA-SHA256) |      |

## jwt 的工作原理

服务端收到客户端传过来的 token 之后，会对 token 以 `.` 进行分隔，获得`Header`、`Payload`、`Signature`三部分，再解码 `Header` 获得其中的签名算法`alg`，然后使用`Header`、`Payload`和`secret`生成新的签名，最后比较新的签名和原始签名是否一致，若不一致则表示token无效。若用户篡改了token的`Payload`信息，则生成的新的签名和 token 中包含的原始签名肯定不一致，也就无法通过认证。

可以看到，认证过程最终比较的是签名，由于生成签名使用的算法是不可逆的，且用户不知道`secret`，所以无法篡改签名。但如果用户知道了服务端存储的`secret`，就可以任意更改token了，这就相当于用户自己给自己签名了，因此一定要注意不能泄露`secret`。

## JWT 如何使用

用户登录成功后，服务端会返回一个 token，用户可以将这个token存储在 Cookie 或 localStorage。每当用户与服务端通信，访问需要授权的资源时，都要传递这个 token，传递方式一般是以 `Authorization` 字段放在 HTTP 请求头中，并带上 Bearer 标注：

```
Authorization: Bearer <token> 
```

当然也可以直接在 URL 中以参数的形式传递。

```
http://bazingafeng.com/?token=xxx
```

**注意：不建议将 token 以 Cookie 的形式传递给服务端，因为这会存在跨域问题，也可能会有CSRF攻击的风险，而放在请求头中就不会有这样的问题。**

## 使用 Jwt 的注意事项

- 不要在 Payload 中存储敏感信息
- 不要泄露 secret
- 尽量使用 HTTPS 因为 token 一旦被其他人获取，则他们可以冒充我们向服务器发起任意请求了(CSRF)。比如在 HTTP 请求中，我们发送 token 给服务端时，该 token 可能会被人抓包获取。针对这个问题，也没有什么解决办法，一般建议使用 HTTPS 而非 HTTP。虽然这样不能保证 token 一定不会被人获取，但可以更加安全。

# Max-Age 和 Expires

Max-Age 和 Expires 都可以设置 Cookie 的 TTL(Time To Live)

## Max-Age

Mage-Age 可以为正数、负数和 0：

- 当值为正数时，表示 Cookie 从创建开始算起，Cookie 的过期时间，单位是秒。这种类型的 Cookie 会以 Cookie 文件的形式被持久化到计算机中，即使关闭浏览器，只要没有过期，就一直有效。
- 当值为负数时，表示 Cookie 为一个 Session Cookie，Cookie 的值会被保存在内存中，当浏览器关闭时，Cookie 也就失效了。Max-Age 的默认值为 -1
- 当值为 0 时，表示删除此 Cookie

## Max-Age 和 Expires 的区别和联系

- Expires 设置 Cookie 的过期日期
- Max-Age 设置 Cookie 的过期秒数
- IE 浏览器(ie6、ie7 和 ie8) 不支持 `max-age`，所有的浏览器都支持 `expires`

如果 Cookie 中即没有设置 Max-Age，也没有设置 Expires，则 Cookie 默认为一个 Session Cookie，即浏览器关闭后，Cookie 失效。

# Session

## Session 什么时候被创建？

在 Java 中，Session 不是一打开网站就会立即建立，它的建立只需要满足下面的条件中的任何一条就可建立：

1. 在 Servlet 中手动调用

   ```java
   HttpSession session = request.getSession();
   ```

   或者

   ```java
   HttpSession session = request.getSession(true);
   ```

2. Jsp 中没有写 `<%@ page session="false" %>`

   Jsp 中默认为 `<%@ page session="true" %>`，即默认会创建 Session

   `<%@ page session="true" %>` 在被解析后会变成：`session = pageContext.getSession()`, 因此默认会帮我们创建 Session。

如果上述两个条件都不满足，则不会为请求创建 Session

通过上面的论述可以知道，因为 Jsp 的本质还是一个 java 文件，因此如果我们只访问 HTML、IMAGE 等静态资源并不会创建 Session。

## Session 失效的时间

在 Tomcat 中，Session 默认的失效时间为 20 分钟。服务器为请求建立 Session 之后，会将 SessionId   放入 Cookie 中，并且 Cookie 的 Max-Age 通常为 -1，即默认为当前浏览器有效，关闭浏览器之后 Session 失效。在 Java 返回的响应中，SessionId 保存在 Cookie 中 key 为 JSESSIONID 的键值对中。

## 浏览器禁用 Cookie 怎么办？

绝大多数的手机浏览器都不支持 Cookie, 解决方案就是 URL 地址重写。

URL 地址重写是对客户端不支持 Cookie 的解决方案。URL 地址重写的原理是将 Session Id 写入到 URL 中，服务端能够解析 URL 中携带的 Session Id，这样即使客户端不支持 Cookie，也可以使用 Session Id 来追踪用户的状态。`HttpServletResponse` 类提供了 `encodeUrl(String url)` 方法来实现 URL 地址重写。该方法会自动判断客户端是否支持 Cookie, 如果客户端支持 Cookie, 就会将 URL 原封不动的输出；如果客户端不支持 Cookie, 就会将 Session Id 写入到 URL 中，然后返回新的 URL。

**注意**：Tomcat 判断客户端浏览器是否支持 Cookie 的依据是请求中是否含有 Cookie。尽管客户端可能会支持Cookie，但是由于第一次请求时不会携带任何 Cookie（因为没有任何 Cookie 可以携带），重写后的 URL 地址中仍然会带有 JSESSIONID。当第二次访问时，服务器已经在浏览器中写入了 Cookie ，因此重写后的 URL 地址中就不会带有 JSESSIONID 了。

## 判断 Session 过期

```java
if(request.getSession(false) == null) {
    System.out.println("Session 已经过期了!");
}
```


# Tomcat 是什么

Apache Tomcat 是 Java Servlet、JavaServer Pages(JSP)、Java 表达式语言和 Java 的 WebSocket 技术的一个开源实现 ,通常我们将 Tomcat 称为 Web 容器或者 Servlet 容器。

# JavaEE协议

servlet 协议是 JavaEE 协议的一部分。JavaEE 协议即进行 JavaWeb 开发的一套API，除了包含 Servlet 协议外，还包含 JSP 协议，JSTL 协议，JPA 协议等。目前使用较多的版本是 JavaEE5.0.

JavaEE5.0 包含的是 Servlet2.5 协议。JavaEE6.0 包含的是 Servlet3.0 协议。而 Servlet2.5 与 3.0 版本很大的区别就是，Servlet3.0 支持对 Servlet.Filter, Listener 类的注解式开发。

Apache 的 Tomcat,只是 JavaEE 规范中的 Servlet/JSP 规范的实现者，所以其实一个轻量级服务器，它是开源免费的。Tomcat也成为 Web 容器，或 Servlet 容器，但不能称为 JavaEE 容器。

## Tomcat 请求处理流程

![image-20221018165334294](../Attachment/image-20221018165334294.png)

## 长连接处理

Tomcat 默认的线程池是核心线程数是10，最大线程数为 100，同时，如果有 75% 的 HTTP 请求都是是长连接(BIO 模型下一个线程对应一个 Socket)，那么超过这个比例的 HTTP 请求都会变成短连接，即使发送的 HTTP 的请求头中希望保持长连接，同样还是会当作短连接来处理

## 浏览器默认的并发数

HTTP 客户端一般对同一个服务器的并发连接个数都是有限制的。实际上，浏览器确实使用并行连接，但它们将并行连接的总数限制为少量,服务器可以自由地关闭来自特定客户端的过多连接。

大部分的浏览器默认的最大可同时向服务器发起请求个数为 4 或者是 6，取决于浏览器的版本，高版本的通常是 6 个并发请求

## Http 分块传输

如果需要使用分块传输,需要设置 Transfer-Encoding: chunked 请求头，对于请求体中的数据格式为：

```text
<length>\r\n # length 表示当前字块的数据长度
<content>\r\n # 当前子块的内容
....
0\r\m # 表示结束分块传输
\r\n
```

使用示例：

```text
2\r\n
hi
0\r\n
\r\n
```

上述示例表示请求中请求体的内容是：hi


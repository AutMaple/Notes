# 谈谈你对 MVC 的理解

MVC 是一种设计模式,在这种模式下软件被分为三层,即 Model（模型）、View（视图）、Controller（控制器）。Model 代表的是数据,View  代表的是用户界面,Controller代表的是数据的处理逻辑,它是 Model 和 View 这两层的桥梁。将软件分层的好处是,可以将对象之间的耦合度降低,便于代码的维护。

## Model

**指从现实世界中抽象出来的对象模型(即一张表代表一个对象)**,是应用逻辑的反应；它封装了数据和对数据的操作,是实际进行数据处理的地方（**模型层与数据库才有交互**）。在 MVC 的三个部件中,模型拥有最多的处理任务。被模型返回的数据是中立的,模型与数据格式无关,这样一个模型能为多个视图提供数据,由于应用于模型的代码只需写一次就可以被多个视图重用,所以减少了代码的重复性。 

## View

负责进行模型的展示,一般就是我们见到的用户界面。

## Controller

控制器负责视图和模型之间的交互,控制对用户输入的响应、响应方式和流程；它主要负责两方面的动作,一是把用户的请求分发到相应的模型,二是把模型的改变及时地反映到视图上。 

## 扩展

为了解耦以及提升代码的可维护性,服务端开发一般会对代码进行分层,服务端代码一般会分为三层：表现层、业务层、数据访问层。在浏览器访问服务器时,请求会先到达表现层最典型的 MVC 就是jsp+servlet+javabean 模式。 以 JavaBean 作为模型,既可以作为数据模型来封装业务数据,又可以作为业务逻辑模型来包含应用的业务操作。 JSP 作为视图层,负责提供页面为用户展示数据,提供相应的表单（Form）来用于用户的请求,并在适当的时候（点击按钮）向控制器发出请求来请求模型进行更新。 Serlvet 作为控制器,用来接收用户提交的请求,然后获取请求中的数据,将之转换为业务模型需要的数据模型,然后调用业务模型相应的业务方法进行更新,同时根据业务执行结果来选择要返回的视图。 当然,这种方式现在已经不那么流行了,Spring MVC 框架已经成为了 MVC 模式的最主流实现。 Spring MVC 框架是基于 Java 的实现了 MVC 框架模式的**请求驱动类型的轻量级框架**。前端控制器是 DispatcherServlet 接口实现类,映射处理器是 HandlerMapping 接口实现类,视图解析器是 ViewResolver 接口实现类,页面控制器是 Controller 接口实现类

# 什么是 SpringMVC

SpringMVC 是使用 Java 语言实现 Web MVC 设计模式的一个轻量级 Web 框架, 该框架是一种请求驱动类型的框架

- 使用了 MVC 架构模式的思想，将 web 层进行职责解耦
- 基于请求驱动指的就是使用请求-响应模型

# SpringMVC 的优点是什么

1. 简化 web 层的开发
2. 天生于 Spring 框架集成
3. 约定大于配置的契约式编程
4. 支持灵活的 URL 到页面控制器到映射,支持 RESTful 风格
5. 非常灵活的数据验证、数据格式化和数据绑定机制，能使用任何对象与数据进行绑定，不必实现特定框架的 API
6. 更加简单的异常处理

# SpringMVC 中的组件有哪些？功能分别是什么

## DispatcherServlet(前端控制器)

DispatcherServlet 是 SpringMVC 的控制中心，用于协调其他组件有序工作，降低了各组件之间的耦合性

## HandlerMapping(处理器映射器)

HanlderMapping 的作用就是根据用户的请求找到对应的 Handler，即我们在项目中编写的 Controller。SpringMVC 根据 Handler 实现方式的不同，提供不同的 HandlerMapping 实现。例如：使用配置文件实现映射的方式，通过实现接口的映射方式，使用注解的方式实现映射

## Handler

Handler 即项目中编写的 Controller，项目的业务逻辑在这里编写

## HandlerAdpter

使用 HandlerAdpter 调用 Handler 进行业务处理。SpringMVC 使用适配器模式进行业务处理可以应付更多的业务场景，自定义更强。

## ViewResolver

负责将 Handler 处理的业务结果生成 View 视图，ViewResolver 首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成 View 视图对象，最后对 View 进行渲染并将处理结果通过页面展示给用户。

# SpringMVC 的跳转方式

1. forward：服务器内部跳转，浏览器请求的地址不会改变。且可以通过 request 作用域进行参数的传递
2. redirect: 重定向，由浏览器重新请求服务器指定的另一个 URL 地址，此时浏览器的地址会发生改变。跳转时不能够在请求间传递参数

如果要使用 redirect 的方式需要使用 redirect 关键字

```java
@Controller
@RequestMapping("/hello")
public class HelloController{
    
    @RequestMapping("test")
    public String hello(){
        System.out.println("Hello World");
        return "redirect: /hello/world";
    }
    
    @RequestMapping("world")
    public String world(){
        System.out.println("Hi!, I'm testing redirection");
        return "xxxx";
    }
}
```

如果要使用 forward 的方式需要使用 forward 关键字, 使用方式和 redirect 类似

# SpringMVC 参数的传递

SpringMVC 可以对请求中传递的参数进行自动赋值，前提条件是处理请求的方法中的参数名要和请求中传递的参数名一致并且数据类型是匹配的。其中日期时间 Date 的赋值无法自动完成, 需要相应的配置才可以

## SpringMVC 参数中文乱码的问题

### Get 请求

主要是 Tomcat8.x 版本之前：默认使用 server.xml 中的 `URIEncoding="ISO-8859-1"`编码，而不是 "UTF-8" 编码，进而会出现中文乱码。

Tomcat8.x 版本之后：默认使用 server.xml 中的 `URIEncoding="UTF-8"`，所以不会出现中文乱码问题。

### Post 请求

SpringMVC 中默认没有对 POST 请求进行任何编码处理，所以无论什么版本直接接收 POST 请求都会出现中文乱码。

## 解决方式: 定义过滤器

### 自定义过滤器

```java
package com.filter;

import javax.servlet.*;
import java.io.IOException;

//自定义编码filter
public class CharacterEncodingFilter  implements Filter {

    private String encoding;
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        this.encoding = filterConfig.getInitParameter("encoding");
        System.out.println(encoding);
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        request.setCharacterEncoding(encoding);
        response.setCharacterEncoding(encoding);
        chain.doFilter(request,response);
    }

    @Override
    public void destroy() {

    }
}
```

```xml
<!--配置post请求方式中文乱码的Filter-->
<filter>
    <filter-name>charset</filter-name>
    <filter-class>com.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
</filter>

<filter-mapping>
    <filter-name>charset</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

### 使用 CharacterEncodingFilter 解决乱码问题

CharacterEncodingFilter 已经实现好了，只需要注册即可

```xml
  <!--配置post请求方式中文乱码的Filter-->
  <filter>
    <filter-name>charset</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>

  <filter-mapping>
    <filter-name>charset</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>

```


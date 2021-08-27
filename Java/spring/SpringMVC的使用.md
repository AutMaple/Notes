# SpringMVC 的使用

## SpringMVC 介绍

SpringMVC：是基于 spring 的一个框架， 实际上就是 spring 的一个模块， 专门是做 web 开发的。可以理解成是 servlet 的一个升级

web开发底层是 servlet ， 框架是在 servlet 基础上面加入一些功能，让我们做 web 开发更方便。

SpringMVC就是一个 Spring。 Spring 是容器，ioc 能够管理对象，使用 `<bean>`, @Component, @Repository, @Service, @Controller，SpringMVC能够创建对象， 并放入到容器中（SpringMVC容器）， SpringMVC 容器中放的是控制器对象。

我们要做的是使用@Contorller 创建控制器对象， 把对象放入到 SpringMVC 容器中， 把创建的对象作为控制器使用。这个控制器对象能接收用户的请求， 显示处理结果，就当做是一个 Servlet 使用。

 使用@Controller注解创建的是一个普通类的对象， 不是Servlet。 SpringMVC 赋予了控制器对象一些额外的功能。web开发底层是 servlet， SpringMVC中有一个对象是Servlet ： DispatherServlet (中央调度器)。DispatherServlet 负责接收用户的所有请求。 用户把请求给 DispatherServlet， 之后 DispatherServlet 把请求转发给我们的 Controller 对象， 最后是 Controller 对象处理请求。

## springmvc执行过程源代码分析

1. tomcat启动，创建容器的过程
   通过 web.xml 中的 `<load-on-start>` 标签指定的 1，创建 DisaptcherServlet 对象， 
   DisaptcherServlet 它的父类是继承HttpServlet 的， 它是一个 serlvet， 因此在被创建时，会执行 init() 方法。
   在 init() 方法中,会执行如下语句

   ```java
   //创建容器，读取配置文件
   WebApplicationContext ctx = new ClassPathXmlApplicationContext("springmvc.xml");
   //把容器对象放入到ServletContext中
   getServletContext().setAttribute(key, ctx);
   ```

   上面创建容器作用： 创建@controller注解所在的类的对象， 创建 MyController 对象，这个对象放入到 SpringMVC 的容器中， 容器是一个 map ， 类似 `map.put("myController",MyController对象)`

2. 请求的处理过程
   执行 servlet 的service()

   ```java
   protected void service(HttpServletRequest request, HttpServletResponse response)
   
   protected void doService(HttpServletRequest request, HttpServletResponse response)
       
   DispatcherServlet.doDispatch(request, response){
         调用MyController的.doSome()方法
     }
   ```

   doDispatch：是 SpringMVC 中 DispatcherServlet 的核心方法， 所有的请求都在这个方法中完成的。

## 配置

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>springMVC</servlet-name>
        <!--
 			
            所有的浏览器的请求都会经过DispatcherServlet
            DispatcherServlet 会将请求转发给对应的 Controller
            经 Controller 处理之后，交给 DispatcherServlet,最后交给浏览器
           浏览器发送请求 <===> DispatcherServlet ===> Controller ===> DispatcherServlet ===> 浏览器
        -->
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        
        <!-- 自定义springMVC配置文件的位置 -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springMVC.xml</param-value>
        </init-param>
        <!-- 服务器一启动就创建 DispatcherServlet 实例 -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springMVC</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

springMVC.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 扫描包下对应的 bean -->
    <context:component-scan base-package="com.autmaple.controller"/>

    <!--
        在 WEB 容器启动的时候会在上下文中定义一个 DefaultServletHttpRequestHandler，它会对DispatcherServlet的请求进行处理
        如果该请求已经作了映射，那么会接着交给后台对应的处理程序，如果没有作映射，就交给 WEB 应用服务器默认的 Servlet 处理
        从而找到对应的静态资源，只有在找不到资源时才会报错。
    -->
    <mvc:default-servlet-handler/>
    
    
 	<!-- 
		注解驱动实现的功能是完成 java 对象到 json ，xml ，text ， 二进制等数据格式的转换
 		HttpMessageConverter接口：消息转换器
		功能：定义了 java 转换成 json ，xml 等数据格式的方法。这个接口有很多的实现类
			 这些实现类是实现了 java 对象到 json ，java 对象到 xml， java 对象到二进制数据的在
	-->
    <mvc:annotation-driven/>



    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/templates/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```

HelloController.java



```java
@Controller
public class HelloController {
    @RequestMapping("/")
    public ModelAndView index(){
        ModelAndView mv = new ModelAndView();
        mv.addObject("name", "AutMaple");
        mv.setViewName("hello");
        return mv;
    }
}
```

`@RequestMapping`注解的作用是将一个请求路径和一个方法进行绑定。绑定的方法必须返回一个 ModelAndView 对象。在处理对应的请求时，可以设置该请求视图名称，比如 `setViewName("hello")`, 该代码表示为对应的请求返回 `/WEB-INF/templates/hello.jsp`对应的页面，类似于 Servlet 的 doGet, doPost 方法

Model 表示要显示给用户的数据，View 表示视图，比如 jsp 页面。可以通过 ModelAndView 对象的 addObject()方法 传递数据给对应的视图，然后在视图中进行使用

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<h1>Hello ${name}</h1>
</body>
</html>
```

如果将 `@RequestMapping`注解放在类上面，那么该类中各方法对应的`@RequestMapping`的路径在访问时需要加上类上`@RequestMapping`注解上的路径才可以访问

```java
@Controller
RequestMapping("/user")
public class HelloController {
    @RequestMapping("/maple")
    public ModelAndView index(){
        ModelAndView mv = new ModelAndView();
        mv.addObject("name", "AutMaple");
        mv.setViewName("hello");
        return mv;
    }
}
```

如果想要让 index 方法处理对应的请求，那么请求路径就得是`/user/maple` 才可以

## 路径处理方法的返回值

要使用 @ResponsBody 注解，需要在spring 的配置文件中配上注解驱动标签 `<mvc:annotation-driven/>`

当路径处理方法返回对象 (Object) 时，springMVC 默认使用 jackson 处理 json 格式的数据。因此需要引入 jackson 的 jar 包

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.12.3</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.12.3</version>
</dependency>
```

没有加入注解驱动标签`<mvc:annotation-driven/>`时的状态

```java
org.springframework.http.converter.ByteArrayHttpMessageConverter 
org.springframework.http.converter.StringHttpMessageConverter
org.springframework.http.converter.xml.SourceHttpMessageConverter
org.springframework.http.converter.support.AllEncompassingFormHttpMessageConverter
```

加入注解驱动标签`<mvc:annotation-driven/>`时的状态

```java
org.springframework.http.converter.ByteArrayHttpMessageConverter
org.springframework.http.converter.StringHttpMessageConverter <=== 纯文本数据使用的转换器
org.springframework.http.converter.ResourceHttpMessageConverter
org.springframework.http.converter.ResourceRegionHttpMessageConverter
org.springframework.http.converter.xml.SourceHttpMessageConverter 
org.springframework.http.converter.support.AllEncompassingFormHttpMessageConverter 
org.springframework.http.converter.xml.Jaxb2RootElementHttpMessageConverter
org.springframework.http.converter.json.MappingJackson2HttpMessageConverter <=== json 数据使用的转换器，使用 jackson 作为转换器
```

### @ResponseBody注解

位置：路径处理方法上

作用：把路径处理方法返回的对象转换成 json 格式的数据之后，通过 HttpServletResponse 输出给浏览器

```java
@RequestMapping("/json")
@ResponseBody
public User json(){
    User user = new User();
    user.setAge(22);
    user.setUsername("AutMaple");
    return user;
}
```

如果返回的是一个 List 集合，那么返回的数据就是一个 json 数组。

看路径处理方法是否加上 @ResponseBody 注解。如果加上 @ResponseBody 注解，那么处理方法返回的是 String 类型时，那么浏览器接收的数据就是纯文本数据，不是 json 格式的数据也不是对应的 String 的 view 视图。在返回纯文本格式的数据时，中文返回会乱码，此时需要配置路径处理方法上的 @RequestMapping

```java
@RequestMapping(value=".....", produces = "text/palin;charset=utf-8")
```

## 路径处理方法中的参数

`@RequestMapping` 标注的方法中，可以传递 `HttpServletRequest` 、`HttpServletResponse` 、`HttpSession`、请求的参数。

 `HttpServletRequest` 、`HttpServletResponse` 、`HttpSession`

```java
@Controller
public class HelloController {
    @RequestMapping("/")
    public ModelAndView index(HttpServletRequest req, HttpServletResponse res, HttpSession session){
        ModelAndView mv = new ModelAndView();
        mv.addObject("name", "AutMaple");
        mv.setViewName("hello");
        return mv;
    }
}
```

请求的参数

请求的参数可以一个一个的接收，也可以直接接收一个对象。一个一个的接收要求方法中的形参名要和请求中的参数名要一致, 并且 springMVC 会进行类型转换。因此如果参数自动转换成基本数据类型失败会报错，此时，可以将基本数据类型转换成对应的包装类型，这样如果转换失败，对应路径的处理方法将不会执行

```java
@RequestMapping("/login")
public ModelAndView login(String username, int age){
    ModelAndView mv = new ModelAndView();
    mv.addObject("username", username);
    mv.addObject("age", age);
    mv.setViewName("user");
    return mv;
}
```

login.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="login">
    <input type="text" name="username">
    <input type="text" name="age">
    <button type="submit">submit</button>
</form>
</body>
</html>
```

## 乱码问题

使用 get 请求提交参数不会出现问题，但是使用 post 方法提交参数时，会出现乱码问题

解决方法：配置 filter

可以在 web.xml 中配置 filter

```xml
 <filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>

    <!--设置项目中使用的字符编码-->
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceRequestEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
    <init-param>
        <param-name>forceResponseEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <!-- 所有的请求都先经过过滤器 -->
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

## @RequestParam

value: 请求中的参数名

required: 一个 boolean 值，默认是 true。设置为 true 表示i请求中必须含有此参数

被 @RequestParam 修饰的变量名可以不与请求中的参数名相同

```java
@RequestMapping("/login")
public ModelAndView login(@RequestParam("username")String name,
                          @RequestParam("age") int age){
    ModelAndView mv = new ModelAndView();
    mv.addObject("username", name);
    mv.addObject("age", age);
    mv.setViewName("user");
    return mv;
}
```

## 传递一个对象

如果处理方法中的参数是一个对象，那么 spring 会将请求中的参数封装成对应而对象，此时要求对象的字段名和请求参数名要相同

```java
@RequestMapping("/login")
public ModelAndView login(User user){
    ModelAndView mv = new ModelAndView();
    mv.addObject("username", user.getUsername());
    mv.addObject("age", user.getAge());
    mv.setViewName("user");
    return mv;
}
```



